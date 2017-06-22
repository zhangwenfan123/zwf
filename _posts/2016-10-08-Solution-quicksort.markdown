---
layout: post
title:  "Sorting algorithms"
date:   2016-10-08 23:00:00
categories: Algorithm
comments: false
---
* content
{:toc}

题目：请手写出两种以上排序算法，并分析不同情况下复杂度的变化情况（语言不限）。
 <!--more-->

-----

**思考：**
算法分析领域，排序算法应该算是最基础的，入门就会接触到的算法。但是在这看似简单的排序上，却体现了算法分析最精髓的思想。在排序算法的历史发展过程当中，有无数大神发明了N多种排序算法，也有给CS的学生提供了大量论文素材。我在这里仅仅选择一些简单的典型进行实现，目的是为了帮助自己进一步理解其中的核心思想。

最常见的排序莫过于冒泡，快排，归并了。具体的实现代码亦有参考一些前辈。欢迎大家一起交流，学习。

-----
C#排序算法实现：

```
/*****
1.冒泡排序，即两两相比较，按大小置换。
分析：在最坏的情况下，即所有的比较之后，都需要进行置换。此时总共经历的比较次数为(N-1)K0+(N-2)K1+...+0*KN,K为循环的趟数。结果为N*(N-1)/2
在最好的情况下，无需置换，但是比较的次数并没有减少。该算法的复杂度为O(N^2)。
*****/
        public static void BubbleSort(List<int> list)
        {

            for (int i = 0; i < list.Count(); i++) {
                int temp;
                for (int j=i+1; j<list.Count();j++ ) {
                    if (list[j] < list[i])
                    {
                        temp = list[j];
                        list[j] = list[i];
                        list[i] = temp;                  
                    }
                }
            }      
        }

/*****
2.快速排序由C. A. R. Hoare在1962年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的
所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。算法核心的思想是分
治处理，在数组切分的过程中进行递归，各子部分完成排序后，数组即完成排序。其复杂度可以达到O(N*LogN)。
*****/
    public class SortHelper
    {

        public static int Division(List<int> list, int left, int right)
        {
            //首先挑选一个基准元素
            int baseNum = list[left];
            while (left < right)
            {
                //从数组的右端开始向前找，一直找到比base小的数字为止(包括base同等数)
                while (left < right && list[right] >= baseNum)
                    right = right - 1;
                //最终找到了比baseNum小的元素，要做的事情就是此元素放到base的位置
                list[left] = list[right];
                //从数组的左端开始向后找，一直找到比base大的数字为止（包括base同等数）
                while (left < right && list[left] <= baseNum)
                    left = left + 1;
                //最终找到了比baseNum大的元素，要做的事情就是将此元素放到最后的位置
                list[right] = list[left];
            }
            //最后就是把baseNum放到该left的位置
            list[left] = baseNum;
            //最终，我们发现left位置的左侧数值部分比left小，left位置右侧数值比left大
            //至此，我们完成了第一篇排序
            return left;
        }

        public static void QuickSort(List<int> list, int left, int right)
        {
            //左下标一定小于右下标，否则就超越了
            if (left < right)
            {
                //对数组进行分割，取出下次分割的基准标号
                int i = Division(list, left, right);

                //对“基准标号“左侧的一组数值进行递归的切割，以至于将这些数值完整的排序
                QuickSort(list, left, i - 1);

                //对“基准标号“右侧的一组数值进行递归的切割，以至于将这些数值完整的排序
                QuickSort(list, i + 1, right);
            }
        }
   }
/*****
3.大名鼎鼎的归并排序。由冯诺依曼发明，其核心的思想和快排是一样的，都是分治的原则。
将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。
归并排序在算法的空间复杂度上较高，时间复杂度上为O(N*LogN)。
*****/
    public class SortHelper
    {
	//主方法
        public static void MergeSortFunction(int[] array, int first, int last)
        {
                if (first < last)   //子表的长度大于1，则进入下面的递归处理
                {
                    int mid = (first + last) / 2;   //子表划分的位置
                    MergeSortFunction(array, first, mid);   //对划分出来的左侧子表进行递归划分
                    MergeSortFunction(array, mid + 1, last);    //对划分出来的右侧子表进行递归划分
                    MergeSortCore(array, first, mid, last); //对左右子表进行有序的整合（归并排序的核心部分）
                }
                //return array;
        }

        //归并排序的核心部分：将两个有序的左右子表（以mid区分），合并成一个有序的表
        private static void MergeSortCore(int[] array, int first, int mid, int last)
        {
                int indexA = first; //左侧子表的起始位置
                int indexB = mid + 1;   //右侧子表的起始位置
                int[] temp = new int[last + 1]; //声明数组（暂存左右子表的所有有序数列）：长度等于左右子表的长度之和。
                int tempIndex = 0;
                while (indexA <= mid && indexB <= last) //进行左右子表的遍历，如果其中有一个子表遍历完，则跳出循环
                {
                    if (array[indexA] <= array[indexB]) //此时左子表的数 <= 右子表的数
                    {
                        temp[tempIndex++] = array[indexA++];    //将左子表的数放入暂存数组中，遍历左子表下标++
                    }
                    else//此时左子表的数 > 右子表的数
                    {
                        temp[tempIndex++] = array[indexB++];    //将右子表的数放入暂存数组中，遍历右子表下标++
                    }
                }
                //有一侧子表遍历完后，跳出循环，将另外一侧子表剩下的数一次放入暂存数组中（有序）
                while (indexA <= mid)
                {
                    temp[tempIndex++] = array[indexA++];
                }
                while (indexB <= last)
                {
                    temp[tempIndex++] = array[indexB++];
                }

                //将暂存数组中有序的数列写入目标数组的制定位置，使进行归并的数组段有序
                tempIndex = 0;
                for (int i = first; i <= last; i++)
                {
                    array[i] = temp[tempIndex++];
                }
        }
    }

```
-----

JAVA实现上述三种排序

```
//1.冒泡
    public static void bubbleSort(int[] numbers)
    {
        int temp = 0;
        int size = numbers.length;
        for(int i = 0 ; i < size-1; i ++)
        {
        for(int j = 0 ;j < size-1-i ; j++)
        {
            if(numbers[j] > numbers[j+1])  //交换两数位置
            {
            temp = numbers[j];
            numbers[j] = numbers[j+1];
            numbers[j+1] = temp;
            }
        }
        }
    }

//2.快排
    public class SortHelper
    {
    public static int getMiddle(int[] numbers, int low,int high)
    {
        int temp = numbers[low]; //数组的第一个作为中轴
        while(low < high)
        {
        while(low < high && numbers[high] > temp)
        {
            high--;
        }
        numbers[low] = numbers[high];//比中轴小的记录移到低端
        while(low < high && numbers[low] < temp)
        {
            low++;
        }
        numbers[high] = numbers[low] ; //比中轴大的记录移到高端
        }
        numbers[low] = temp ; //中轴记录到尾
        return low ; // 返回中轴的位置
    }

    public static void quickSort(int[] numbers,int low,int high)
    {
        if(low < high)
        {
        　　int middle = getMiddle(numbers,low,high); //将numbers数组进行一分为二
        　　quickSort(numbers, low, middle-1);   //对低字段表进行递归排序
        　　quickSort(numbers, middle+1, high); //对高字段表进行递归排序
        }

    }
    }

//3.归并排序
    public class SortHelper
    {
    public static int[] sort(int[] nums, int low, int high) {  
        int mid = (low + high) / 2;  
        if (low < high) {  
            // 左边  
            sort(nums, low, mid);  
            // 右边  
            sort(nums, mid + 1, high);  
            // 左右归并  
            merge(nums, low, mid, high);  
        }  
        return nums;  
    }  

    public static void merge(int[] nums, int low, int mid, int high) {  
        int[] temp = new int[high - low + 1];  
        int i = low;// 左指针  
        int j = mid + 1;// 右指针  
        int k = 0;  

        // 把较小的数先移到新数组中  
        while (i <= mid && j <= high) {  
            if (nums[i] < nums[j]) {  
                temp[k++] = nums[i++];  
            } else {  
                temp[k++] = nums[j++];  
            }  
        }  

        // 把左边剩余的数移入数组  
        while (i <= mid) {  
            temp[k++] = nums[i++];  
        }  

        // 把右边边剩余的数移入数组  
        while (j <= high) {  
            temp[k++] = nums[j++];  
        }  

        // 把新数组中的数覆盖nums数组  
        for (int k2 = 0; k2 < temp.length; k2++) {  
            nums[k2 + low] = temp[k2];  
        }  
    }  
    }


```

-----

python实现以上三种算法

```python

#冒泡
def bubble_sort(lists):
    # 冒泡排序
    count = len(lists)
    for i in range(0, count):
        for j in range(i + 1, count):
            if lists[i] > lists[j]:
                lists[i], lists[j] = lists[j], lists[i]
    return lists

#快排
def quick_sort(lists, left, right):
    # 快速排序
    if left >= right:
        return lists
    key = lists[left]
    low = left
    high = right
    while left < right:
        while left < right and lists[right] >= key:
            right -= 1
        lists[left] = lists[right]
        while left < right and lists[left] <= key:
            left += 1
        lists[right] = lists[left]
    lists[right] = key
    quick_sort(lists, low, left - 1)
    quick_sort(lists, left + 1, high)
    return lists

#归并
def merge(left, right):
    i, j = 0, 0
    result = []
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result += left[i:]
    result += right[j:]
    return result

def merge_sort(lists):
    if len(lists) <= 1:
        return lists
    num = len(lists) / 2
    left = merge_sort(lists[:num])
    right = merge_sort(lists[num:])
    return merge(left, right)


```
