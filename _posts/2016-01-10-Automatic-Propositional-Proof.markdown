---
layout: post
title:  "Automatic Propositional Proof in C#"
date:   2016-01-10 23:00:00
categories: Personal
comments: false
---

命题逻辑自动证明机
主要实现的步骤如下，其实都是比较简单的逻辑实现：
1. 命题逻辑语句中和计算机逻辑运算等价部分的替换；
2. 命题逻辑规则中比较特殊的规则，转换成计算机可识别的逻辑运算；
3. 将处理完成的命题等价字符串动态拼接进C#源代码；
4. 调用C#编译器接口动态编译源代码，调用程序运算并得出结果；
 <!--more-->

``` C#
using Microsoft.CSharp;
using System;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;

namespace DynamicCompile
{
    public class DynamicCompiler
    {

        public static void Invoke(string Proposition)
        {
            #region 编译准备：语句+编译参数+编译语言
            // 1.命题逻辑语句预处理
            string sourceCode = GenerateCode(Proposition);

            // 2.CSharpCodePrivoder对象
            CSharpCodeProvider objCSharpCodePrivoder = new CSharpCodeProvider();

            // 3.CompilerParameters参数
            CompilerParameters objCompilerParameters = new CompilerParameters();
            objCompilerParameters.ReferencedAssemblies.Add("System.dll");//引用系统dll
            objCompilerParameters.GenerateExecutable = false;
            objCompilerParameters.GenerateInMemory = true;

            // 4.CompilerResults,编译
            CodeDomProvider kk = CSharpCodeProvider.CreateProvider("CSharp");
            CompilerResults cr = kk.CompileAssemblyFromSource(objCompilerParameters, new string[] { sourceCode });
            #endregion

            #region 动态编译源+执行代码
            if (cr.Errors.HasErrors)
            {
                Console.WriteLine("编译错误：");
                foreach (CompilerError err in cr.Errors)
                {
                    Console.WriteLine(err.ErrorText);
                }
            }
            else
            {
                // 通过反射，调用HelloWorld的实例
                Assembly objAssembly = cr.CompiledAssembly;
                object objHelloWorld = objAssembly.CreateInstance("DynamicCodeGenerate.HelloWorld");
                MethodInfo objMI = objHelloWorld.GetType().GetMethod("OutPut");
                // 调用执行
                Console.WriteLine(objMI.Invoke(objHelloWorld, null));
            }
            Console.ReadLine();
            #endregion
        }

		//动态生成代码串
        public static string GenerateCode( string Proposition)
        {

            #region 命题逻辑系统语句处理
            List<string> listAtomP=new List<string>();
            string strCode = Manager(Proposition);
            if (strCode.Contains("不是"))
            {
                strCode = "false";
            }
            else {
                listAtomP = DefineBool(strCode);
            }          
            #endregion

            #region 动态拼接程序源代码

            StringBuilder sb = new StringBuilder();
            sb.Append("using System;");
            sb.Append(Environment.NewLine);
            sb.Append("namespace DynamicCodeGenerate");
            sb.Append(Environment.NewLine);
            sb.Append("{");
            sb.Append(Environment.NewLine);

            #region 其他源码传入程序测试
            //sb.Append(Proposition);
            #endregion

            #region hello,world逻辑语句验证
            sb.Append("     public class HelloWorld");
            sb.Append(Environment.NewLine);
            sb.Append("     {");
            sb.Append(Environment.NewLine);
            sb.Append("         public string OutPut()");
            sb.Append(Environment.NewLine);
            sb.Append("         {");
            sb.Append(Environment.NewLine);
            for (int i = 0; i < listAtomP.Count; i++)
            {
                sb.Append(" bool " + listAtomP[i] + "=true;");
            }
            sb.Append("  if(" + strCode + ")");
            sb.Append(Environment.NewLine);
            sb.Append("              {return \"该命题成立！\";}");
            sb.Append(Environment.NewLine);
            sb.Append("              else");
            sb.Append(Environment.NewLine);
            sb.Append("              {return \"该命题不成立！\";}");
            sb.Append(Environment.NewLine);
            sb.Append("         }");
            sb.Append(Environment.NewLine);
            sb.Append("     }");
            #endregion

            sb.Append(Environment.NewLine);
            sb.Append("}");

            #endregion

            #region 打印/返回代码

            string code = sb.ToString();

            Console.WriteLine(code);
            Console.WriteLine();
            return code;

            #endregion
        }

		/// <summary>
		/// 命题正规化
		/// </summary>
		/// <param name="Proposition"></param>
		/// <returns></returns>
		public static string Manager(string Proposition)
		{

			//合式公式判定
			int flag=isWFF(Proposition);

			if(flag!=0)
			{
                return "该命题不是合式公式！";
			}
			else
			{
				//符号替换(消去↔，﹁)
				Proposition=SymbolRepalce(Proposition);

				//正规化（消去→）具体算法，参考之前命题逻辑系统中完备性证明
                Proposition = Formalize(Proposition);
			}

			return Proposition;		

		}

		/// <summary>
        /// 判定命题是否是合式公式
		/// </summary>
		/// <param name="StrPro"></param>
		/// <returns></returns>
		private static int isWFF(string StrPro)
		{
			int flag=0;
			//消去语句所有的空格
			StrPro=StrPro.Trim().Replace(" ","");

			//空字符串
			if(StrPro.Length==0)
			{
				flag=1;
			}

            if (StrPro.IndexOf("==")>-1)
            {
                flag = 2;
            }

			//除了逻辑运算符，命题符号中有非字母字符
            string kk = StrPro.Replace("↔", "").Replace("﹁", "").Replace("∧", "").Replace("∨", "").Replace("=", "").Replace("→", "").Replace("(", "").Replace(")", "");
            if (!Regex.Match(kk,"^[A-Za-z]+$").Success)
			{              
				flag=3;
			}

			//合式公式中"(",")"数量相等
            if(StrPro.IndexOf("(")>-1&&StrPro.IndexOf(")")>-1)
            {
                int ll = Regex.Matches(StrPro, @"\(").Count;
                int mm=Regex.Matches(StrPro, @"\)").Count;

                if (Regex.Matches(StrPro, @"\(").Count!= Regex.Matches(StrPro, @"\)").Count)
                {
                    flag = 4;
                }
            }

			//合式公式中在最小运算单元中不存在&&，||并存的模糊定义
			string[] stringSplit = StrPro.Split(new char[] { '(' ,')'},StringSplitOptions.None);
			foreach(string item in stringSplit)
			{
				if(item.IndexOf("&&")>-1&&item.IndexOf("||")>-1)
				{					
					flag=5;			
				}
				else
					continue;				
			}					
			return flag;
		}

		/// <summary>
        /// 对逻辑运算中的等价，非，合取，析取符号进行替换
		/// </summary>
		/// <param name="StrPro"></param>
		/// <returns></returns>
		private static string SymbolRepalce(string StrPro)
		{

            StrPro = StrPro.Replace("↔", "==").Replace("﹁", "!").Replace("∧", "&&").Replace("∨", "||").Replace("=", "==");

			return StrPro;

		}

		/// <summary>
        /// 逻辑运算符蕴含→，比较特殊，需要通过之前完备性证明的一个命题进行形式转换
		/// </summary>
		/// <param name="StrPro"></param>
		/// <returns></returns>
		private  static string Formalize(string StrPro)
		{

			string strP="";
            int k = StrPro.IndexOf("→");
			string strArrF=StrPro.Substring(0,k);
            string strArrS = StrPro.Substring(k + 1);
			strP="!("+strArrF+")"+"||("+strArrS+")";
            int m = strP.IndexOf("→");
            while (m > -1)
            {
                Formalize(strP);  
            }
		    return strP;
		}

		/// <summary>
        /// 取出所有命题符号
		/// </summary>
		/// <param name="StrPro"></param>
		/// <returns></returns>
		private static List<string> DefineBool(string StrPro)
		{
			string[] stringSplit = StrPro.Split(new string[] { "(" ,")","!","||","&&","=="},StringSplitOptions.None);

			List<string> strNew=new List<string>();

			foreach(string item in stringSplit)
			{
				if(item!="")
				{
					strNew.Add(item);
				}
			}
            return strNew = strNew.AsEnumerable().Distinct().ToList();
		}

    }
}
```
