## curl命令

**curl**：transfer a URL

**URL**：unit resource location （统一资源定位）

使用以下三行命令curl www.baidu.com

```sh
$ curl -s -v -H "Frank:xxx" -- "www.baidu.com"
$ curl -X POST -s -v -H "Frank:xxx" -- "www.baidu.com"
$ curl -d "1234567890" -s -v -H "Frank:xxx" -- "www.baidu.com"
```

[解释命令网站](explainshell.com)<http://explainshell.com>

**curl中文乱码**：windows中文编码默认是GBK，网页编码UTF-8，curl某个URL出现中文乱码，用其自带的chcp指令改成UTF-8（代码页65001）

```sh
$ chcp 65001
```

上面三个请求示例，对应的请求与响应分别为

```sh
$ curl -s -v -H "Frank:xxx" -- "www.baidu.com"
```

```sh
> GET / HTTP/1.1
> Host: www.baidu.com
> User-Agent: curl/7.55.1
> Accept: */*
> Frank:xxx
```

```sh
< HTTP/1.1 200 OK
< Accept-Ranges: bytes
< Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
< Connection: keep-alive
< Content-Length: 2381
< Content-Type: text/html
< Date: Fri, 29 Jan 2021 07:02:07 GMT
< Etag: "588604dd-94d"
< Last-Modified: Mon, 23 Jan 2017 13:27:57 GMT
< Pragma: no-cache
< Server: bfe/1.0.8.18
< Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
<
<!DOCTYPE html>
...省略
```

```sh
$ curl -X POST -s -v -H "Frank:xxx" -- "www.baidu.com"
```

```sh
> POST / HTTP/1.1
> Host: www.baidu.com
> User-Agent: curl/7.55.1
> Accept: */*
> Frank:xxx
```

```sh
< HTTP/1.1 302 Found
< Connection: keep-alive
< Content-Length: 17931
< Content-Type: text/html
< Date: Fri, 29 Jan 2021 07:09:19 GMT
< Etag: "54d9748e-460b"
< Server: bfe/1.0.8.18
<
<html>
```

```sh
$ curl -d "1234567890" -s -v -H "Frank:xxx" -- "www.baidu.com"
```

```sh
> POST / HTTP/1.1
> Host: www.baidu.com
> User-Agent: curl/7.55.1
> Accept: */*
> Frank:xxx
> Content-Length: 10
> Content-Type: application/x-www-form-urlencoded
```

```sh
< HTTP/1.1 302 Found
< Connection: keep-alive
< Content-Length: 17931
< Content-Type: text/html
< Date: Fri, 29 Jan 2021 07:09:54 GMT
< Etag: "54d9748e-460b"
< Server: bfe/1.0.8.18
<
<html>
```

1. GET请求和POST请求对应的响应可以一样，也可以不一样

2. 响应的**第四部分**可以很长很长

3. `Content-Length`字段代表发送数据部分长度，比如使用

   ```sh
   $ curl -d "1234567890" -s -v -H "Frank:xxx" -- "www.baidu.com"
   ```

   发送了数据`1234567890`**长度为10**

   报文字段`Content-Length: 10`

## 响应格式

```txt
1 协议/版本号 状态码 状态解释
2 key1: value1
2 key2: value2
2 Content-Length: 17931
2 Content-Type: text/html
3
4 要下载的内容
```

- 状态码

| 分类 | 分类描述                                       |
| :--- | :--------------------------------------------- |
| 1**  | 信息，服务器收到请求，需要请求者继续执行操作   |
| 2**  | 成功，操作被成功接收并处理                     |
| 3**  | 重定向，需要进一步的操作以完成请求             |
| 4**  | 客户端错误，请求包含语法错误或无法完成请求     |
| 5**  | 服务器错误，服务器在处理请求的过程中发生了错误 |

| 状态码  | 状态码英文名称             | 中文描述                                                     |
| :------ | :------------------------- | :----------------------------------------------------------- |
| 100     | Continue                   | 继续。[客户端](http://www.dreamdu.com/webbuild/client_vs_server/)应继续其请求 |
| **200** | **OK**                     | 请求**成功**。一般用于GET与POST请求                          |
| 201     | Created                    | 已创建。成功请求并创建了新的资源                             |
| **301** | Moved Permanently          | **永久移动**。请求的资源已被永久的移动到新URI，返回信息会包括新的URI，浏览器会自动定向到新URI。今后任何新的请求都应使用新的URI代替 |
| **302** | Found                      | **临时移动**。与301类似。但资源只是临时被移动。客户端应继续使用原有URI |
| **304** | Not Modified               | **未修改**。**客户端已经缓存访问过该资源**，服务器不会返回该请求的资源。 |
| 400     | Bad Request                | 客户端请求的语法错误，服务器无法理解                         |
| 403     | Forbidden                  | 服务器理解请求客户端的请求，但是拒绝执行此请求               |
| **404** | **Not Found**              | **资源无法找到**                                             |
| **500** | **Internal Server Error**  | **服务器内部错误**                                           |
| 501     | Not Implemented            | 服务器不支持请求的功能                                       |
| 503     | Service Unavailable        | 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中 |
| 505     | HTTP Version not supported | 服务器不支持HTTP协议版本                                     |

## 手写请求响应

```
GET 请求

GET / HTTP/1.1
Host:baidu.com
Accept:text/html

响应
HTTP/1.1 200 OK
Content-Type:text/html;charset=utf-8
Contetnt-Length:1000

<!DOCHTML>
<html>...</html>

POST请求

POST /login HTTP/1.1
Host:baidu.com
Accept:application/json
Content-Type:application/x-www-form-urlencoded
Content-Length:10

username=wusili&password=123456

响应
HTTP/1.1 403 Forbidden
Content-Type:application/json
Content-Length:200

{"error":"密码错误"}
```

