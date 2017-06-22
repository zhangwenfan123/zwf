---
layout: post
title:  "User manual of Dapper"
date:   2016-10-14 23:00:00
categories: Translation
tags: Translation
comments: false
---
* content
{:toc}

Dapper - 一个.Net简单对象映射器
 <!--more-->

Release Notes
发布须知
Link

**Features**
特性

>Dapper is a NuGet library that you can add in to your project that will extend your IDbConnection interface.
>Dapper是一个Nuget库，通过将其添加进你自己的项目来拓展IDbConnection数据库连接接口。

It provides 3 helpers:
它在三个方面提供帮助：

-----

**Execute a query and map the results to a strongly typed List**
执行查询，并将返回结果映射为强类型的集合


Note: all extension methods assume the connection is already open, they will fail if the connection is closed.
注意：所有拓展方法假设数据库连接已经被打开，如果连接是关闭的所有的方法都将操作失败。

```
public static IEnumerable<T> Query<T>(
this IDbConnection cnn, string sql,
object param = null,
SqlTransaction transaction = null,
bool buffered = true)
```

Example usage:
使用实例：

```
public class Dog
{
    public int? Age { get; set; }
    public Guid Id { get; set; }
    public string Name { get; set; }
    public float? Weight { get; set; }

    public int IgnoredProperty { get { return 1; } }
}            

var guid = Guid.NewGuid();
var dog = connection.Query<Dog>("select Age = @Age, Id = @Id", new { Age = (int?)null, Id = guid });

dog.Count()
    .IsEqualTo(1);

dog.First().Age
    .IsNull();

dog.First().Id
    .IsEqualTo(guid);
```

-----

**Execute a query and map it to a list of dynamic objects**
执行一个查询，并将返回结果映射成为一个动态对象集合


```
public static IEnumerable<dynamic> Query (
this IDbConnection cnn, string sql,
object param = null,
SqlTransaction transaction = null,
bool buffered = true)
```
This method will execute SQL and return a dynamic list.
此方法将会执行SQL语句并返回一个动态的集合。

Example usage:
使用实例：

```
var rows = connection.Query("select 1 A, 2 B union all select 3, 4");

((int)rows[0].A)
   .IsEqualTo(1);

((int)rows[0].B)
   .IsEqualTo(2);

((int)rows[1].A)
   .IsEqualTo(3);

((int)rows[1].B)
    .IsEqualTo(4);
```

---
**Execute a Command that returns no results**
执行一条无返回结果的命令

```
public static int Execute(
this IDbConnection cnn,
string sql, object param = null,
SqlTransaction transaction = null)

```

Example usage:
使用实例：

```
connection.Execute(@"
  set nocount on
  create table #t(i int)
  set nocount off
  insert #t
  select @a a union all select @b
  set nocount on
  drop table #t", new {a=1, b=2 })
   .IsEqualTo(2);
```

---
**Execute a Command multiple times**
多次执行一条同样的命令

The same signature also allows you to conveniently and efficiently execute a command multiple times (for example to bulk-load data)
相同的签名还允许你方便和高效的多次执行一条命令（比如批量加载数据）

Example usage:
使用实例：

```
connection.Execute(@"insert MyTable(colA, colB) values (@a, @b)",
    new[] { new { a=1, b=1 }, new { a=2, b=2 }, new { a=3, b=3 } }
  ).IsEqualTo(3); // 3 rows inserted: "1,1", "2,2" and "3,3"
```

This works for any parameter that implements IEnumerable for some T.
这种使用方式对于任何实现了IEnumerable接口的对象参数都有效。

---
**Performance**
性能

A key feature of Dapper is performance. The following metrics show how long it takes to execute 500 SELECT statements against a DB and map the data returned to objects.
Dapper的一个关键特性就是其性能。下面的矩阵图展示了针对同一数据库在执行500条查询语句并将结果映射到返回对象的时间对比。

The performance tests are broken in to 3 lists:
性能测试被分成3个表格：



    POCO serialization for frameworks that support pulling static typed objects from the DB. Using raw SQL.
    在支持从数据库到静态类型对象转化的框架下执行POCO序列化。使用原生SQL。

    Dynamic serialization for frameworks that support returning dynamic lists of objects.
    在支持返回动态对象集合的框架下执行动态序列化操作。

    Typical framework usage. Often typical framework usage differs from the optimal usage performance wise. Often it will not involve writing SQL.
    典型框架使用。通常的框架使用与最佳使用性能明显不同。通常情况下这不涉及到SQL语句的编写。



Performance of SELECT mapping over 500 iterations - POCO serialization
查询和映射超过500次的性能 - POCO序列化


|	Method 	|	Duration	|	Remarks	|
| ------------- |:------------| -----|
|Hand coded (using a SqlDataReader) |47ms|Can be faster|
|DapperExecuteMapperQuery |49ms| |
|ServiceStack.OrmLite (QueryById)|50ms| |
|PetaPoco|52ms| |
|BLToolkit |80ms| |
|SubSonic CodingHorror|107ms| |
|NHibernate SQL |104ms| |
|Linq 2 SQL ExecuteQuery |181ms| |
|Entity framework ExecuteStoreQuery|631ms| |


----------


Performance of SELECT mapping over 500 iterations - dynamic serialization
查询和映射超过500次的性能 - 动态序列化


|	Method 	|	Duration	|	Remarks	|
| ------------- |:------------| -----|
|Dapper ExecuteMapperQuery (dynamic) |48ms| |
|Massive |52ms| |
|Simple.Data |50ms| |


----------


Performance of SELECT mapping over 500 iterations - typical usage
查询和映射超过500次的性能 - 典型使用


|	Method 	|	Duration	|	Remarks	|
| ------------- |:------------| -----|
|Linq 2 SQL CompiledQuery| 81ms|Not super typical involves complex code|
|NHibernate HQL |118ms| |
|Linq 2 SQL|559ms| |
|Entity framework | 859ms| |
|SubSonic ActiveRecord.SingleOrDefault | 3619ms | |

Performance benchmarks are available here.
性能测试代码链接：。。


Feel free to submit patches that include other ORMs - when running benchmarks, be sure to compile in Release and not attach a debugger (ctrl F5).
请各位踊跃提交包含其他ORM框架的测试数据 - 在运行测试比较时，一定要确认代码在Release模式下编译，请勿使用调试器进行
（ctrl F5）。


Alternatively, you might prefer Frans Bouma's RawDataAccessBencher test suite or OrmBenchmark.
另外，你也许更倾向于使用弗兰斯博马的RawDataAccessBencher的测试套件或者OrmBenchmark。

----

**Parameterized queries**
带参数的查询请求

Parameters are passed in as anonymous classes. This allow you to name your parameters easily and gives you the ability to simply cut-and-paste SQL snippets and run them in Query analyzer.
参数通过匿名类进行传递。这种方式允许你很容易的对你的参数进行命名并且让你可以通过简单的复制粘贴原生SQL片段，然后在查询分析器中进行运行。


```
new {A = 1, B = "b"} // A will be mapped to the param @A, B to the param @B
```

-----

**List Support**
集合的支持

Dapper allow you to pass in IEnumerable and will automatically parameterize your query.
Dapper允许你传递可枚举的数据并自动将你的查询参数化。

For example:
比如：

```
connection.Query<int>("select * from (select 1 as Id union all select 2 union all select 3) as X where Id in @Ids", new { Ids = new int[] { 1, 2, 3 } });
```
Will be translated to:
将会被翻译成：

```
select * from (select 1 as Id union all select 2 union all select 3) as X where Id in (@Ids1, @Ids2, @Ids3)" // @Ids1 = 1 , @Ids2 = 2 , @Ids2 = 3
```

-----
**Buffered vs Unbuffered readers**
带缓冲的和不带缓冲的读取器

Dapper's default behavior is to execute your sql and buffer the entire reader on return. This is ideal in most cases as it minimizes shared locks in the db and cuts down on db network time.
Dapper的默认行为是执行你的sql语句然后将整个读取器返回缓冲。在大部分情况下这是一种理想状态因为这种方式将共享数据库锁死出现的情况降到最低并减少数据库连接时间。


However when executing huge queries you may need to minimize memory footprint and only load objects as needed. To do so pass, buffered: false into the Query method.
然而当你在执行大数据查询的时候，你也许需要清除内存记录，仅仅加载需要的对象。为了实现这种方式讲buffered: false参数设置到查询方式中。

-----
**Multi Mapping**
多数据映射

Dapper allows you to map a single row to multiple objects. This is a key feature if you want to avoid extraneous querying and eager load associations.
Dapper允许你将一行单独的返回数据映射进多对象。这是一个关键的特性，如果你想要避免无关查询和急加载关联。

Example:
比如：

Consider 2 classes: Post and User
考虑2个类：Post和User类

```
class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
    public User Owner { get; set; }
}

class User
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Now let us say that we want to map a query that joins both the posts and the users table. Until now if we needed to combine the result of 2 queries, we'd need a new object to express it but it makes more sense in this case to put the User object inside the Post object.
现在假设我们想执行一个查询讲posts表和users表进行关联。目前如果我们需要将两个查询的结果进行合并，我们需要一个新的对象去表示。但是在此例子中将User对象放到Post对象内部是更合适的方式。


This is the user case for multi mapping. You tell dapper that the query returns a Post and a User object and then give it a function describing what you want to do with each of the rows containing both a Post and a User object. In our case, we want to take the user object and put it inside the post object. So we write the function:
这是多数据映射的使用情况。你告诉dapper查询需要返回一个post对象和一个user对象并给定一个函数用来描述你想在数据行中对post和user对象进行怎样的处理。在我们的例子中，我们想要获取一个user对象并将user对象放post对象内部。所以我们用下面的函数表示：


```
(post, user) => { post.Owner = user; return post; }
```

The 3 type arguments to the Query method specify what objects dapper should use to deserialize the row and what is going to be returned. We're going to interpret both rows as a combination of Post and User and we're returning back a Post object. Hence the type declaration becomes
这查询语句的三个的参数详细描述了dapper应该将行数据进行怎样的反序列化，和应该返回什么数据。我们将会同时返回包含post和user的行数据，并且我们会返回一个post对象。然后类型的声明变成了

```
<Post, User, Post>
```

Everything put together, looks like this:
所有的这些放到一起，看上去就是这样：

```
var sql =
@"select * from #Posts p
left join #Users u on u.Id = p.OwnerId
Order by p.Id";

var data = connection.Query<Post, User, Post>(sql, (post, user) => { post.Owner = user; return post;});
var post = data.First();

post.Content.IsEqualTo("Sams Post1");
post.Id.IsEqualTo(1);
post.Owner.Name.IsEqualTo("Sam");
post.Owner.Id.IsEqualTo(99);

```

Dapper is able to split the returned row by making an assumption that your Id columns are named Id or id, if your primary key is different or you would like to split the wide row at point other than Id, use the optional splitOn parameter.
Dapper可以通过假设你的Id列名是Id或者id，将返回的数据进行分离，如果你的主键不是这个名称或者你希望在另一个你制定的参数而不是Id列进行拆分，请使用可选的splitOn参数。

-----
**Multiple Results**
多结果返回

Dapper allows you to process multiple result grids in a single query.
Dapper允许你通过单查询语句处理多个返回结果。

Example:
比如：

```
var sql =
@"
select * from Customers where CustomerId = @id
select * from Orders where CustomerId = @id
select * from Returns where CustomerId = @id";

using (var multi = connection.QueryMultiple(sql, new {id=selectedId}))
{
   var customer = multi.Read<Customer>().Single();
   var orders = multi.Read<Order>().ToList();
   var returns = multi.Read<Return>().ToList();
   ...
}
```

-----
**Stored Procedures**
存储过程

Dapper fully supports stored procs:
dapper完全支持存储过程：

```
var user = cnn.Query<User>("spGetUser", new {Id = 1},
        commandType: CommandType.StoredProcedure).SingleOrDefault();
```

If you want something more fancy, you can do:
如果你想写的更加骚气，你可以这样做：


```
var p = new DynamicParameters();
p.Add("@a", 11);
p.Add("@b", dbType: DbType.Int32, direction: ParameterDirection.Output);
p.Add("@c", dbType: DbType.Int32, direction: ParameterDirection.ReturnValue);

cnn.Execute("spMagicProc", p, commandType: CommandType.StoredProcedure);

int b = p.Get<int>("@b");
int c = p.Get<int>("@c");
```

-----
**Ansi Strings and varchar**


Dapper supports varchar params, if you are executing a where clause on a varchar column using a param be sure to pass it in this way:
dapper支持可变字符串参数，如果你正在通过一个varchar类型的列进行查询操作，请用下面的方式进行参数传递：


```
Query<Thing>("select * from Thing where Name = @Name", new {Name = new DbString { Value = "abcde", IsFixedLength = true, Length = 10, IsAnsi = true });
```

On SQL Server it is crucial to use the unicode when querying unicode and ansi when querying non unicode.
在SQL server上如果查询非unicode数据时，使用了unicode字符将会发生严重错误。

-----
**Limitations and caveats**
局限和注意事项

>Dapper caches information about every query it runs, this allow it to materialize objects quickly and process parameters quickly. The current implementation caches this information in a ConcurrentDictionary object. The objects it stores are never flushed. If you are generating SQL strings on the fly without using parameters it is possible you will hit memory issues. We may convert the dictionaries to an LRU Cache.

>dapper会对每一个执行的查询信息进行缓存，这样的方式允许快速的实例化对象和迅速处理参数。目前的实现机制会将信息缓存到一个ConcurrentDictionary对象中。被缓存的对象永远不会被释放。如果你自己动态生成SQL字符串而不使用参数，你也许会遇到内存的问题。我们也许会将dictionaries转换成LRU缓存。

---
>Dapper's simplicity means that many feature that ORMs ship with are stripped out. It worries about the 95% scenario, and gives you the tools you need most of the time. It doesn't attempt to solve every problem.

>dapper的简洁性意味着其他常见ORM框架的特性都被剔除了。它只是用来处理常见的95%的场景，并且提供你大部分时间都需要的一个工具。然而它并不能用来处理解决所有的问题。

----
**Will Dapper work with my DB provider?**

Dapper has no DB specific implementation details, it works across all .NET ADO providers including SQLite, SQL CE, Firebird, Oracle, MySQL, PostgreSQL and SQL Server.


Do you have a comprehensive list of examples?
