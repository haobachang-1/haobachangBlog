# 0x00 概述
好靶场是一个网络安全靶场平台，我们立志于做出让所有安全人员都好用的靶场。

[https://github.com/haobachang-1/haobachangBlog/tree/main](https://github.com/haobachang-1/haobachangBlog/tree/main)

# 0x01 漏洞解析
<font style="color:rgba(0, 0, 0, 0.85) !important;">发包多次获得优惠券漏洞是营销活动中常见的业务逻辑漏洞，主要因系统未对优惠券领取请求进行有效限制，导致攻击者通过重复发送请求（即 "重复发包"）绕过领取规则，获取远超正常数量的优惠券，进而利用这些优惠券进行不正当消费。</font>

<font style="color:rgba(0, 0, 0, 0.85) !important;">具体表现与危害：</font>

+ **<font style="color:rgb(0, 0, 0) !important;">漏洞成因</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：系统在设计优惠券领取功能时，未对用户的领取次数、领取频率进行严格限制，或仅在前端进行限制而未在后端实现校验逻辑。攻击者可通过抓包工具（如 Burp Suite）捕获领取优惠券的请求包，然后通过工具重复发送该请求，实现无限次领取。</font>
+ **<font style="color:rgb(0, 0, 0) !important;">典型场景</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：某平台推出 "新用户首次登录送 1 张满 100 减 50 优惠券" 活动，攻击者注册一个新用户后，通过重复发送领取请求，可能一次获得 100 张甚至更多同类型优惠券，随后通过拆分订单等方式使用这些优惠券，大幅降低消费成本。</font>
+ **<font style="color:rgb(0, 0, 0) !important;">危害后果</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：导致优惠券发放数量失控，远超活动预算；破坏营销活动的公平性，影响正常用户体验；大量优惠券被恶意使用会直接造成平台经济损失，严重时可能导致营销活动提前终止。</font>

下图为乌云找到的漏洞。

![](https://cdn.nlark.com/yuque/0/2025/png/8420228/1755435857123-5547f4c5-c745-44ec-88eb-d738917119fc.png)

# 0x02 漏洞环境
![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1757743307401-9cb9fe7d-66ff-4e49-9b17-769f82796f01.png)

# 0x03 漏洞复现
浏览器打开靶场连接，可以看到一个优惠卷领取页面

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755244113168-99aecd44-232e-45e4-9dd3-f2592f774902.png)

点击立即领取，可以领取优惠卷，且已经领取过后的优惠卷无法再进行领取

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755244247279-0edcba99-4c6f-435f-8cff-e301ab20d0d4.png)

点击我的优惠券，可以查看已经领取的优惠卷

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755244434491-dce31846-b4ce-46d8-bc8f-51b200eb2aee.png)

获取领取优惠卷的数据包

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755244610531-fa7c762f-9cef-4c22-95cc-01d86d318fab.png)

对数据包进行重放，找到flag

![](https://cdn.nlark.com/yuque/0/2025/png/50745682/1755244629159-e3976646-43f4-424a-9373-ed553b953d8c.png)

# 0x04 漏洞修复方法
**<font style="color:rgb(0, 0, 0) !important;">严格的领取规则与后端校验</font>**

    - **<font style="color:rgb(0, 0, 0) !important;">明确领取限制条件</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：在后端代码中明确优惠券的领取规则，如 "每个用户限领 1 张"、"每个设备限领 1 张"、"活动期间限领 3 张" 等，避免仅依赖前端按钮灰化等表面限制。</font>
    - **<font style="color:rgb(0, 0, 0) !important;">基于唯一标识限制</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：以用户 ID（登录状态）或设备指纹（未登录状态）作为唯一标识，在发放优惠券前，后端查询该标识的历史领取记录，若已达到领取上限，则直接拒绝新请求并返回明确提示（如 "您已达到该优惠券的领取上限"）。</font>
    - **<font style="color:rgb(0, 0, 0) !important;">请求幂等性处理</font>**<font style="color:rgba(0, 0, 0, 0.85) !important;">：为每个领取请求生成唯一的幂等标识（如 UUID），前端在发送请求时携带该标识，后端记录已处理的标识，对于重复的标识请求直接返回之前的处理结果，防止因网络延迟等导致用户重复提交。</font>





