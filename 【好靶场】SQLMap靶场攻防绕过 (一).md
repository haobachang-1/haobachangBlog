# 0x00 前言
最近遇到很多在做基础靶场的小伙伴们都在SQLMap一把索，那么所幸搞一个SQLMap绕过的靶场。

我们是好靶场，一个立志于让所有学习安全的同学用上好靶场的团队。

[https://github.com/haobachang-1/haobachangBlog/](https://github.com/haobachang-1/haobachangBlog/)

[https://github.com/haobachang-1/haobachangBlog/tree/main/SQLMap%E6%94%BB%E9%98%B2](https://github.com/haobachang-1/haobachangBlog/tree/main/SQLMap%E6%94%BB%E9%98%B2)

# 0x01 开启环境
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482051705-b19767d5-6662-4144-aa55-58b4387f3561.png)

## 进入环境
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482106513-c366ea3a-757b-4700-92d0-87950e8cca58.png)

如果要自己尝试的伙伴们可以从这里开始自己尝试了。

# 0x02 开工
输入admin，看看效果

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482157550-cffc4110-f6e7-408a-8fc0-17e09a705b09.png)

输入单引号，有报错，那基本上就是有SQL注入

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482181673-071097da-82c6-41b5-9c32-d9c48c8f9609.png)

单引号闭合，发现是存在漏洞的

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482255950-403f14a6-be78-40c4-a8f8-42b833ffd57e.png)

来叭，SQLMap，交给你了

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482335498-9855293b-825e-41fb-900e-bdcfd4df1bff.png)

哦豁，失败了，SQLMap并没有正常的工作

# 0x03 绕过
从我们的扫描结果可以看出来，页面是返回了404，可能是因为检测了什么内容。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482374276-5c340712-1d7e-47f3-be97-a38d76f4e5fd.png)

根据过往经验，最好去识别的是SQLMap的UA特征。SQLMap默认情况下UA就是SQLMap。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482460826-0de912a7-d760-4549-ad98-cff4a18ea676.png)

我们进行一个简单的测试，切换为SQLMap的UA，发现果然，是检查了UA。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482532307-94e92e8d-9353-42b2-b7a0-7bf6510a7fee.png)

那我们就需要去绕过了，SQLMap有一个自带的随机UA的功能。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482611249-0db77fad-d718-4510-9cda-8540118c27d8.png)

我们使用这个试试，ok成功了。

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757482884020-62ad2614-f37c-4d4a-92f9-ca7a05c6b88f.png)

拿一下Flag

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757483086011-077c6c33-8d57-47d7-9272-c427325b224b.png)

# 徽章到手
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757483493560-cc5714d2-edeb-4372-9ea9-039e4afb458b.png)

# 备注：
 SQLMap 是一款开源的自动化 SQL 注入检测与利用工具，主要用于帮助安全测试人员、渗透测试工程师等在合法授权的前提下，检测 Web 应用程序中是否存在 SQL 注入漏洞，并对已发现的漏洞进行深度利用，以评估系统的安全风险。  

