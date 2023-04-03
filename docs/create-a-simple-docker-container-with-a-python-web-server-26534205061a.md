# 使用 Python Web 服务器创建简单的 Docker 容器

> 原文：<https://medium.com/oracledevs/create-a-simple-docker-container-with-a-python-web-server-26534205061a?source=collection_archive---------0----------------------->

由[克里斯本森](https://twitter.com/chrisbensen)

![](img/7106c618a45cf44e56bed617675fb046.png)

[Photo by Frans van Heerden](https://www.pexels.com/photo/cargo-containers-trailer-lot-1624695/)

如果你愿意，你可以在 GitHub [这里](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/DockerWebServer/DockerWebServer.md)阅读这篇博文。

这篇文章对一些人来说似乎是显而易见的，但其他人需要知道如何开始。在容器中运行服务器是许多伟大事情的开始。

## 先决条件

1.  安装[对接器](https://www.docker.com)。
2.  阅读[创建一个简单的 Python Web 服务器](/oracledevs/create-a-simple-python-web-server-on-oci-1d3634a1d7c2)，因为我们将使用这个 Web 服务器，但是将它放入 Docker 容器中。

## 构建 Web 服务器

1.  创建一个文件夹，并将我们要创建的所有文件放入该文件夹。
2.  创建[index.html](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/DockerWebServer/files/index.html):

    `<!DOCTYPE html>
    <html>
    <body>
    Hello World
    </body>
    </html>`
3.  创建 [server.py](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/DockerWebServer/files/server.py) :

```
 #!/usr/bin/python3
 from http.server import BaseHTTPRequestHandler, HTTPServer
 import time
 import json
 from socketserver import ThreadingMixIn
 import threadinghostName = “0.0.0.0”
 serverPort = 80class Handler(BaseHTTPRequestHandler):
 def do_GET(self):
 # curl http://<ServerIP>/index.html
 if self.path == “/”:
 # Respond with the file contents.
 self.send_response(200)
 self.send_header(“Content-type”, “text/html”)
 self.end_headers()
 content = open(‘index.html’, ‘rb’).read()
 self.wfile.write(content)else:
 self.send_response(404)returnclass ThreadedHTTPServer(ThreadingMixIn, HTTPServer):
 “””Handle requests in a separate thread.”””if __name__ == “__main__”:
 webServer = ThreadedHTTPServer((hostName, serverPort), Handler)
 print(“Server started http://%s:%s" % (hostName, serverPort))try:
 webServer.serve_forever()
 except KeyboardInterrupt:
 passwebServer.server_close()
 print(“Server stopped.”)
 ```
```

## 将所有东西放入 Docker 容器中

1.  创建[docker file](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/DockerWebServer/files/Dockerfile):

    `FROM python:3
    ADD index.html index.html
    ADD server.py server.py
    EXPOSE 8000
    ENTRYPOINT [“python3”, “server.py”]`
2.  cd 放入您创建的文件夹中。
3.  跑`docker build -f Dockerfile . -t web-server-test`
4.  运行`docker run -rm -p 8000:80 — name web-server-test web-server-test`

你有它！享受 Docker 容器中的 web 服务器。这样做的好处是，作为一名开发人员，您可以在容器内运行和调试东西，就像它们在您的本地计算机上一样，但您的本地系统上安装的唯一东西是 Docker。