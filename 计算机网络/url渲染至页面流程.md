# 输入url到页面加载的全过程

[没有缓存部分但是其他地方描述很详细](https://blog.csdn.net/weixin_45629285/article/details/120930370?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167888430416800222835439%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=167888430416800222835439&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-19-120930370-null-null.142^v73^insert_down4,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=%E8%BE%93%E5%85%A5url%E5%88%B0%E6%98%BE%E7%A4%BA%E7%9A%84%E5%85%A8%E8%BF%87%E7%A8%8B&spm=1018.2226.3001.4187)

## URL

`http://www.baidu.com`

这个域名由三部分组成，协议名、域名和端口号组成，端口号默认就隐藏了

除此之外URL还包含一些常见的路径，查询和其他片段。（**请求参数，另学**）。

协议有HTTP，加密的是HTTPS，FTP协议，FILE协议等。URL中间部分为域名或者是IP，之后就是端口号了。通常端口号不常见是因为大部分的使用都是默认端口，如HTTP默认端口是80，HTTPS默认端口为443

## 查询缓存

浏览器需要找到这个url域名的服务器ip，浏览器首先会寻找缓存，查看缓存中是否有记录。缓存的记录为：`浏览器缓存-》 系统缓存-〉路由器缓存`（缓存中的记录为页面）,缓存中没有则查找系统中的host文件中是否有缓存记录

**缓存机制？**

## DNS服务器

域名解析，如果没有缓存则查询DNS服务器，得到服务器的ip地址后，浏览器根据这个ip以及相应的端口号发送连接请求；如果DNS服务器中没有解析成功，他会向上一步获得的顶级DNS服务器发送解析请求。

**端口号哪里来的，上一步获得的顶级DNS服务器如何发送解析请求？**

![img](https://img-blog.csdnimg.cn/697fb6e8780b4a6d94816ebc4ffb3447.png)

详细版：

在输入url后，用户pc中的DNS客户端进程会发送一个DNS查询请求报文，其内容为**域名所对应的IP地址是什么？**

DNS 的查询请求报文需要使用运输层的UDP协议封装成UDP用户数据报，其首部中的源端口字段值在短暂端口号49151~65535中挑选一个未被占用的端口号用来表示DNS客户端进程进程，例如49152。目的端口字段的值设置为53，这是DNS服务器端进程所使用的熟知端口号。

![在这里插入图片描述](https://img-blog.csdnimg.cn/27b57008454c487785db4abf4a9ec913.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p6X5rex5pe25LiN6KeB6bm_,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

之后用户PC将UDP用户数据报封装在IP数据报中通过以太网发送给DNS服务器。

DNS服务器端收到该数据报后，从中解封出UDP用户数据报

UDP首部中的目的端口号为53，这表明应该将UDP用户数据报的数据荷载部分，也就是DNS查询请求报文，交付给本服务器中的DNS服务器端进程。DNS服务器解析DNS查询请求报文的内容，然后按照要求查找对应的IP地址。

首先，查询请求会先找到本地DNS服务器来查询是否包含IP地址，如果本地DNS无法查询到目标IP地址，就会向根据名服务器发起一个DNS查询。

>如果跟域名服务器无法告知本地DNS服务器下一步要访问哪一个顶级域名服务器，就会使用递归查询。
>
>如果跟域名服务器能告知DNS服务器下一步需要访问的顶级域名服务器，就会使用迭代查询。

再由根域名服务器->顶级域名服务器->权威DNS服务器后，由权威服务器告诉本地服务器目标IP地址，再由本地DNS服务器告诉用户需要访问的IP地址。

![img](https://img-blog.csdnimg.cn/a7fd1d67f568401fa0e36821b75aa44e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p6X5rex5pe25LiN6KeB6bm_,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

得知目标IP地址之后，DNS服务器会给用户PC发送DNS的响应报文，其内容为域名www.porttest.com 所对应的IP地址是192.168.0.3。

DNS响应报文需要使用运输层的UDP协议封装成UDP用户数据报，其首部中的原端口字段的值设置为熟知端口号53，表明这是DNS 服务器端进程所发送的UDP 用户数据报，目的端口字段的值设置为49152，这是之前用户PC中发送DNS查询请求报文的DNS客户端进程所使用的短暂端口号。
![在这里插入图片描述](https://img-blog.csdnimg.cn/d6444402e4924ff898ac1889e0418d9c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p6X5rex5pe25LiN6KeB6bm_,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

DNS 服务器之后将UDP用户数据报封装在IP 数据报中，通过以太网发送给用户PC。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5eb985b704734469b01c7b17aef3d0a6.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p6X5rex5pe25LiN6KeB6bm_,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

用户PC收到该数据报后，从中解封出UDP用户数据报。UDP首部中的目的端口号为49452，这表明应将该UDP用户数据报的数据载荷部分，也就是DNS的响应报文交付给用户PC中的DNS客户端进程。DNS客户端进程解析DNS响应报文的内容，就可知道自己之前所请求的外部服务器的域名所对应的IP地址为192.168.0.3。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a8d7d92ba25d4442a1c61889122f7fb2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBA5p6X5rex5pe25LiN6KeB6bm_,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## TCP三次握手

可以简化为：

1. 浏览器发送一个请求 ACK = 1
2. 服务器允许连接后发送ACK报文给浏览器
3. 浏览器接受ACK后并向后段发送一个ACK，TCP连接建立成功

**回去再看一遍三次握手！！！！**

## HTTP协议包

构造一个http请求，这个请求报文包括这次请求，主要是请求方法，请求说明和请求附带的数据，并将这个http请求封装在一个tcp包中；这个TCP包也就是会依次经过传输层，网络层，数据链路层，物理层到达服务器，服务器解析这个请求来做出响应，返回相应的html给服务器。

## 浏览器处理HTML文档

因为HTML是一个树形结构，浏览器根据这个html来构建DOM树，在dom树的构建过程中如果遇到JS脚本和外部JS连接，则会停止构建DOM树来执行和下载相应的代码，这会造成阻塞，**这就是为什么推荐JS代码应该放在html代码的后边；**

meta标签和link标签的作用

![img](https://img-blog.csdnimg.cn/4840563c69e642d9aa0f7ff69e2f18c9.png)

**渲染树**

之后根据外部样式，内部样式，内联样式构建一个cssom树，构建完成后和dom树合并为渲染树，在排除非视觉节点，比如script，meta标签和排除display为none的节点，之后进行布局，布局主要是确定各个元素的位置和尺寸，之后是渲染页面，因为html文件中会含有图片，视频，音频等资源，在解析DOM过程中，遇到这些都不会并行下载，浏览器对每一个域的并行下载数量有一定的限制，一般是4-6个，当然这些所有的请求中我们还需要关注的就是缓存，缓存一般通过Cache-Control、last- Modify、expires等首部字段控制。

**什么是域？缓存机制是什么**



**Cache-control和Expires的区别**

在于Cache-Control使用相对时间，Expires使用的是基于服务器端的绝对时间，因为存在时间差问题，一般采用Cache-Control，在请求这些有设置了缓存的数据时，会先查看是否过期，如果没有过期则直接使用本地缓存，过期则请求并在服务器校验文件是否修改，如果上一次相应设置了ETag值会在这次请求的时候作为If-None-Match的值交服务器校验，如果一致，继续校验Last-Modified，没有设置ETag则直接验证Last-Modified，再决定是否返回304



## 关闭TCP连接，四次挥手



## 附加

### TCP和UDP的区别

