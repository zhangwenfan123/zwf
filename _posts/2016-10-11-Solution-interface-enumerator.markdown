---
layout: post
title:  "Enumerator, IEnumerable and Collection"
date:   2016-10-11 23:00:00
categories: Algorithm
tags: Algorithm
comments: false
---
* content
{:toc}

题目：请简单说一说C#中主要的集合类，并阐述几种主要集合类的实现原理的区别。
 <!--more-->

-----
**思考：**（Part 1）
在任何一门号称为高级编程语言的语言当中，各种基础集合类是必不可少的一部分。在实际的编程当中，开发人员肯定会遇到各种需要操作大量数据，或者大量对象的情况。在面对不同的应用场景下，各种不同的集合类便在这里发挥出了其各自在设计的时候，所具备的各种强大特性。当然，在c#当中，也同样有很多强大的集合类用来处理大量数据的应用场景。在日常的实际使用中，虽然用起来很方便，但是却不太有机会深入到源码的底层去了解其具体的实现。这里我想借这个机会，深入了解一下，同时也是自己学习提升的一次尝试。

这里主要讨论C#和JAVA两种语言的集合类及底层的实现原理，具体的实现细节可能有区别，但是原理上应当是相差不大的。学过JAVA的人，对List接口，ArrayList,LinkedList以及HashMap,HashSet之类的估计再熟悉不过了。而学习C#的同学，对早期老师说过的IEnumerator接口，GetEnumerator方法，还有各种ICollection,IList等估计也是和老朋友一般。

-----

## 论C#集合_接口部分_Enumerator

```
//学习开发之初面试万精油之IEnumerator和GetEnumerator方法，还有大家常用的各种List，ArrayList等集合及其泛型类
//这里通过源码探索一下各自的关系，首先看一看集合相关最基础的接口部分是怎样的。
//下面的源码参考.NET framework 4.5
```
-----
IEnumerator接口和IEnumerable接口

```
//First of First:IEnumerator接口

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*==========================================================================
**
** Interface:  IEnumerator
**
**
** Purpose:
** This interface is redefined here since the original IEnumerator interface
** has all its methods marked as ecall's since it is a managed standard
** interface. This interface is used from within the runtime to make a call
** on the COM server directly when it implements the IEnumerator interface.
**
**
==========================================================================*/
namespace System.Runtime.InteropServices.ComTypes
{
    using System;

    [Guid("496B0ABF-CDEE-11d3-88E8-00902754C43A")]
    internal interface IEnumerator
    {
        bool MoveNext();

        Object Current
        {
            get;
        }

        void Reset();
    }
}

//Second of First:IEnumerator接口

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IEnumerable
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Interface for classes providing IEnumerators
**
**
===========================================================*/
namespace System.Collections {
    using System;
    using System.Diagnostics.Contracts;
    using System.Runtime.InteropServices;

    // Implement this interface if you need to support VB's foreach semantics.
    // Also, COM classes that support an enumerator will also implement this interface.
#if CONTRACTS_FULL
    [ContractClass(typeof(IEnumerableContract))]
#endif // CONTRACTS_FULL
    [Guid("496B0ABE-CDEE-11d3-88E8-00902754C43A")]
    [System.Runtime.InteropServices.ComVisible(true)]
    public interface IEnumerable
    {
        // Interfaces are not serializable
        // Returns an IEnumerator for this enumerable Object.  The enumerator provides
        // a simple way to access all the contents of a collection.
        [Pure]
        [DispId(-4)]
        IEnumerator GetEnumerator();
    }

#if CONTRACTS_FULL
    [ContractClassFor(typeof(IEnumerable))]
    internal abstract class IEnumerableContract : IEnumerable
    {
        [Pure]
        IEnumerator IEnumerable.GetEnumerator()
        {
            Contract.Ensures(Contract.Result<IEnumerator>() != null);
            return default(IEnumerator);
        }
    }
#endif // CONTRACTS_FULL
}

//上述两种接口的各自的泛型形式如下：

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IEnumerator
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all generic enumerators.
**
**
===========================================================*/
namespace System.Collections.Generic {    
    using System;
    using System.Runtime.InteropServices;

    // Base interface for all generic enumerators, providing a simple approach
    // to iterating over a collection.
    public interface IEnumerator<out T> : IDisposable, IEnumerator
    {    
        // Returns the current element of the enumeration. The returned value is
        // undefined before the first call to MoveNext and following a
        // call to MoveNext that returned false. Multiple calls to
        // GetCurrent with no intervening calls to MoveNext
        // will return the same object.
        //
        /// <include file='doc\IEnumerator.uex' path='docs/doc[@for="IEnumerator.Current"]/*' />
        new T Current {
            get;
        }
    }
}

//
// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IEnumerable
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Interface for providing generic IEnumerators
**
**
===========================================================*/
namespace System.Collections.Generic {
    using System;
    using System.Collections;
    using System.Runtime.InteropServices;
    using System.Runtime.CompilerServices;
    using System.Diagnostics.Contracts;

    // Implement this interface if you need to support foreach semantics.

    // Note that T[] : IList<T>, and we want to ensure that if you use
    // IList<YourValueType>, we ensure a YourValueType[] can be used
    // without jitting.  Hence the TypeDependencyAttribute on SZArrayHelper.
    // This is a special hack internally though - see VM\compile.cpp.
    // The same attribute is on IList<T> and ICollection<T>.
    [TypeDependencyAttribute("System.SZArrayHelper")]
#if CONTRACTS_FULL
    [ContractClass(typeof(IEnumerableContract<>))]
#endif // CONTRACTS_FULL
    public interface IEnumerable<out T> : IEnumerable
    {
        // Returns an IEnumerator for this enumerable Object.  The enumerator provides
        // a simple way to access all the contents of a collection.
        /// <include file='doc\IEnumerable.uex' path='docs/doc[@for="IEnumerable.GetEnumerator"]/*' />
        new IEnumerator<T> GetEnumerator();
    }

#if CONTRACTS_FULL
    [ContractClassFor(typeof(IEnumerable<>))]
    internal abstract class IEnumerableContract<T> : IEnumerable<T>
    {
        [Pure]
        IEnumerator<T> IEnumerable<T>.GetEnumerator()
        {
            Contract.Ensures(Contract.Result<IEnumerator<T>>() != null);
            return default(IEnumerator<T>);
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            return default(IEnumerator);
        }
    }
#endif // CONTRACTS_FULL
}



```
上述源码可以看出IEnumerator接口实际上是提供给了一个类MoveNext(),Current和Reset三个方法，也就是提供给了类遍历集合对象的能力而已。
而IEnumerable接口本质上是提供了一个GetEnumerator成员，获取一个遍历器。

-----

在这里，有一个小的细节，我们在使用foreach的时候，其底层的实现也是通过内部的遍历器变量来完成循环操作的。
在语法层面上，作为一种语法糖，foreach直接提供了方便的使用方式。在源码中也可以看到其遍历能力的来源。

```
//-----------------------------------------------------------------------------
// Copyright (c) Microsoft Corporation.  All rights reserved.
//-----------------------------------------------------------------------------

namespace System.Activities.Statements
{
    using System.Collections;
    using System.Collections.Generic;
    using System.ComponentModel;
    using System.Runtime;
    using System.Runtime.Serialization;
    using System.Windows.Markup;
    using System.Activities;
    using System.Activities.Validation;
    using SA = System.Activities;

    [ContentProperty("Body")]
    public sealed class ForEach<T> : NativeActivity
    {
        Variable<IEnumerator<T>> valueEnumerator;
        CompletionCallback onChildComplete;

        public ForEach()
            : base()
        {
            this.valueEnumerator = new Variable<IEnumerator<T>>();
        }

	//其他成员略

    }

```
-----

## 论C#集合_接口部分_Collection

-----

除了上述两个基础的接口，还有ICollection集合接口。相对于上述两个接口提供的对象遍历能力，在IColletction接口当中，另外新增了拷贝，数量等成员。
具体的源码及其相对应的泛型形式源码如下：


```
//First of first:ICollection接口

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  ICollection
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all collections.
**
**
===========================================================*/
namespace System.Collections {
    using System;
    using System.Diagnostics.Contracts;

    // Base interface for all collections, defining enumerators, size, and
    // synchronization methods.
#if CONTRACTS_FULL
    [ContractClass(typeof(ICollectionContract))]
#endif // CONTRACTS_FULL
    [System.Runtime.InteropServices.ComVisible(true)]
    public interface ICollection : IEnumerable
    {
        // Interfaces are not serialable
        // CopyTo copies a collection into an Array, starting at a particular
        // index into the array.
        //
        void CopyTo(Array array, int index);

        // Number of items in the collections.
        int Count
        { get; }


        // SyncRoot will return an Object to use for synchronization
        // (thread safety).  You can use this object in your code to take a
        // lock on the collection, even if this collection is a wrapper around
        // another collection.  The intent is to tunnel through to a real
        // implementation of a collection, and use one of the internal objects
        // found in that code.
        //
        // In the absense of a static Synchronized method on a collection,
        // the expected usage for SyncRoot would look like this:
        //
        // ICollection col = ...
        // lock (col.SyncRoot) {
        //     // Some operation on the collection, which is now thread safe.
        //     // This may include multiple operations.
        // }
        //
        //
        // The system-provided collections have a static method called
        // Synchronized which will create a thread-safe wrapper around the
        // collection.  All access to the collection that you want to be
        // thread-safe should go through that wrapper collection.  However, if
        // you need to do multiple calls on that collection (such as retrieving
        // two items, or checking the count then doing something), you should
        // NOT use our thread-safe wrapper since it only takes a lock for the
        // duration of a single method call.  Instead, use Monitor.Enter/Exit
        // or your language's equivalent to the C# lock keyword as mentioned
        // above.
        //
        // For collections with no publically available underlying store, the
        // expected implementation is to simply return the this pointer.  Note
        // that the this pointer may not be sufficient for collections that
        // wrap other collections;  those should return the underlying
        // collection's SyncRoot property.
        Object SyncRoot
        { get; }

        // Is this collection synchronized (i.e., thread-safe)?  If you want a
        // thread-safe collection, you can use SyncRoot as an object to
        // synchronize your collection with.  If you're using one of the
        // collections in System.Collections, you could call the static
        // Synchronized method to get a thread-safe wrapper around the
        // underlying collection.
        bool IsSynchronized
        { get; }
    }

#if CONTRACTS_FULL
    [ContractClassFor(typeof(ICollection))]
    internal abstract class ICollectionContract : ICollection
    {
        void ICollection.CopyTo(Array array, int index)
        {
        }

        int ICollection.Count {
            get {
                Contract.Ensures(Contract.Result<int>() >= 0);
                return default(int);
            }
        }

        Object ICollection.SyncRoot {
            get {
                Contract.Ensures(Contract.Result<Object>() != null);
                return default(Object);
            }
        }

        bool ICollection.IsSynchronized {
            get { return default(bool); }
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            return default(IEnumerator);
        }
    }
#endif // CONTRACTS_FULL
}

//Second of first:ICollection接口泛型形式ICollection<T>,此处源码可以看泛型形式提供了更多的处理方法，也同时意味着功能更为强大。

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  ICollection
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all generic collections.
**
**
===========================================================*/
namespace System.Collections.Generic {
    using System;
    using System.Runtime.CompilerServices;
    using System.Diagnostics.Contracts;

    // Base interface for all collections, defining enumerators, size, and
    // synchronization methods.

    // Note that T[] : IList<T>, and we want to ensure that if you use
    // IList<YourValueType>, we ensure a YourValueType[] can be used
    // without jitting.  Hence the TypeDependencyAttribute on SZArrayHelper.
    // This is a special hack internally though - see VM\compile.cpp.
    // The same attribute is on IEnumerable<T> and ICollection<T>.
#if CONTRACTS_FULL
    [ContractClass(typeof(ICollectionContract<>))]
#endif
    [TypeDependencyAttribute("System.SZArrayHelper")]
    public interface ICollection<T> : IEnumerable<T>
    {
        // Number of items in the collections.        
        int Count { get; }

        bool IsReadOnly { get; }

        void Add(T item);

        void Clear();

        bool Contains(T item);

        // CopyTo copies a collection into an Array, starting at a particular
        // index into the array.
        //
        void CopyTo(T[] array, int arrayIndex);

        //void CopyTo(int sourceIndex, T[] destinationArray, int destinationIndex, int count);

        bool Remove(T item);
    }

#if CONTRACTS_FULL
    [ContractClassFor(typeof(ICollection<>))]
    internal abstract class ICollectionContract<T> : ICollection<T>
    {
        int ICollection<T>.Count {
            get {
                Contract.Ensures(Contract.Result<int>() >= 0);
                return default(int);
            }
        }

        bool ICollection<T>.IsReadOnly {
            get { return default(bool); }
        }

        void ICollection<T>.Add(T item)
        {
            //Contract.Ensures(((ICollection<T>)this).Count == Contract.OldValue(((ICollection<T>)this).Count) + 1);  // not threadsafe
        }

        void ICollection<T>.Clear()
        {
        }

        bool ICollection<T>.Contains(T item)
        {
            return default(bool);
        }

        void ICollection<T>.CopyTo(T[] array, int arrayIndex)
        {
        }

        bool ICollection<T>.Remove(T item)
        {
            return default(bool);
        }

        IEnumerator<T> IEnumerable<T>.GetEnumerator()
        {
            return default(IEnumerator<T>);
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            return default(IEnumerator);
        }
    }
#endif // CONTRACTS_FULL
}

```

-----

待续：
在源码层面彻底了解各自之间的关系是一个庞大的工程。本文只是尝试探索了几个最基础的接口，后续会更进一步阐述遗留的一些接口及其相关的实现类。

-----
近期反思了自己的技术学习的历程，个人对自己做了个简单的总结：用之有余，不求甚解，唯论应用，不求精深。
在接下来的日子中，个人决定调整开发和学习的策略，希望自己可以静下心来。愿与各位共勉之。
