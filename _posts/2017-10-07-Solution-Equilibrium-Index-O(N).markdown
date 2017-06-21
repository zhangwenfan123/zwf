---
layout: post
title:  "Solution of Equilibrium Index Problem in O(N)"
date:   2016-10-07 23:00:00
categories: Personal
comments: false
---
* content
{:toc}

In this blog, I gave the solution of Equilibrium Index Problem in Three Languages.
 <!--more-->

```java
public class Solution {
	//平衡索引算法-java
	public int solution(int[] arr){

	       int sumright=0;
	       int sumleft=0;
	       int index=0;
	       for(int i=0;i<arr.length;i++){
	       sumright+=arr[i];
	       }  

	       for(int i=0;i<arr.length;i++){
		       sumleft+=arr[i];
		       sumright-=arr[i];
		       if(sumleft==sumright){
			       return i;
			       }else if(sumleft>sumright){
                return -1;
			       }
	       }
	       return -1;
	}
}
//在主方法中调用：
int[] arr={5,7,1,3,4,3,7,2};
System.out.print(new Solution().solution(arr));

```

```python
class Solution:
    #平衡索引算法-python
    def slotion(self,arr):
        sumleft = 0
        sumright = 0
        for item in arr:
            sumright = sumright + item

        for item in arr:
            sumleft = sumleft + item
            sumright = sumright - item
            if sumleft == sumright:
                return arr.index(item)
            elif sumleft > sumright:
                return -1

#arr=[5,7,1,1,4,3,5,2]
#print(Solution().slotion(arr))
```

```c#
namespace algorithm
{
    public class Solution
    {
        //平衡索引算法-c#
        public int solution(int[] arr)
        {
            int sumright = 0;
            int sumleft = 0;
            for (int i = 0; i < arr.Count(); i++)
            {
                sumright += arr[i];
            }

            for (int i = 0; i < arr.Count(); i++)
            {
                sumleft += arr[i];
                sumright -= arr[i];
                if (sumleft == sumright)
                {
                    return i;
                }
                else if (sumleft > sumright)
                {
                    return -1;
                }
            }
            return -1;
        }
    }
}
//在主方法中调用：
int[] arr={5,7,1,3,4,3,7,2};
Console.WriteLine(new Solution().solution(arr));
Console.Read();

```
