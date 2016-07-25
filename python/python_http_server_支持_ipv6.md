# Introduction
原来写过[Python救急HttpServer和Ftpserver](http://www.findspace.name/easycoding/1479)，来一句话迅速建立一个httpserver，供文件传输，但是只支持单线程，后续更新了下[python多线程启动httpserver](http://www.findspace.name/easycoding/1692)，但是仍然不支持ipv6,现在继续添加对ipv6支持的方法。
教育网一般都支持ipv6,走ipv6一般都可以到100Mbps的极限（10MB/s左右），[搬瓦工VPS](http://www.findspace.name/res/1417)支持ipv6,这样可以充分利用vps的网络速度，一般搬瓦工的下载速度可以到50MB/s，就算再通过ipv6传回来，速度加成也是非常明显。
这里提供几种简单的方法使python的httpserver支持ipv6。
下文均已python3 为例，python2同理。
# 1.修改http.server模块的原文件
## 1.1 获得python模块的地址
```python
import http
http.__path__
```
就可以获得http模块文件的地址，默认一般是`/usr/lib/python3.4/http`，
## 1.2 修改server模块内容
在python官网我们可以看到有这样一个bug报告[Allow IPv6 bind in http.server](https://bugs.python.org/issue24209)，维护人员也给了一个补丁，但是应该还没有添加到一些linux源里。补丁内容很简单，如下：
```python
--- a/Lib/http/server.py
+++ b/Lib/http/server.py
@@ -1173,6 +1173,9 @@
     """
     server_address = (bind, port)
 
+    if ':' in bind:
+        ServerClass.address_family = socket.AF_INET6
+
     HandlerClass.protocol_version = protocol
     httpd = ServerClass(server_address, HandlerClass)
 
```
即在server模块中添加了两行(行首有加号的)，在`/usr/lib/python3.4/http/server.py`找到对应行，自己手动添加即可。
然后通过`python3 -m http.server -b ::`绑定本机即可运行。通过访问`http://[::]:8000`来测试是否开启成功。
# 2. 自定义httpserver
参考[github python2 ipv6-httpd.py](https://gist.github.com/akorobov/7903307)
```python
import http.server
import socketserver
import socket
class HTTPServerV6(http.server.HTTPServer):
  address_family = socket.AF_INET6

Handler = http.server.SimpleHTTPRequestHandler
server = HTTPServerV6(('::', 8000), Handler)
server.serve_forever()
```
python3运行这个文件，仍然通过访问`http://[::]:8000`来测试是否开启成功。