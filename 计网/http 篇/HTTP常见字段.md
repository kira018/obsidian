# _Host_ 字段

客户端发送请求时，用来指定服务器的域名。
# Content-Length 字段
服务器在返回数据时，会有 `Content-Length` 字段，表明本次回应的数据长度。

大家应该都知道 HTTP 是基于 TCP 传输协议进行通信的，而使用了 TCP 传输协议，就会存在一个“粘包”的问题，**HTTP 协议通过设置回车符、换行符作为 HTTP header 的边界，通过 Content-Length 字段作为 HTTP body 的边界，这两个方式都是为了解决“粘包”的问题**。具体什么是 TCP 粘包，可以看这篇文章：[如何理解是 TCP 面向字节流协议？](https://xiaolincoding.com/network/3_tcp/tcp_stream.html)
# Connection 字段
`Connection` 字段最常用于客户端要求服务器使用「HTTP 长连接」机制，以便其他请求复用。
HTTP 长连接的特点是，只要任意一端没有明确提出断开连接，则保持 TCP 连接状态。
HTTP/1.1 版本的默认连接都是长连接，但为了兼容老版本的 HTTP，需要指定 `Connection` 首部字段的值为 `Keep-Alive`。

```
Connection: Keep-Alive
```

开启了 HTTP Keep-Alive 机制后， 连接就不会中断，而是保持连接。当客户端发送另一个请求时，它会使用同一个连接，一直持续到客户端或服务器端提出断开连接。

PS：大家不要把 HTTP Keep-Alive 和 TCP Keepalive 搞混了，这两个虽然长的像，但是不是一个东西，具体可以看我这篇文章：[TCP Keepalive 和 HTTP Keep-Alive 是一个东西吗？](https://xiaolincoding.com/network/3_tcp/tcp_http_keepalive.html)
长连接主要是为了避免每次请求都建立tcp连接然后http三次握手

# _Content-Type 字段_

`Content-Type` 字段用于服务器回应时，告诉客户端，本次数据是什么格式。
```
Content-Type: text/html; Charset=utf-8
```P