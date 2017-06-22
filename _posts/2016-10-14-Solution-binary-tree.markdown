---
layout: post
title:  "About the binary tree"
date:   2016-10-14 23:00:00
categories: Algorithm
comments: false
---
* content
{:toc}


题目：构建一个二叉树，并给定一个二叉树根节点Node,请写出函数计算出二叉树的深度，叶子节点。（兼论二叉树的不同遍历方式）
 <!--more-->

-----
**思考：**
在数据结构中，树是一类非常重要的结构，很多底层非常重要的基础算法都是建立在树的基础上的。数据库索引的B-tree结构存储，路由搜索引擎的搜索算法，文件系统等等。二叉树又是树中一种非常重要，又应用广泛的结构。这里重点阐述二叉树的一个相关算法，以后如果有机会再深入聊聊树相关的其他方面。

这里遇到的一个很典型的问题，就是计算二叉树的深度和叶子节点数。这里我使用几种常用的语言进行实现，同时也简单聊聊二叉树的几种不同遍历方式。结果参考网络上一些前辈的文字，以及结合个人面试过程中的一些细节。欢迎大家吐槽，交流心得。

-----

## C#实现树深度和叶子节点计算：

```
/*****
递归计算二叉树的深度和叶子节点数
*****/
    public class TreeNodeHelper
    {
        public static Node CreateTree() {
            Node root = new Node();
            root.data = 9;

            Node temp01 = new Node();
            temp01.data = 1;
            root.left = temp01;

            Node temp02 = new Node();
            temp02.data = 3;
            root.right = temp02;

            Node temp03 = new Node();
            temp03.data = 2;
            root.left.left = temp03;

            Node temp04 = new Node();
            temp04.data = 4;
            root.left.right = temp04;

            return root;

        }
        // 叶子数  
        public static int leafNum(Node node)
        {
            if (node != null)
            {
                if (node.left == null && node.right == null)
                {
                    return 1;
                }
                return leafNum(node.left) + leafNum(node.right);
            }
            return 0;
        }

        // 求二叉树的深度  
        public static int deep(Node node)
        {
            int h1, h2;
            if (node == null)
            {
                return 0;
            }
            else
            {
                h1 = deep(node.left);
                h2 = deep(node.right);
                return (h1 < h2) ? h2 + 1 : h1 + 1;
            }

        }


        // 中序遍历  左子树->根节点->右子树
        public static void SelectTreeIn(Node root) {  
        if (root == null)  
            return;
        SelectTreeIn(root.left);  
        Console.WriteLine(root.data + " ");
        SelectTreeIn(root.right);  
    }

        // 先序遍历  根节点->左子树->右子树
        public static void SelectTreePre(Node root) {  
        if (root == null)  
            return;
        Console.WriteLine(root.data + " ");
        SelectTreePre(root.left);
        SelectTreePre(root.right);  
    }

        // 后序遍历  左子树->右子树->根节点
        public static void SelectTreePost(Node root) {  
        if (root == null)  
            return;
        SelectTreePost(root.left);
        SelectTreePost(root.right);
        Console.WriteLine(root.data + " ");   
    }
    //
    public class Node
    {
        bool visited = false;
        public int data = 0;
        public Node left = null;
        public Node right = null;
    }  


```

## JAVA实现树深度和叶子节点计算：

```
    public class TreeNodeHelper
    {
        public static Node CreateTree() {
            Node root = new Node();
            root.data = 9;

            Node temp01 = new Node();
            temp01.data = 1;
            root.left = temp01;

            Node temp02 = new Node();
            temp02.data = 3;
            root.right = temp02;

            Node temp03 = new Node();
            temp03.data = 2;
            root.left.left = temp03;

            Node temp04 = new Node();
            temp04.data = 4;
            root.left.right = temp04;

            return root;

        }
        // 叶子数  
        public static int leafNum(Node node)
        {
            if (node != null)
            {
                if (node.left == null && node.right == null)
                {
                    return 1;
                }
                return leafNum(node.left) + leafNum(node.right);
            }
            return 0;
        }

        // 求二叉树的深度  
        public static int deep(Node node)
        {
            int h1, h2;
            if (node == null)
            {
                return 0;
            }
            else
            {
                h1 = deep(node.left);
                h2 = deep(node.right);
                return (h1 < h2) ? h2 + 1 : h1 + 1;
            }

        }


        // 中序遍历  左子树->根节点->右子树
        public static void SelectTreeIn(Node root) {  
        if (root == null)  
            return;
        SelectTreeIn(root.left);  
        System.out.printf(root.data + " ");
        SelectTreeIn(root.right);  
    }

        // 先序遍历  根节点->左子树->右子树
        public static void SelectTreePre(Node root) {  
        if (root == null)  
            return;
        System.out.printf(root.data + " ");
        SelectTreePre(root.left);
        SelectTreePre(root.right);  
    }

        // 后序遍历  左子树->右子树->根节点
        public static void SelectTreePost(Node root) {  
        if (root == null)  
            return;
        SelectTreePost(root.left);
        SelectTreePost(root.right);
	System.out.printf(root.data + " ");
    }
    //
    public class Node
    {
        boolean visited = false;
        int data = 0;
        Node left = null;
        Node right = null;
    }  



```
