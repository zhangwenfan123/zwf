---
layout: post
title:  "Dictionary, IDictionary, ISet and HashSet"
date:   2016-10-13 23:00:00
categories: Algorithm
comments: false
---
* content
{:toc}

问题：请简单说一说C#中主要的集合类，并阐述几种主要集合类的实现原理的区别。
 <!--more-->

-----

**思考：**（Part 3）
在任何一门号称为高级编程语言的语言当中，各种基础集合类是必不可少的一部分。在实际的编程当中，开发人员肯定会遇到各种需要操作大量数据，或者大量对象的情况。在面对不同的应用场景下，各种不同的集合类便在这里发挥出了其各自在设计的时候，所具备的各种强大特性。当然，在c#当中，也同样有很多强大的集合类用来处理大量数据的应用场景。在日常的实际使用中，虽然用起来很方便，但是却不太有机会深入到源码的底层去了解其具体的实现。这里我想借这个机会，深入了解一下，同时也是自己学习提升的一次尝试。

上一篇文章主要聊了C#最基础的几个重要接口IList，List等直接在业务中使用的一些常见结构，以及这几个接口的泛型等价接口，并且从源码的层面简要的对比了各个接口之间的差异。本篇会接着聊一些继承了这几个基础接口的集合接口及其各自的集合实现，同样源码是参考.NET4.5版本。

-----

论C#集合_接口部分_IDictionary&Dictionary

```
//除了List相关接口，C#中还有字典类，字典在实际的编程中使用的情况也很多。
//下面的部分我们先看一下IDictionary接口具体的内容
////下面的源码参考.NET framework 4.5

```
-----
IDictionary接口及其泛型形式

```
// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IDictionary
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all dictionaries.
**
**
===========================================================*/
namespace System.Collections {
    using System;
    using System.Diagnostics.Contracts;

    // An IDictionary is a possibly unordered set of key-value pairs.
    // Keys can be any non-null object.  Values can be any object.
    // You can look up a value in an IDictionary via the default indexed
    // property, Items.  
#if CONTRACTS_FULL
    [ContractClass(typeof(IDictionaryContract))]
#endif // CONTRACTS_FULL
    [System.Runtime.InteropServices.ComVisible(true)]
    public interface IDictionary : ICollection
    {
        // Interfaces are not serializable
        // The Item property provides methods to read and edit entries
        // in the Dictionary.
        Object this[Object key] {
            get;
            set;
        }

        // Returns a collections of the keys in this dictionary.
        ICollection Keys {
            get;
        }

        // Returns a collections of the values in this dictionary.
        ICollection Values {
            get;
        }

        // Returns whether this dictionary contains a particular key.
        //
        bool Contains(Object key);

        // Adds a key-value pair to the dictionary.
        //
        void Add(Object key, Object value);

        // Removes all pairs from the dictionary.
        void Clear();

        bool IsReadOnly
        { get; }

        bool IsFixedSize
        { get; }

        // Returns an IDictionaryEnumerator for this dictionary.
        new IDictionaryEnumerator GetEnumerator();

        // Removes a particular key from the dictionary.
        //
        void Remove(Object key);
    }

/***
上面是IDictionary接口中定义的行为，下面是是其等价的泛型形式
***/

// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  IDictionary
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all generic dictionaries.
**
**
===========================================================*/
namespace System.Collections.Generic {
    using System;
    using System.Diagnostics.Contracts;

    // An IDictionary is a possibly unordered set of key-value pairs.
    // Keys can be any non-null object.  Values can be any object.
    // You can look up a value in an IDictionary via the default indexed
    // property, Items.  
#if CONTRACTS_FULL
    [ContractClass(typeof(IDictionaryContract<,>))]
#endif // CONTRACTS_FULL
    public interface IDictionary<TKey, TValue> : ICollection<KeyValuePair<TKey, TValue>>
    {
        // Interfaces are not serializable
        // The Item property provides methods to read and edit entries
        // in the Dictionary.
        TValue this[TKey key] {
            get;
            set;
        }

        // Returns a collections of the keys in this dictionary.
        ICollection<TKey> Keys {
            get;
        }

        // Returns a collections of the values in this dictionary.
        ICollection<TValue> Values {
            get;
        }

        // Returns whether this dictionary contains a particular key.
        //
        bool ContainsKey(TKey key);

        // Adds a key-value pair to the dictionary.
        //
        void Add(TKey key, TValue value);

        // Removes a particular key from the dictionary.
        //
        bool Remove(TKey key);

        bool TryGetValue(TKey key, out TValue value);
    }

```
在上述的接口和泛型形式中，首先可以看到定义了一个索引器，以key作为索引下标，获取对应的value值。而通过源码可以看到，key和value都是ICollection类型的一组集合。并同时定义了一些字典操作常用的方法。新增，包含判断，移除等。这两个接口是所有字典数据结构的基类。

在泛型的接口中还可以看到其继承了一个KeyValuePair<TKey, TValue>类型的集合，是一个键值对的数据结构，这个结构也是定义在system.collection.generic下的键值对的遍历器。并重写了toString方法，用来返回具体的key和value的组装。
这个类型接口同时被IDictionary<TKey, TValue> IReadOnlyDictionary<TKey, TValue>使用。
具体的源码如下：

```
// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  KeyValuePair
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Generic key-value pair for dictionary enumerators.
**
**
===========================================================*/
namespace System.Collections.Generic {

    using System;
    using System.Text;

    // A KeyValuePair holds a key and a value from a dictionary.
    // It is used by the IEnumerable<T> implementation for both IDictionary<TKey, TValue>
    // and IReadOnlyDictionary<TKey, TValue>.
    [Serializable]
    public struct KeyValuePair<TKey, TValue> {
        private TKey key;
        private TValue value;

        public KeyValuePair(TKey key, TValue value) {
            this.key = key;
            this.value = value;
        }

        public TKey Key {
            get { return key; }
        }

        public TValue Value {
            get { return value; }
        }

        public override string ToString() {
            StringBuilder s = StringBuilderCache.Acquire();
            s.Append('[');
            if( Key != null) {
                s.Append(Key.ToString());
            }
            s.Append(", ");
            if( Value != null) {
               s.Append(Value.ToString());
            }
            s.Append(']');
            return StringBuilderCache.GetStringAndRelease(s);
        }
    }
}

```
接下来，大致看一下Dictionary的具体实现：

```
namespace System.Collections.Generic {

    using System;
    using System.Collections;
    using System.Diagnostics;
    using System.Diagnostics.Contracts;
    using System.Runtime.Serialization;
    using System.Security.Permissions;

    [DebuggerTypeProxy(typeof(Mscorlib_DictionaryDebugView<,>))]
    [DebuggerDisplay("Count = {Count}")]
    [Serializable]
    [System.Runtime.InteropServices.ComVisible(false)]
    public class Dictionary<TKey,TValue>: IDictionary<TKey,TValue>, IDictionary, IReadOnlyDictionary<TKey, TValue>, ISerializable, IDeserializationCallback  {

        private struct Entry {
            public int hashCode;    // Lower 31 bits of hash code, -1 if unused
            public int next;        // Index of next entry, -1 if last
            public TKey key;           // Key of entry
            public TValue value;         // Value of entry
        }

        private int[] buckets;
        private Entry[] entries;
        private int count;
        private int version;
        private int freeList;
        private int freeCount;
        private IEqualityComparer<TKey> comparer;
        private KeyCollection keys;
        private ValueCollection values;
        private Object _syncRoot;

        // constants for serialization
        private const String VersionName = "Version";
        private const String HashSizeName = "HashSize";  // Must save buckets.Length
        private const String KeyValuePairsName = "KeyValuePairs";
        private const String ComparerName = "Comparer";

        public Dictionary(): this(0, null) {}

        public Dictionary(int capacity): this(capacity, null) {}

        public Dictionary(IEqualityComparer<TKey> comparer): this(0, comparer) {}

        public Dictionary(int capacity, IEqualityComparer<TKey> comparer) {
            if (capacity < 0) ThrowHelper.ThrowArgumentOutOfRangeException(ExceptionArgument.capacity);
            if (capacity > 0) Initialize(capacity);
            this.comparer = comparer ?? EqualityComparer<TKey>.Default;
        }

        public Dictionary(IDictionary<TKey,TValue> dictionary): this(dictionary, null) {}

        public Dictionary(IDictionary<TKey,TValue> dictionary, IEqualityComparer<TKey> comparer):
            this(dictionary != null? dictionary.Count: 0, comparer) {

            if( dictionary == null) {
                ThrowHelper.ThrowArgumentNullException(ExceptionArgument.dictionary);
            }

            foreach (KeyValuePair<TKey,TValue> pair in dictionary) {
                Add(pair.Key, pair.Value);
            }
        }

...//略


```
这是核心的基础字典类，除了这个实现类，字典类还有SortedDictionary类，这两个类分别在mscorlib和system两个包下，从逻辑上讲SortedDictionary应该是对于字典类的封装，在其中数据由二叉树结构进行存储_TreeSet<T>。具体的实现还涉及到树结构的实现和比较器的实现，如果再深入的话，可以直接参考源码：
```
        public TValue this[TKey key] {
            get {
                if ( key == null) {
                    ThrowHelper.ThrowArgumentNullException(ExceptionArgument.key);                    
                }

                TreeSet<KeyValuePair<TKey, TValue>>.Node node = _set.FindNode(new KeyValuePair<TKey, TValue>(key, default(TValue)));
                if ( node == null) {
                    ThrowHelper.ThrowKeyNotFoundException();                    
                }

                return node.Item.Value;
            }
            set {
                if( key == null) {
                    ThrowHelper.ThrowArgumentNullException(ExceptionArgument.key);
                }

                TreeSet<KeyValuePair<TKey, TValue>>.Node node = _set.FindNode(new KeyValuePair<TKey, TValue>(key, default(TValue)));
                if ( node == null) {
                    _set.Add(new KeyValuePair<TKey, TValue>(key, value));                        
                } else {
                    node.Item = new KeyValuePair<TKey, TValue>( node.Item.Key, value);
                    _set.UpdateVersion();
                }
            }
        }
/***
上面部分是索引器的是实现，下面是其中的寻找树节点的内部方法。方法当中包含一个比较器的实现。
***/
        internal virtual Node FindNode(T item) {
            Node current = root;
            while (current != null) {
                int order = comparer.Compare(item, current.Item);
                if (order == 0) {
                    return current;
                } else {
                    current = (order < 0) ? current.Left : current.Right;
                }
            }

            return null;
        }

```

-----
论C#集合_Set部分_ISet和IHashSet
```
//由上面的有序字典集合，其中有一个TreeSet的数据结构，由此可以引申出Set相关
//TreeSet继承SortedSet,SortedSet继承ISet,ICollection等，先了解ISet的具体内容
////下面的源码参考.NET framework 4.5

```
-----

```
// ==++==
//
//   Copyright (c) Microsoft Corporation.  All rights reserved.
//
// ==--==
/*============================================================
**
** Interface:  ISet
**
** <OWNER>[....]</OWNER>
**
**
** Purpose: Base interface for all generic sets.
**
**
===========================================================*/
namespace System.Collections.Generic {

    using System;
    using System.Runtime.CompilerServices;


    /// <summary>
    /// Generic collection that guarantees the uniqueness of its elements, as defined
    /// by some comparer. It also supports basic set operations such as Union, Intersection,
    /// Complement and Exclusive Complement.
    /// </summary>
    public interface ISet<T> : ICollection<T> {

        //Add ITEM to the set, return true if added, false if duplicate
        new bool Add(T item);    

        //Transform this set into its union with the IEnumerable<T> other
        void UnionWith(IEnumerable<T> other);

        //Transform this set into its intersection with the IEnumberable<T> other
        void IntersectWith(IEnumerable<T> other);

        //Transform this set so it contains no elements that are also in other
        void ExceptWith(IEnumerable<T> other);

        //Transform this set so it contains elements initially in this or in other, but not both
        void SymmetricExceptWith(IEnumerable<T> other);

        //Check if this set is a subset of other
        bool IsSubsetOf(IEnumerable<T> other);

        //Check if this set is a superset of other
        bool IsSupersetOf(IEnumerable<T> other);

        //Check if this set is a subset of other, but not the same as it
        bool IsProperSupersetOf(IEnumerable<T> other);

        //Check if this set is a superset of other, but not the same as it
        bool IsProperSubsetOf(IEnumerable<T> other);

        //Check if this set has any elements in common with other
        bool Overlaps(IEnumerable<T> other);

        //Check if this set contains the same and only the same elements as other
        bool SetEquals(IEnumerable<T> other);


    }

}
```

上面的接口源码可以看出ISet<T>是all generic sets的基础接口。可以看出其是直接继承了ICollection<T>，并提供了自己的行为定义。其内部包含多个不重复的元素，且元素为无序状态。
在SortedSet中，则可以看到其内部比ISet复杂的多，包括其内部的数据存储的具体实现。如果能直接看源码的话，可以看到其实现是基于Stack的。而且依然是用二叉树来保证其内部数据的元素存储处于有序状态。
接下来，看一下Set接口的具体实现：

```
  public class HashSet<T> : ICollection<T>, ISerializable, IDeserializationCallback, ISet<T>

```
具体的细节不做深入，这里源码直接可以看出HashSet继承与ISet泛型接口。HashSet是一个无序的能够保持唯一性的集合。我们也可以把HashSet看作是Dictionary<TKey,TValue>，只不过TKey和TValue都指向同一个对象。HashSet非常适合在我们需要保持集合内元素唯一性但又不需要按顺序排列的时候，但不支持下标访问。
除了HashSet之外，还有一个hashtable类，具体的继承关系如下：
```
    public class Hashtable : IDictionary, ISerializable, IDeserializationCallback, ICloneable {
    //略

}
```
由其继承关系，我们可以看出HashTable实际上是继承非泛型形式的字典接口IDictionary以及序列化接口和反序列化接口的。而且HashTable是没有泛型的，这点和hashset，dictionary都是不一样的。而在java中常见的hashmap，在c#项目的构建初期，虽然大量借鉴了java，但是似乎hashmap直接被抛弃了。所以没有了hashtable和hashmap这样的狗血问题。但是却有了HashSet,HashTable和Dictionary区别这样更狗血的问题。

-----

写在最后：
1.最近的考试，让我深深的感觉到除了这些基础的工程性知识外，底层知识和理论原理的羸弱。在准备考试的过程中，决定要调整未来2年的学习思路。
2.考虑到申请学位的时候论文的重要性，在接下来的时间中，会将一部分精力用于NLP的入门综合相关的阅读和学科前沿技术的论文追踪。
3.另外会尽量在以后的理论学习中，使用纯英文的材料进行阅读，写作和相关的工作。
