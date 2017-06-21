---
layout: post
title:  "Automatic Propositional Proof in JAVA"
date:   2016-01-12 23:00:00
categories: Engineering
comments: false
---

 - 之前曾经用C#实现了命题逻辑自动证明机，其实本质上来说数学基础比较简单，所有的基础就是基本的“与”，“或”，“非”的逻辑运算。
 - 关键是通过具体的语言进行实现，涉及到几个比较不太常用的动态编译的系统方法，剩下的就是通过逻辑结构判定是否是合式公式，以及通过字符串对自然逻辑符号进行替换。
 <!--more-->
 - 这次就不提供完整的JAVA代码实现，仅对其中需要用到的JAVA的一些技术进行简单的阐述，如果要实现，可以参考之前的C#版本，基本的结构其实是一致的。

------

JAVA动态编译其中涉及几个平时不太常用的类，下面对具体的类进行说明：
1.通过系统的ToolProvider创建一个JavaCompiler对象
```java
JavaCompiler compiler=ToolProvider.getSystemJavaCompiler();
```
2.通过JavaCompiler对象的run()方法，传入JAVA代码源文件可以直接编译执行
```java
//run(InputStream in, OutputStream out, OutputStream err, String... arguments)
/*前 3 个参数分别用来为 java 编译器提供参数、得到 Java 编译器的输出信息及接收编译器的错误信息,后面的可变参数能传入一个或多个 Java 源程式文件。如果 run 编译成功,返回 0。*/
int res=compiler.run(null,null,null,"hello.java");
```  

- Given，the source code have achieved the aim of replacement of symbol and the determination of wff. The info. above is enough.
- 但是现在有个问题，就是在执行的时候，如果没有java源文件，需要动态的将整个方法的字符串进行动态编译，就有问题了。run()方法中，需要有".java"源文件作为参数进行传入，如果没有的该参数，实现直接在内存中实现字符串的动态编译，则需要更灵活的方法。

3.动态的拼接java源码字符串  
```java
javaStringWriter writer = new StringWriter(); PrintWriter out = new PrintWriter(writer);  
out.println("package com.dynamic.hello;");  
out.println("public class Hello{");  
out.println("public static void main(String[] args){");     
out.println("System.out.println(\"HelloWorld!
\");");  //动态替换成命题判定语句即可
out.println("}");  
out.println("}");  
out.flush();  
out.close();  
```

4.JavaFileObject 类型对象&CompilationTask对象

 - JavaCompiler 对象有一个GetTask(null, fileManager, null, null, null,compilationUnits)方法，在这个方法的最后一个参数，我们可以动态将内存中拼接的java源码字符串传入。
 - 通过上述方法可以得到一个CompilationTask对象，可以通过该对象的call()方法实现对内存中java源码字符串的编译。

```java
JavaCompiler JC=ToolProvider.getSystemJavaCompiler();  
JavaFileObject DynamicStr= new JavaStringObject("Hello", writer.toString());  
CompilationTask task = JC.getTask(null, null, null, Arrays.asList("-d","./bin"), null, Arrays.asList(DynamicStr));  
```
和之前的直接编译java源文件方法不同，call()方法返回的是布尔值，这里我们就可以通过布尔值直接判断是否编译成功了。

```java
boolean success = task.call();
if (!success) {
System.out.println("编译失败");
}else{
System.out.println("编译成功");
}
```

到这里，在技术上实现自动逻辑证明所需的java相关类已经基本介绍完了。剩下的两个部分：自然逻辑符号判定&合式公式的判定逻辑参考C#的代码实现，很容易就能完成。

完。
