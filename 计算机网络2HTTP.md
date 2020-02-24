### 基础概念

#### URI

URI

Uniform Resource Identifier 统一资源标识符
Uiform Resource Locator 统一资源定位符
Uniform Resource Name 统一资源名称

#### 请求和响应报文

1，请求报文
![image-20200218231032906](/Users/jacyn/Documents/study/Learning-materials/image/image-20200218231032906.png)

2.响应报文
![image-20200218231150784](/Users/jacyn/Documents/study/Learning-materials/image/image-20200218231150784.png)

#### HTTP方法

请求报文第一行 请求行 包含方法字段

1. GET
   获取资源

2. HEAD
   获取报文首部：确认URL有效性及资源更新的日期时间

3. POST

   传输数据

4. PUT

   上传文件，不带验证机制，任何人可传存在安全问题

5. PATCH

   对资源部分修改（PUT完全替代）

   ```
   PATCH /file.txt HTTP/1.1 
   Host: www.example.com 
   Content-Type: application/example 
   If-Match: "e0023aa4e" 
   Content-Length: 100
   
   [description of changes]
   ```

6. DELETE

   删除文件，不带验证机制

   ```
   DELETE /file.html HTTP/1.1
   ```

7. OPTIONS

   查询指定URL能支持的方法

   会返回 Allow: GET, POST, HEAD, OPTIONS 这样的内容。

8. CONNECT

   要求与代理服务器通信时建立隧道

   如使用SSL(Secure SOcket Layer，安全套接层)

   ​	TLS(Transport LAyer Security，传输层安全)协议

   把通信内容加密后经过网络隧道传输

9. TRACE

   追踪路径：服务器返回通信路径

   Max-Forwards 首部字段中填入数值，每经过一个服务器就会减 1

   容易收到XST(Cross-Site Tracing )跨站追踪

#### HTTP状态码

响应报文第一行为状态行，包含状态码以及原因短语

| 状态码 | 类别                             | 含义                       |
| ------ | -------------------------------- | -------------------------- |
| 1XX    | Informational(信息性状态码)      | 接收的请求正在处理         |
| 2XX    | Success（成功状态码）            | 请求正常处理完毕           |
| 3XX    | Redirection（重定向状态码）      | 需要进行附加操作以完成请求 |
| 4XX    | CLient Error（客户端错误状态码） | 服务器无法处理请求         |
| 5XX    | Server Error（服务器错误状态码） | 服务器处理请求出错         |

1. 1XX信息
   100 COntinue：到目前为止正常

2. 2XX 成功
   200 ok
   204 No Content：请求亿成功处理，返回效应报文不包含实体的主体部分；用于向服务器只需发送信息，不需返回数据

   206 Partial Content：客户端进行范围请求，响应报文包含由Content-Range指定范围的实体内容

3. 3XX 重定向
   301 Moved premanently 永久重定向
   302 Found 临时性重定向
   303 See Other 同上，要求客户端改成GET
   大多数都会在301，302，303时把POST改为GET
   304 Not Modified 请求报文若含有If-Match，If-Modiﬁed-Since，If-NoneMatch，If-Range，If-Unmodiﬁed-Since条件，若不满足则返回304
   307 Temporary Redirect 临时重定向，类似302，不把post改get

4. 4XX 客户端错误
   400 Bad Request 请求报文存在语法错误
   401 Unauthorized 发送请求中需要有认证信息（BASIC 认证、DIGEST 认证）；若之前已执行过一次请求，表示用户认证失败
   403 Forbidden 请求被拒绝
   404 Not Found

5. 5XX 服务器错误
   500 Internal Server Error 服务器正在执行请求时出错
   503 Server Unavailable 服务器处于超负载或正在停机维护，无法处理请求

#### HTTP首部

##### 通用首部字段

![image-20200219202629573](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219202629573.png)

##### 请求首部字段

![image-20200219202659025](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219202659025.png)

##### 响应首部字段

![image-20200219204612299](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219204612299.png)

##### 实体首部字段

![image-20200219204628891](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219204628891.png)

#### 具体应用

##### 连接管理

![image-20200219205330767](/Users/jacyn/Documents/study/Learning-materials/image/image-20200219205330767.png)

1. 短连接
   进行一次HTTp通信，新建一个TCP连接，开销大
2. 长连接
   建立一次TCP连接进行多次HTTP通信
   HTTP/1.1开始默认长连接，断开使用connection：close
   之前默认短连接，Connection：keep-Alive使用长连接
3. 流水线
   在同一条长连接上连续发出请求，buoying等待响应返回，减少延迟

##### Cookie

HTTP协议无状态(简单，可处理大量事务)--->HTTP/1.1后引入cookie保存状态信息
**Cookie**：服务器发送到用户浏览器并保存在本地的一小块数据，在浏览器向同一服务器再次发起i去那个i去时被携带--->告知服务端请求来自同一浏览器--->梅西请求需携带cookie，额外新能开销

###### 用途

1. 会话状态管理（用户登录状态，购物车。。）
2. 个性化设置（自定义主题）
3. 浏览器行为跟踪（跟踪分析用户行为）

###### 创建过程

1. 服务器响应报文包含Set-Cookie首部字段-->客户端把Cookie内容保存在浏览器

   ```
   HTTP/1.0 200 OK Content-type: text/html Set-Cookie: yummy_cookie=choco Set-Cookie: tasty_cookie=strawberry
   
   [page content]客户端
   ```

2. 客户端之后对同一服务器发请求时，先从浏览器去除Cookie信息并通过Cookie请求首部字段发送给服务器

   ```
   GET /sample_page.html HTTP/1.1 Host: www.example.org Cookie: yummy_cookie=choco; tasty_cookie=strawberry
   ```

###### 分类

会话期Cookie：浏览器关闭-->自动删除，尽在会话期有效
持久性Cookie：指定过期时间（Expires）或有效期（max-age）之后成为持久性的Cookie

```
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```

###### 作用域

Domain标识指定哪些主机可以接受Cookie
不指定：默认为当前文档主机，不包含子域名
指定：包含子域名
Path标识：指定主机下的哪些路径可接受(该URL路径必须存在于请求URL中)

###### JavaScript

浏览器通过document.cookie属性可创建新的Cookie，或访问非HttpOnly标记的Cookie

```
document.cookie = "yummy_cookie=choco"; document.cookie = "tasty_cookie=strawberry"; console.log(document.cookie);
```

标记为HttpOnly的cookie--->不能被JavaScript脚本调用，一定程度上避免XSS(跨站脚本攻击，常使用JavaScript的document.cookie API窃取用户Cookie信息
标记为Secure的Cookie只能通过被HTTPS协议加密过的请求发送

###### Session

用户信息存储在服务器端
1.用户登录---提交用户名密码的表单---HTTP请求报文
2.服务器验证，正确则存入Redis，其key称为Session ID
3.服务器返回响应报文，其Set-Cookie首部字段包含Session ID，客户端保存Cookie
4.客户端再次发送，服务器提取Session ID，从Redis中提取用户信息
安全性问题：要求不能被轻易获取
浏览器禁用Cookie时，只能使用Session，且使用URL重写技术，将Session ID作为URL的参数传递

###### Cookie 与Session

1.Cookie只能存储ASCII，Session可存储任意类型--->数据复杂性首选Session
2.Cookie存储在浏览器，易被恶意查看---隐私出局非要存在Cookie中，将Cookie值加木，在服务器解密
3.大型网站 ，不建议所有用户信息存储在Session，开销极大

##### 缓存

**优点**：1.缓解服务器压力 2.降低客户端获取资源的延迟（缓存通常位于内存中，读取速度快）
**实现方法**：1.代理服务器缓存 2.客户端浏览器缓存
**Cache-Control**首部字段控制缓存 HTTP/1.1

```
1.禁止进行缓存
Cache-Control：no-store
2.强制确认缓存
规定缓存服务器先向源服务器验证缓存资源的有效性，有效时可用于响应
Cache-Control：no-cache
3.私有缓存：单独用户使用，存于用户浏览器
	Cache-Control: private
	公共缓存：多个用户使用，代理服务器
	Cache-Control: public
4.缓存过期机制
max-age
请求报文，缓存资源的缓存时间小于该指令指定时间，可接受缓存；
响应报文，表示缓存资源在缓存服务器中保存的时间
Cache-Control: max-age=31536000
Expires 首部字段：告知缓存server资源什么时候过期
Expires: Wed, 04 Jul 2012 08:26:05 GMT

在 HTTP/1.1 中，会优先处理 max-age 指令； 在 HTTP/1.0 中，max-age 指令会被忽略掉。
```

**缓存验证**

ETag首部字段：资源的唯一标识
缓存资源ETag放入If-None-Match首部--->服务器验证ETag，一致则有效，返回304 Not Modiﬁed
Last-Modiﬁed 首部字段：包含在源服务器发送的响应报文中，白表示源服务器中对该资源的最后修改时间，弱校验器，作为ETag备用。
若响应报文有上字段，客户端在请求中带入If-Modiﬁed-Since 来验证缓存。
			有修改：服务器返回资源，200 OK；
			无修改：返回304 Not Modiﬁed，且无实体主体

```
Last-Modified: Wed, 21 Oct 2015 07:28:00 GMT

If-Modified-Since: Wed, 21 Oct 2015 07:28:00 GMT
```

##### 内容协商

返回最合适的内容，例如根据浏览器默认语言返回对应界面

###### 类型

1. 服务端驱动型
   客户端设定特定HTTP首部字段，如Accept，Accept-Encoding，Accept-Language，服务器根据字段返回特定资源
   存在问题：
   服务器难以知道客户端浏览器全部信息
   客户端提供信息冗长(HTTP/2首部压缩机制)，存在隐私风险(HTTP指纹识别技术)
   给定资源需不同展现形式，共享缓存效率低，服务器端实现复杂
2. 代理驱动型
   服务器返回300 Multiple Choices 或者 406 Not Acceptable，客户端选择最佳

###### Vary

```
Vary: Accept-Language
```

使用内容协商的情况下，只有当那个缓存服务器的缓存满足内容协商条件时，才使用该缓存，否则向源服务器请求该资源。
如：一客户端发送一个包含Accept-Language首部字段的请求，源服务器返回响应包含Vary: Accept-Language，缓存服务器缓存该响应--->客户端下一次访问该URL资源时，Accept-Language 与缓存中的对应的值相同时才会返回该缓存。

###### 内容编码

压缩实体主体，减少传输数据量：gzip，compress，deflate，identity
（1）浏览器发送Accept-Encoding首部，包含压缩算法及各自优先级。
（2）服务器选择一种算法，对消息响应主体进行压缩，发送Content-Encoding告知浏览器选择的算法
（3）该内容协商基于编码类型选择资源展现形式，响应报文的Vary首部字段至少要包含Content-Encoding

###### 范围请求

若网络中断，服务器只发送一部分数据，范围请求可使客户端只请求服务器未发送的那部分数据，避免重新发送所有数据
1.Range 请求报文中添加，指定请求的范围

```
GET /z4d4kWk.jpg HTTP/1.1 
Host: i.imgur.com 
Range: bytes=0-1023
```

请求成功服务器返回响应包含206 Partial Content状态码

```
HTTP/1.1 206 Partial Content Content-Range: bytes 0-1023/146515 Content-Length: 1024 ...

(binary content)
```

2.Accept-Ranges 告知客户端是否能处理范围请求，可以使用bytes，否则用none

```
Accept-Ranges: bytes
```

3.响应状态码

```
在请求成功的情况下，服务器会返回 206 Partial Content 状态码。 
在请求的范围越界的情况下，服务器会返回 416 Requested Range Not Satisﬁable 状态码。
在不支持范围请求的情况下，服务器会返回 200 OK 状态码。
```

###### 分块传输代码

Chunked Transfer Encoding，可以把数据分割成多块，让浏览器逐步显示页面。

###### 多部分对象集合

 报文主体可含有多种类型实体发送，各部分用boundary字段定义分隔符分割

```
Content-Type: multipart/form-data; boundary=AaB03x

--AaB03x Content-Disposition: form-data; name="submit-name"

Larry --AaB03x Content-Disposition: form-data; name="files"; filename="file1.txt" Content-Type: text/plain

... contents of file1.txt ... --AaB03x--
```

###### 虚拟主机

HTTP/1.1使用，一台服务器拥有多个域名，逻辑上为多个服务器

###### 通信数据转发

1.代理
代理服务器接受客户端请求，转发给其他服务器
代理目的：
缓存；负载均衡；网络访问控制；访问日志记录
正向代理：用户可察觉

![image-20200224195934811](/Users/jacyn/Documents/study/Learning-materials/image/image-20200224195934811.png)

反向代理：位于内部网络中，用户无法察觉

![image-20200224195949418](/Users/jacyn/Documents/study/Learning-materials/image/image-20200224195949418.png)

2.网关
将HTTP转化为其他协议进行通信，从而请求其他非HTTP服务器的服务
3.隧道
使用SSL等加密手段，在客户端和服务器之间建立安全的通信网络

##### HTTPS

HTTP问题：
1.明文通信，易被窃听
2.不验证通信方身份，身份可能被伪装
3.无法验证报文完整性，报文可能被篡改
HTTPS：HTTP先和SSL(Secure Sockets Layer)通信，再有SSL和TCP通信--->即HTTPS使用隧道通信
SSL：加密（防窃听），认证（防伪装），完整性保护（防篡改）

###### 加密

1.对称密码加密
运算速度快
无法安全地将密钥传输给通信方
2.非对称加密
加密，签名
可以更安全的将公开密钥传输给通信发送方
运算速度慢
3.HTTPS加密方式--混合的加密机制
非对称密钥加密传输对称密钥保证传输安全性
对称密钥加密进行通信保证传输效率
客户端生成对称密码，用服务器的公钥加密

###### 认证---证书

CA(certificate Authority)数字证书认证机构：可信第三方
服务器向CA申请公钥，CA对其公钥进行签名，分配，绑定证书
HTTPS通信时，服务器将证书发送给客户端，客户端取得公钥后，用数字签名进行验证。

###### 完整性保护

HTTP：MD5报文宅摘要，报文被修改后，重新计算MD5，客户端无法察觉
HTTPS：结合加密和认证

###### 缺点

1.需要进行加解密，速度慢
2.需要支付证书授权的高昂费用

##### HTTP/2.0

###### HTTP1.0 缺陷

实现简单以牺牲性能为代价
1.客户端需要使用多个连接才能实现并发和缩短延迟
2.不会压缩请求和响应首部，导致不必要的网络流量
3.不支持有效的资源优先级，使底层TCP连接的利用率低下

###### 二进制分帧层

报文分成HEADERS帧和DATA帧，二进制格式
![image-20200224203625796](/Users/jacyn/Documents/study/Learning-materials/image/image-20200224203625796.png)

通信时只有一个TCP连接存在，承载任意数量的双向数据流(Stream)
1.一个数据流(Stream)有一个唯一标识符和可选的优先级信息，用于承载双向信息
2.消息(Message)是与逻辑请求或响应对应的完整的一系列帧
3.帧(Frame)是最小的通信单位，来自不同数据流的帧可以交错发送，再根据每个帧头的数据流标识符重新组装

![image-20200224204852323](/Users/jacyn/Documents/study/Learning-materials/image/image-20200224204852323.png)

###### 服务端推送

HTTP/2.0 在客户端请求一个资源市场，服务器会把相关资源一起发送，客户端就不用再次发起请求
如：客户端请求page.html，服务器发送script.js,style.css等相关资源

###### 首部压缩

HTTP/1.1 首部带有大量信息，且每次都重复发送
HTTP/2.0 要求客户端与服务器同时维护和更新一个包含之前见过的首部字段表，避免重复传输
且使用Huffman编码对首部字段压缩

![image-20200224205324778](/Users/jacyn/Documents/study/Learning-materials/image/image-20200224205324778.png)

##### HTTP/1.1新特性

1.默认长连接
2.支持流水线
3.支持同时打开多个TCP连接
4.支持虚拟主机
5.新增状态码100
6.支持分块传输编码
7.新增缓存处理指令max-age

##### GET和POST比较

###### 作用

get：获取资源
post：传输实体主体

###### 参数

get：查询字符串出现在URL，只支持ASCII
post：存储在实体主体，(照样可以通过抓包工具，比如Fiddler查看)，支持标准字符集

```
GET /test/demo_form.asp?name1=value1&name2=value2 HTTP/1.1

POST /test/demo_form.asp HTTP/1.1 Host: w3schools.com name1=value1&name2=value2
```

###### 安全

get安全
post不安全，因为其可能是用户上传的表单数据，上传成功后，服务器该数据，可能使得数据库状态改变
安全的方法：get，head，options
不安全：post，put，delete

###### 幂等性

同样的请求被执行一次与执行多次效果一样，服务器状态也一样
安全的都幂等
不安全的delete是幂等的，即使不同请求接受到的状态码不同，因为删除一个之后再删除效果一样

###### 可缓存

对响应进行缓存：

1. 请求报文的HTTP方法本身可缓存，包括GET和HEAD，put和delete不可缓存，post多数情况不可缓存
2. 响应报文的状态码可缓存，如：200，203，204，206，300，301，404，405，410，414，501
3. 响应报文的Cache-Control首部字段没有指定不进行缓存

###### XMLHttpRequest

一个API。客户端通过URL来获取数据的简单方式，网页只更新一部分内容，而不是整体刷新
在AJAX中大量使用

1. POST，浏览器先发送Jeader再发送Data。不是所有浏览器这样做
2. GET方法将Header和Data一起发送

Ajax 即“A synchronous Javascript And XML”（异步 JavaScript 和 XML），是指一种创建交互式、快速动态网页应用的网页开发技术，无需重新加载整个网页的情况下，能够更新部分网页的技术。

通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。