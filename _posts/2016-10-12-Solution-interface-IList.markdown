---
layout: post
title:  "IList, IList<> and IReadOnlyList<>"
date:   2016-10-12 23:00:00
categories: Algorithm
comments: false
---
* content
{:toc}

题目：请简单说一说C#中主要的集合类，并阐述几种主要集合类的实现原理的区别。
 <!--more-->
-----
思考：（Part 2）
在任何一门号称为高级编程语言的语言当中，各种基础集合类是必不可少的一部分。在实际的编程当中，开发人员肯定会遇到各种需要操作大量数据，或者大量对象的情况。在面对不同的应用场景下，各种不同的集合类便在这里发挥出了其各自在设计的时候，所具备的各种强大特性。当然，在c#当中，也同样有很多强大的集合类用来处理大量数据的应用场景。在日常的实际使用中，虽然用起来很方便，但是却不太有机会深入到源码的底层去了解其具体的实现。这里我想借这个机会，深入了解一下，同时也是自己学习提升的一次尝试。

上一篇文章主要聊了C#最基础的几个重要接口IEnumerator,IEnumerable,ICollection以及这几个接口的泛型等价接口，并且从源码的层面简要的对比了各个接口之间的差异。本篇会接着聊一些继承了这几个基础接口的集合接口及其各自的集合实现，同样源码是参考.NET4.5版本。

-----

论C#集合_接口部分_List

```
//作为日常最常见的接口及其实现，IList接口及List在业务系统的代码中实在是及其重要的一部分
//下面的部分我们先看一下IList接口具体的内容
////下面的源码参考.NET framework 4.5

```
-----
IList接口及其泛型形式

```
// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IList
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all Lists.
**
**
===========================================================*/
namespace System.Collections {

    using System;
    using System.Diagnostics.Contracts;

    // An IList is an ordered collection of objects.  The exact ordering
    // is up to the implementation of the list, ranging from a sorted
    // order to insertion order.  
#if CONTRACTS_FULL
    [ContractClass(typeof(IListContract))]
#endif // CONTRACTS_FULL
    [System.Runtime.InteropServices.ComVisible(true)]
    public interface IList : ICollection
    {
        // The Item property provides methods to read and edit entries in the List.
        Object this[int index] {
            get;
            set;
        }

        // Adds an item to the list.  The exact position in the list is
        // implementation-dependent, so while ArrayList may always insert
        // in the last available location, a SortedList most likely would not.
        // The return value is the position the new element was inserted in.
        int Add(Object value);

        // Returns whether the list contains a particular item.
        bool Contains(Object value);

        // Removes all items from the list.
        void Clear();

        bool IsReadOnly
        { get; }


        bool IsFixedSize
        {
            get;
        }


        // Returns the index of a particular item, if it is in the list.
        // Returns -1 if the item isn't in the list.
        int IndexOf(Object value);

        // Inserts value into the list at position index.
        // index must be non-negative and less than or equal to the
        // number of elements in the list.  If index equals the number
        // of items in the list, then value is appended to the end.
        void Insert(int index, Object value);

        // Removes an item from the list.
        void Remove(Object value);

        // Removes the item at position index.
        void RemoveAt(int index);
    }

//泛型形式

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IList
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all generic lists.
**
**
===========================================================*/
namespace System.Collections.Generic {

    using System;
    using System.Collections;
    using System.Runtime.CompilerServices;
    using System.Diagnostics.Contracts;

    // An IList is an ordered collection of objects.  The exact ordering
    // is up to the implementation of the list, ranging from a sorted
    // order to insertion order.  

    // Note that T[] : IList<T>, and we want to ensure that if you use
    // IList<YourValueType>, we ensure a YourValueType[] can be used
    // without jitting.  Hence the TypeDependencyAttribute on SZArrayHelper.
    // This is a special hack internally though - see VM\compile.cpp.
    // The same attribute is on IEnumerable<T> and ICollection<T>.
    [TypeDependencyAttribute("System.SZArrayHelper")]
#if CONTRACTS_FULL
    [ContractClass(typeof(IListContract<>))]
#endif // CONTRACTS_FULL
    public interface IList<T> : ICollection<T>
    {
        // The Item property provides methods to read and edit entries in the List.
        T this[int index] {
            get;
            set;
        }

        // Returns the index of a particular item, if it is in the list.
        // Returns -1 if the item isn't in the list.
        int IndexOf(T item);

        // Inserts value into the list at position index.
        // index must be non-negative and less than or equal to the
        // number of elements in the list.  If index equals the number
        // of items in the list, then value is appended to the end.
        void Insert(int index, T item);

        // Removes the item at position index.
        void RemoveAt(int index);
    }


```
这里可以看到IList接口实际就是在继承ICollection接口的基础上，又新增了一些操作方法，比原有的基础集合更为强大。然后在我们最常用的List<> list=new List<>()方式创建的List<T>泛型类，
在下面的源码实现中，可以看到其继承的接口和实现。如下：

```
namespace System.Collections.Generic {

    using System;
    using System.Runtime;
    using System.Runtime.Versioning;
    using System.Diagnostics;
    using System.Diagnostics.Contracts;
    using System.Collections.ObjectModel;
    using System.Security.Permissions;

    // Implements a variable-size List that uses an array of objects to store the
    // elements. A List has a capacity, which is the allocated length
    // of the internal array. As elements are added to a List, the capacity
    // of the List is automatically increased as required by reallocating the
    // internal array.
    //
    [DebuggerTypeProxy(typeof(Mscorlib_CollectionDebugView<>))]
    [DebuggerDisplay("Count = {Count}")]
    [Serializable]
    public class List<T> : IList<T>, System.Collections.IList, IReadOnlyList<T>
    {
        private const int _defaultCapacity = 4;

        private T[] _items;
        [ContractPublicPropertyName("Count")]
        private int _size;
        private int _version;
        [NonSerialized]
        private Object _syncRoot;

        static readonly T[]  _emptyArray = new T[0];        

        // Constructs a List. The list is initially empty and has a capacity
        // of zero. Upon adding the first element to the list the capacity is
        // increased to 16, and then increased in multiples of two as required.
#if !FEATURE_CORECLR
        [TargetedPatchingOptOut("Performance critical to inline across NGen image boundaries")]
#endif
        public List() {
            _items = _emptyArray;
        }

        // Constructs a List with a given initial capacity. The list is
        // initially empty, but will have room for the given number of elements
        // before any reallocations are required.
        //
#if !FEATURE_CORECLR
        [TargetedPatchingOptOut("Performance critical to inline across NGen image boundaries")]
#endif
        public List(int capacity) {
            if (capacity < 0) ThrowHelper.ThrowArgumentOutOfRangeException(ExceptionArgument.capacity, ExceptionResource.ArgumentOutOfRange_NeedNonNegNum);
            Contract.EndContractBlock();

            if (capacity == 0)
                _items = _emptyArray;
            else
                _items = new T[capacity];
        }

        // Constructs a List, copying the contents of the given collection. The
        // size and capacity of the new list will both be equal to the size of the
        // given collection.
        //
        public List(IEnumerable<T> collection) {
            if (collection==null)
                ThrowHelper.ThrowArgumentNullException(ExceptionArgument.collection);
            Contract.EndContractBlock();

            ICollection<T> c = collection as ICollection<T>;
            if( c != null) {
                int count = c.Count;
                if (count == 0)
                {
                    _items = _emptyArray;
                }
                else {
                    _items = new T[count];
                    c.CopyTo(_items, 0);
                    _size = count;
                }
            }    
            else {                
                _size = 0;
                _items = _emptyArray;
                // This enumerable could be empty.  Let Add allocate a new array, if needed.
                // Note it will also go to _defaultCapacity first, not 1, then 2, etc.

                using(IEnumerator<T> en = collection.GetEnumerator()) {
                    while(en.MoveNext()) {
                        Add(en.Current);                                    
                    }
                }
            }
        }

        // Gets and sets the capacity of this list.  The capacity is the size of
        // the internal array used to hold items.  When set, the internal
        // array of the list is reallocated to the given capacity.
        //
        public int Capacity {
#if !FEATURE_CORECLR
            [TargetedPatchingOptOut("Performance critical to inline across NGen image boundaries")]
#endif
            get {
                Contract.Ensures(Contract.Result<int>() >= 0);
                return _items.Length;
            }
            set {
                if (value < _size) {
                    ThrowHelper.ThrowArgumentOutOfRangeException(ExceptionArgument.value, ExceptionResource.ArgumentOutOfRange_SmallCapacity);
                }
                Contract.EndContractBlock();

                if (value != _items.Length) {
                    if (value > 0) {
                        T[] newItems = new T[value];
                        if (_size > 0) {
                            Array.Copy(_items, 0, newItems, 0, _size);
                        }
                        _items = newItems;
                    }
                    else {
                        _items = _emptyArray;
                    }
                }
            }
        }
	//具体实现略...
        private void EnsureCapacity(int min) {
            if (_items.Length < min) {
                int newCapacity = _items.Length == 0? _defaultCapacity : _items.Length * 2;
                // Allow the list to grow to maximum possible capacity (~2G elements) before encountering overflow.
                // Note that this check works even when _items.Length overflowed thanks to the (uint) cast
                if ((uint)newCapacity > Array.MaxArrayLength) newCapacity = Array.MaxArrayLength;
                if (newCapacity < min) newCapacity = min;
                Capacity = newCapacity;
            }
        }


```
在上述的具体实现中，可以看到，List<>泛型类继承了IList<>,IList,IReadOnlyList<>三个接口，数据实际上是通过一个动态数组Array来承载具体的数据。
且在初始化的时候通过_defaultCapacity将数组初始长度设定为4，通过内部成员Capacity来维护数组的容量，在内部通过EnsureCapacity这个方法,来处理集合在插入数据的时候动态数组容量的变化。
在这个方法当中可以清楚的看到，在集合数组容量不足的时候会将数组容量*2,以保证容量的充足。

-----

当然整个实现还有很多具体的细节，这里主要阐述各个接口之间的关系。更多的实现细节可以参考源码。

-----

这里有一个IReadOnlyList<T>接口之前没有说，这个是在Framework4.5中新增的接口类型，可以被看作是IList<T>的缩减版，去掉了所有可能更改这个集合的功能。比如：Add, RemoveAt等等。
下面的源码可以看出其具体的继承关系。

```
//IReadOnlyList
    public interface IReadOnlyList<out T> : IReadOnlyCollection<T>
    {
        T this[int index] { get; }
    }

//IReadOnlyCollection
    public interface IReadOnlyCollection<out T> : IEnumerable<T>
    {
        int Count { get; }
    }

```
-----


在List的实现中，可能还有一类，我们在日常使用中能接触到，就是LinkedList<>同样也是一种集合，不过LinkedList在内部维护了一个双向的链表，也就是说我们在LinkedList的任何位置添加或者删除数据其性能都是很快的。因为它不会导致其它元素的移动。一般情况下List已经够我们使用了，但是如果对这个集合在中间的添加删除操作非常频繁的话，就建议使用LinkedList。具体源码如下：



```

namespace System.Collections.Generic {
    using System;
    using System.Diagnostics;
    using System.Diagnostics.CodeAnalysis;
#if !SILVERLIGHT
    using System.Runtime.Serialization;   
#endif
    using System.Security.Permissions;

    [System.Runtime.InteropServices.ComVisible(false)]  
    [DebuggerTypeProxy(typeof(System_CollectionDebugView<>))]
    [DebuggerDisplay("Count = {Count}")]    
#if SILVERLIGHT
    public class LinkedList<T>: ICollection<T>, System.Collections.ICollection
#else
    [Serializable()]    
    public class LinkedList<T>: ICollection<T>, System.Collections.ICollection
           ,ISerializable, IDeserializationCallback
#endif
    {
        // This LinkedList is a doubly-Linked circular list.
        internal LinkedListNode<T> head;
        internal int count;
        internal int version;
        private Object _syncRoot;

#if !SILVERLIGHT
        private SerializationInfo siInfo; //A temporary variable which we need during deserialization.        
#endif

        // names for serialization
        const String VersionName = "Version";
        const String CountName = "Count";  
        const String ValuesName = "Data";

        public LinkedList() {
        }

	//具体实现略...

```

从上述源码实现能看出来其维护的内部head成员变量，作为LinkedListNode<T>双向链表装载具体的数据。



-----

待续：到目前为止，几个集合相关的主要接口之间关系大致的捋了捋。除了这些，集合中还有一大类——键值对相关的接口或者叫字典类相关接口。这里内容较多，在下篇文中主要阐述字典相关的接口和实现。
