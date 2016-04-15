title: "Java之JDBC连接MySql的一点坑"
date: 2016-04-15 23:51:11
tags: [MySql]
categories: Programming Notes

---
<blockquote  class="blockquote-center">
**对代码不满足，是任何真正有天才的程序员的根本特征。**
</blockquote>

####getTimestamp()报错can not be represented
在**MySql**中对于**timestamp**类型的列，如果设置**not null**的话，其默认值是**0000-00-00**；如果**timestamp**的列默认值是**null**的话，那么**0000-00-00**和**null**值在数据库中都是不显示，也就是说在数据库中不管其默认值是**not null**还是**null**，该列值显示的都是空，你无法根据值去判断其类型，并且当是**not null**的时候，你取出来的值实际上是**0000-00-00**，所以使用**resultSet.getTimestamp()**方法会报错，**java.sql.SQLException: Value '0000-00-00' can not be represented as java.sql.Timestamp**。

另外**timestamp**类型要求第一个出现的**timestamp**列必须是**not null default current_timestamp**。

在**Java**中如果取到这种**0000-00-00**数据的话，转成**timestamp**会报错，解决办法是修改**jdbc url**为：
> jdbc:mysql://yourserver:3306/yourdatabase?zeroDateTimeBehavior=convertToNull

####resultSet.getRow()总是返回0
在使用**resultSet.getRow()**的时候，发现总是返回0，注意该方法返回的是数据库当前的行数，所以当你没调用**resultSet.next()**的时候，该方法总是返回0。

####resultSet.getFetchSize()总是返回0
另外使用**resultSet.getFetchSize()**的时候也是每次都返回0，这是怎么回事呢？**getFetchSize()**方法不是获得记录数，而是获得每次抓取的记录数，默认是0，也就是说不限制。可以用**setFetchSize()**来设置，而**getFetchSize()**是用来读出那个设置值。**setFetchSize()**最主要是为了减少网络交互次数设计的，访问**ResultSet**时，如果它每次只从服务器上取一行数据，则会产生大量的开销，**setFetchSize()**的意思是当调用**resultSet.next()**时，**resultSet**会一次性从服务器上取多行数据回来，这样在下次**resultSet.next()**时，它可以直接从内存中获得数据，而不需要网络交互，提高了效率。

**Note**：有时候觉得英文确实表述更准确，请看原汁原味的解释
The fetch size is the number of rows that should be retrieved from the database in each roundtrip. It has nothing to do with the number of rows returned.

####获取查询的数据集的行数
如果你是想获得符合条件的记录数目，最少有三种方法
1. 自己维护计数器
```java
int count = 0;
while(resultSet.next()){
  count++;
}
```
2. 可以这样
```java
resultSet.last();
rowCount = resultSet.getRow();
resultSet.beforeFirst();//将数据集重新归位，这样就获取到行数了
```
3. 利用SQL查询
```java
String sql = "select count(*) totalCount from table";
rowCount = resultSet.getInt("totalCount");
```
