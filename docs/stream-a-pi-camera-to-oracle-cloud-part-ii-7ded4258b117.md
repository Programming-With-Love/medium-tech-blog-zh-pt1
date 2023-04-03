# 将 Pi 摄像机流式传输到 Oracle Cloud Part Deux

> 原文：<https://medium.com/oracledevs/stream-a-pi-camera-to-oracle-cloud-part-ii-7ded4258b117?source=collection_archive---------1----------------------->

克里斯本森

![](img/11840ee70ce509588e955e0083cbeb72.png)

Photo by Photography Maghradze PH from Pexels

如果你愿意，你可以在 GitHub [这里](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/PiCamera2/PiCamera2.md)阅读这篇博文。

上一次在[将一台 Pi 摄像机流式传输到 Oracle Cloud](https://chrisbensen.medium.com/stream-a-pi-camera-to-oracle-cloud-6328653c60af) 第一部分中，我向您展示了如何使用新的 Pi 摄像机系统将一台覆盆子 Pi 摄像机和一台 V2 Pi 摄像机流式传输到 Oracle Cloud。该条按逐步的方向加以简化。我也解释了很多问题的 Pi 相机系统面临的，因为有很多问题，我没有看到他们概述在一个地方。最后，当我们将软件、硬件、操作系统和 bug 中的所有这些排列相加时，情况就会变得非常复杂。我希望您在本系列文章之后能够提供一些已经过测试和验证的示例，以便您可以将它们用于自己的项目。

让我们来看看传统的 Pi 相机。这是一个传统，但仍然有效(就目前而言)。但重要的是，我在撰写本文时解释了一些差异:

*   传统相机目前(巴斯特和靶心)的工作原理是 Pi 零，Pi 3 和 Pi 4。
*   我还没有测试过 Pi 零。
*   使用新的相机系统无法启动从 Pi 到外部服务器的流。
*   新的相机系统无法从 Python 或 OpenCV 访问。
*   我没有测试所有 Pi 上的所有排列或组合。
*   我有更好的运气与运动-JPEG 时，流整个整个堆栈(Pi ->网络服务器->网络浏览器)。
*   在未来的文章中，我希望有一个完整的堆栈 H264 的例子。

## 证明文件

以下是我能找到的最好的相机文档:

*   [最新 Pi 相机文档](https://buildmedia.readthedocs.org/media/pdf/picamera/latest/picamera.pdf)
*   [相机 API](https://picamera.readthedocs.io/en/release-1.10/api_camera.html)
*   [流运动-jpg](https://www.codeinsideout.com/blog/pi/stream-picamera-mjpeg/)

## 先决条件

1.  你有一个 OCI 账户。
2.  您已经创建了一个[计算实例](https://chrisbensen.medium.com/create-an-oci-compute-instance-493d10e2e6a6)。
3.  您已将计算机上的[ssh](https://chrisbensen.medium.com/white-list-your-ip-address-to-security-connect-to-an-oci-compute-instance-4fb99958f0d9)锁定为仅适用于您的计算机。
4.  您已经将 [Compute 实例设置为 web 服务器](/@chrisbensen/create-a-simple-python-web-server-on-oci-1d3634a1d7c2)。

点击了解更多关于[计算](https://docs.oracle.com/en-us/iaas/Content/Compute/home.htm?source=:ex:tb:::::RC_WWMK220210P00062:Medium_CBensen&SC=:ex:tb:::::RC_WWMK220210P00062:Medium_CBensen&pcode=WWMK220210P00062)和其他[甲骨文云文档](https://docs.oracle.com/en-us/iaas/Content/GSG/Concepts/baremetalintro.htm?source=:ex:tb:::::RC_WWMK220210P00062:Medium_CBensen&SC=:ex:tb:::::RC_WWMK220210P00062:Medium_CBensen&pcode=WWMK220210P00062) [。要获得交互式支持和社区，请查看甲骨文面向开发者的公共](https://docs.oracle.com/en-us/iaas/Content/GSG/Concepts/baremetalintro.htm?source=:ex:tb:::::RC_WWMK220210P00062:Medium_CBensen&SC=:ex:tb:::::RC_WWMK220210P00062:Medium_CBensen&pcode=WWMK220210P00062) [Slack 频道](https://oracledevrel.slack.com/join/shared_invite/zt-uffjmwh3-ksmv2ii9YxSkc6IpbokL1g#/shared-invite/email)。

## 将 Pi 摄像机流式传输到 Oracle Cloud 并从 Web 服务器提供服务的示例

我在互联网上找到的少数几个实际可行的例子之一是这个[例子](http://picamera.readthedocs.io/en/latest/recipes2.html#web-streaming)。它在 Pi 上有一个网络服务器，只要在你网络上的任何一台计算机上访问这个网络服务器，你就可以看到摄像机看到的东西。我举了这个例子，并把它分成两部分:一个运行在 Pi 上的 Python 应用程序，它把视频从摄像机传输到一个 IP 地址。服务器接收该流并托管一个 web 服务器。由于 web 服务器托管在 Oracle Cloud 上，您拥有一个公共 IP 地址，因此在世界任何地方都可以看到您的 Pi 摄像机！

要开始，你需要上述先决条件。

注意，这个例子只是一个例子。它不处理所有错误情况或重新连接情况。一旦服务器或客户端终止，都必须重新启动。这很不幸，但是通过不添加所有的代码，您可以看到实现这一点所需的核心代码。

## 云设置

1.此时，您有一个为 web 服务器公开端口 80 的计算实例。我们还需要打开端口 8100 来接收摄像机流。按照步骤[将端口`8100`添加到安全列表:](/@chrisbensen/create-a-simple-python-web-server-on-oci-1d3634a1d7c2)

2.然后运行以下命令打开端口 8100:

`sudo firewall-cmd — permanent — zone=public — add-port=8100/tcp
sudo firewall-cmd — permanent — zone=public — add-port=8100/udp
sudo firewall-cmd — reload`

3.将` ` vidserver.py `'复制到您的计算实例:

`scp vidserver.py pi@<PI IP Address>:/home/pi`

4.运行视频网络服务器:

`sudo python3 vidserver.py`

web 服务器将一直等待，直到流被连接。

[camserver.py](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/PiCamera2/files/vidserver.py) :

```
import logging
import socketserver
from http import server
import socket
import structPAGE=”””\
<html>
<head>
<title>Raspberry Pi — Surveillance Camera</title>
</head>
<body>
<center><h1>Raspberry Pi — Surveillance Camera</h1></center>
<center><img src=”stream.mjpg” width=”640" height=”480"></center>
</body>
</html>
“””serverSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
serverSocket.bind((“”, 8100))
serverSocket.listen()(clientConnected, clientAddress) = serverSocket.accept()
print(“Accepted a connection request from %s:%s”%(clientAddress[0], clientAddress[1]))def receiveMessage(connection):
 result = None
 # Message: [4 byte message length][variable message]
 lengthOfStruct = internalReceiveMessage(connection, 4)if lengthOfStruct:
 messageLength = struct.unpack(‘>I’, lengthOfStruct)[0]
 result = internalReceiveMessage(connection, messageLength)return resultdef internalReceiveMessage(connection, length):
 # Internal function to receive a specific length number of bytes.
 # Returns the received data or returns None if EOF is encountered.
 result = bytearray()while len(result) < length:
 packet = connection.recv(length — len(result))if not packet:
 result = None
 breakresult.extend(packet)return resultclass StreamingHandler(server.BaseHTTPRequestHandler):
 def do_GET(self):
 if self.path == ‘/’:
 self.send_response(301)
 self.send_header(‘Location’, ‘/index.html’)
 self.end_headers()
 elif self.path == ‘/index.html’:
 content = PAGE.encode(‘utf-8’)
 self.send_response(200)
 self.send_header(‘Content-Type’, ‘text/html’)
 self.send_header(‘Content-Length’, len(content))
 self.end_headers()
 self.wfile.write(content)
 elif self.path == ‘/stream.mjpg’:
 self.send_response(200)
 self.send_header(‘Age’, 0)
 self.send_header(‘Cache-Control’, ‘no-cache, private’)
 self.send_header(‘Pragma’, ‘no-cache’)
 self.send_header(‘Content-Type’, ‘multipart/x-mixed-replace; boundary=FRAME’)
 self.end_headers()
 try:
 while True:
 frame = receiveMessage(clientConnected)
 self.wfile.write(b’ — FRAME\r\n’)
 self.send_header(‘Content-Type’, ‘image/jpeg’)
 self.send_header(‘Content-Length’, len(frame))
 self.end_headers()
 self.wfile.write(frame)
 self.wfile.write(b’\r\n’)
 except Exception as e:
 logging.warning(
 ‘Removed streaming client %s: %s’,
 self.client_address, str(e))
 else:
 self.send_error(404)
 self.end_headers()class StreamingServer(socketserver.ThreadingMixIn, server.HTTPServer):
 allow_reuse_address = True
 daemon_threads = Truetry:
 address = (‘’, 80)
 server = StreamingServer(address, StreamingHandler)
 server.serve_forever()
 print(“starting”)
finally:
 print(“shutdown”)) 
```

## Rasbperry Pi 设置

1.用你最喜欢的操作系统设置 SD 卡。以下是如何[安装 Oracle Linux](https://geraldonit.com/2019/03/18/how-to-install-oracle-linux-on-raspberry-pi/) 的方法，但树莓 Pi 操作系统非常容易安装[树莓 Pi 成像仪](https://www.raspberrypi.com/software/)。无论你决定使用哪种操作系统，它们都可以工作，并且有理由使用每种操作系统。

请注意，操作系统选择和您的 Raspberry Pi 版本之间存在一些差异，有些可以，有些不行。我会尽我所能记录下这种情况，但是我没有一个完整的列表。下面是我整理的一个列表，可以帮助你理清这些选择。

1.当我启动一个 Pi 时，我做的第一件事是重命名一个音频文件。当 Pi 第一次启动时，如果你有音频连接，你会得到一个恼人的“安装屏幕阅读器按下控制 alt 空格”:

`sudo mv /usr/share/piwiz/srprompt.wav /usr/share/piwiz/srprompt.wav.old`

2.更新操作系统:

`sudo apt-get update -y && sudo apt-get upgrade -y`

3.打开 SPI、I2C 和传统摄像机:

`sudo raspi-config`

**注意** : *牛眼在 raspi-config 工具的 GUI 版本中没有这个选项。*

4.重新启动以使所有更改生效:

`sudo reboot`

5.为运动 MPEG 设置摄像机。运行命令:

`sudo pico /boot/config.txt`

在文件的底部，确保删除了所有名为:`dtoverlay=`的行。

**注意** : *我见过需要在 config.txt 中放置* `*dtoverlay=imx219*` *的例子，但根据我的实验，情况并非如此。我用 V2 相机做了这些测试。*

5.将 sendvid.py 复制到您的 Pi:

`scp sendvid.py pi@<PI IP Address>:/home/pi`

[sendvid.py](https://github.com/chrisbensen/chris-blogs/blob/main/HowTo/PiCamera2/files/sendvid.py) :

```
 import io
 import picamera
 import socket
 from threading import Condition
 import argparse
 import time
 import structparser = argparse.ArgumentParser(description=”stream video.”)
 parser.add_argument(“ip”, type=str)
 parser.add_argument(“port”, type=int)
 args = parser.parse_args()address = (args.ip, args.port)
 clientSocket = Nonewhile True:
 try:
 clientSocket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
 clientSocket.connect(address)
 break
 except socket.error:
 print(“Connection Failed, Retrying..”)
 time.sleep(1)print(“connected”)def sendMessage(connection, message):
 # Message: [4 byte message length][variable message]
 lmessage = struct.pack(‘>I’, len(message)) + message
 connection.sendall(lmessage)class StreamingOutput(object):
 def __init__(self):
 self.buffer = io.BytesIO()def write(self, buf):
 if buf.startswith(b’\xff\xd8'):
 self.buffer.truncate()
 sendMessage(clientSocket, self.buffer.getvalue())
 self.buffer.seek(0)
 return self.buffer.write(buf)with picamera.PiCamera(resolution=’640x480', framerate=24) as camera:
 output = StreamingOutput()
 camera.start_recording(output, format=’mjpeg’)
 print(“camera”)try:
 camera.wait_recording(6)
 print(“done”)
 finally:
 camera.stop_recording()
 clientSocket.close() 
```

6.一旦 Pi 上有了“sendvid.py ”,运行它:

`sudo python3 sendvid.py <IpAddress> 8100`

## 观看视频

1.打开网络浏览器，输入你的公共 IP 地址，观看你的相机！

如果您有任何问题或需要交互式支持和社区，请查看甲骨文面向开发者的公共 [Slack 频道](https://bit.ly/devrel_slack)。