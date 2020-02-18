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