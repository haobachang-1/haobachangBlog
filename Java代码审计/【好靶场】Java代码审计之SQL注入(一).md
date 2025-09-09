# 0x00 前言
从来没有那种很好用的靶场来学习Java代码审计，所以它来了，我们今天就会通过这个靶场来学习代码审计的内容。

这里是好靶场，我们会做出好的网络安全靶场。学了不练，等于白练，做练习就用好靶场。

[https://github.com/haobachang-1/haobachangBlog/blob/main/README.md](https://github.com/haobachang-1/haobachangBlog/blob/main/README.md)

本文需要在有一定Java基础的前提下进行,且掌握了基本的SQL手注的方式。

# 0x01 开启环境
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757394668450-520375c9-28e8-4ff3-8e9e-e5e313a45b88.png)

开启环境

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757394781010-4accbeea-c9f3-400e-95cd-9ede390a213d.png)

访问一下

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757395373472-eb26a868-2663-4d8f-8692-8d1f973ef7fc.png)

# 0x02 审计利用分析
首先我们来看一下存在漏洞的源码，可以看到SQL语句是通过 `+` 号去拼接的

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757395442156-3a4862b9-f979-4575-bd12-0a698ee565d2.png)



那么如果是`+`号拼接的话就会出现输入的`'`将前面的语句闭合，从而导致存在SQL注入。

根据提示进行测试，可以看到SQL注入是真实存在的。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757395541245-840ba564-35c1-4383-a616-df587b9a035c.png)

接下来就需要各位去进行手注的练习了，比如order by 判断显示，等等，我们这里查询一个数据库名称。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757395643888-fd586a0a-d8c5-47f0-a366-8d7148e05866.png)

查询Flag的事情就交给各位了。

## 徽章到手：
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757396081365-6269025c-8eb5-4abf-a0ad-d3e149a46b8a.png)

# 0x03 修复
我们再来看看安全代码。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757395724803-0c972e6f-db7c-4687-852d-84e415d1178a.png)

安全代码使用了 PreparedStatement  预编译，从而规避了SQL注入。一般情况下预编译是可以规避大部分的SQL注入的。

## 科普：
`PreparedStatement` 是 Java JDBC 中的一个接口（继承自 `Statement`），主要用于执行**预编译的 SQL 语句**，其核心原理围绕 “预编译” 和 “参数化查询” 展开，相比普通的 `Statement` 具有性能优化和安全增强的特点。  

 使用 `PreparedStatement` 时，SQL 语句中用 `?` 作为占位符代替具体参数（例如 `SELECT * FROM user WHERE id = ?`），这条 “带占位符的 SQL 模板” 会被提前发送到数据库服务器。  

 数据库收到后，会对 SQL 进行**语法解析、语义检查、生成执行计划**（这一过程称为 “预编译”），并将编译后的执行计划缓存起来。  

# 0x04 总结
大家看了这个案列应该就知道一般Java中SQL注入的样子了吧，这里有一个潜在的内容，就是如何快速的在一个项目中发现SQL注入，大家可以进行通篇的查询` executeQuery  `关键字。

我们在他的前后去找执行的SQL语句。从而达到快速发现SQL注入的目的。![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757395928528-2563d72e-adec-42f3-9e91-7c1919e0c881.png)

