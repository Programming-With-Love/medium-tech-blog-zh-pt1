# 创建媒体内容的自适应加载

> 原文：<https://medium.com/quick-code/hello-guys-9e2c00993057?source=collection_archive---------5----------------------->

你们好，伙计们！我目前正在为我的项目开发一个网站。在网站上，我需要展示很多 gif，每个都很重要。如果一次显示所有内容，页面加载时间过长。同时，它应该明确包含 gif 在最开始，所以内容不能加载后加载一个页面。

如果你对这个问题的解决方案感兴趣，那么这篇文章将会很有帮助。

# 实际上问题是

正如我以前说过的，网站上有太多的 gif。你可以在 [reface.tech](https://reface.tech/) 上查看它们

![](img/fdf8df817c84e41bd2bb3a4e6254d2af.png)

Gifs on the webpage

当我开发登陆页面的测试版时，就出现了一个加载问题:对于一些用户来说，它要加载很长时间。这当然是一件令人沮丧的事情。

有必要以某种方式解决它。那时，媒体内容已经通过 compressor.io 无损压缩，所以我与此无关。因此，有必要进行有损压缩，并给出质量较低的 gif。

但是把不好的媒体内容发给互联网好的用户，是某种亵渎。因此，我决定以某种方式确定一个客户端的互联网速度，并根据速度滑动适当的质量。

# 画草图

我将有一个数组，其中包含实际显示的媒体内容的描述。

**举例:**

```
[
   {
      "large": 
         {
            "size": 0.6211118698120117,
            "url":"gifs/control/large/control_1.gif"
         },
      "middle": 
         {
            "size":0.5330495834350586,
            "url":"gifs/control/middle/control_1.gif"
         },
      "small": 
         {
            "size":0.4901447296142578,
            "url":"gifs/control/small/control_1.gif"
         }
   }
]
```

我们在元素中有一个 url 和文件大小(针对每个质量)。

## 我们只是:

1.  遍历数组(当页面加载时)
2.  计算每种质量的已加载内容的总大小
3.  检查它们中的哪一个(从最好的开始)可以在 4 秒内加载(这个时间符合我的要求)

## 那么我们应该:

1.  编写一个脚本来自动输出这样的数组
2.  写一个输出网络连接速度的东西

# 写一个检查速度的东西

会比较简单。在你浏览器的地址栏中输入 eu.httpbin.org/stream-bytes/51200。然后你的浏览器会下载一个 51200 字节的文件。将它粘贴到“公共”目录中(以测量主机速度)。

现在我们需要检查文件下载了多少。让我们为此编写一个简单的函数，它将以每秒兆字节为单位返回速度。

```
async checkDownloadSpeed(baseUrl, fileSizeInBytes) {
   return new Promise((resolve, _) => {
      let startTime = new Date().getTime();
      return axios.get(baseUrl).then( response => {
         const endTime = new Date().getTime();
         const duration = (endTime - startTime) / 1000;
         const bytesPerSecond = (fileSizeInBytes / duration);
         const megabytesPerSecond = (bytesPerSecond / 1000 / 1000);
         resolve(megabytesPerSecond);
      });
   }).catch(error => {
      throw new Error(error);         
   });
}
```

因此，我们只需设置开始下载的时间，完成下载的时间，测量差异，并且，因为我们知道文件的大小，只需划分。

现在让我们写一个函数，用这个速度做一些事情:

```
async getNetworkDownloadSpeed() {
   const baseUrl = process.env.PUBLIC_URL + ‘/51200’;
   const fileSize = 51200;
   const speed = await this.checkDownloadSpeed(baseUrl, fileSize);
   console.log(“Network speed: “ + speed); if (speed.mbps === “Infinity”) {
      SpeedMeasure.speed = 1;
   }   else { 
      SpeedMeasure.speed = speed * 5;
   }
}
```

实际上，这段代码存在一个问题:由于下载文件很小，无法确定互联网连接的准确速度。但是我们不能下载更大的东西，所以我们只是将确定的速度乘以 5。实际上它会比正确的速度还要慢。

现在让我们写一个函数，它将根据速度设置质量:

```
static getResolution(gifsArray) {
   let totalSizeLevel1 = 0;
   let totalSizeLevel2 = 0;
   let totalSizeLevel3 = 0; for (let i = 0; i < gifsArray.length; i++) {
      for (let a = 0; a < gifsArray[i].length; a++) {
         let element = gifsArray[i][a];
         totalSizeLevel1 += element.small.size;
         totalSizeLevel2 += element.middle.size;
         totalSizeLevel3 += element.large.size;
      }
   } if (isNaN(SpeedMeasure.speed)) {
      SpeedMeasure.speed = 1;
   } let timeLevel1 = totalSizeLevel1 / SpeedMeasure.speed;
   let timeLevel2 = totalSizeLevel2 / SpeedMeasure.speed;
   let timeLevel3 = totalSizeLevel3 / SpeedMeasure.speed; if (timeLevel3 < APPROPRIATE_TIME_LIMIT) {
      return "large";
   } else if (timeLevel2 < APPROPRIATE_TIME_LIMIT) {
      return "middle";
   } else {
      return "small";
   } 
}
```

因为计算速度的函数是异步的，所以 SpeedMeasure.speed 可以是 NaN。默认情况下，我们认为连接速度是每秒 1 兆字节。当函数计算速度时，我们只是重新渲染容器。

我们将数组的数组传递给 getResolution 函数。为什么？因为如果我们页面上有几个带有 gif 的容器，那么我们把对应的内容作为数组传递给其中的每一个容器会更方便，但是要考虑一下子全部加载的速度。

# 用法的例子

下面是一个用法示例(React):

```
async runFunction() {
   let speedMeasure = new SpeedMeasure();
   await speedMeasure.getNetworkDownloadSpeed();
   this.forceUpdate()
}componentDidMount() {
   this.runFunction();
}render() {
   let quality = SpeedMeasure.getResolution([Control.getControlArray(),Health.getHealthArray()]); return (
      <div className="app">
         <Presentation />
         <Control quality={quality} />
         <Health quality={quality} />
      </div>
   );
}
```

因此，当一切都将被下载，速度将被测量，容器将被重新渲染。

在容器内部(比如在控件内部)，我只是从一个数组中取出一个相应的 gif(通过索引)，然后我只是通过“quality”键得到一个对象，通过“url”键得到一个链接。其实很简单。

# 编写 Python 脚本:

现在我需要以某种方式压缩 gif 并输出一个包含内容描述的数组。

首先让我们写一个压缩 gif 的脚本。我们将使用 gifsicle。对于“中等”质量的 gif，压缩率为 80(满分为 200)，对于“小”质量的 gif，压缩率为 160。

```
import osGIFS_DIR = "/home/mixeden/Документы/Landingv2/"
COMPRESSOR_DIR = "/home/mixeden/Документы/gifsicle-static"
NOT_OPTIMIZED_DIR = "not_optimized"
OPTIMIZED_DIR = "optimized"
GIF_RESIZED_DIR = "gif_not_optimized_resized"
GIF_COMPRESSED_DIR = "gif_compressed"
COMPRESSION_TYPE = ["middle", "small"]for (root, dirs, files) in os.walk(GIFS_DIR, topdown=True):
   if len(files) > 0 and GIF_RESIZED_DIR in root:
      for file in files:
         path = root + "/" + file
            for compression in COMPRESSION_TYPE:
               final_path = path.replace(GIF_RESIZED_DIR, GIF_COMPRESSED_DIR + "/" + compression + "/" + OPTIMIZED_DIR)
               print(path, final_path) if compression == COMPRESSION_TYPE[0]:
                  rate = 80 else:
                  rate = 160 os.system("echo 0 > " + final_path)
               os.system(COMPRESSOR_DIR + " -O3 --lossy={} -o {} {}".format(rate, final_path, path))
```

为了让您理解我的文件系统的结构，这里有一个描述:

1.  未优化目录-未优化的 gif
2.  GIF _ RESIZED _ DIR 未优化的 GIF，但根据页面容器的大小调整大小
3.  GIF_COMPRESSED_DIR —压缩的 GIF
4.  目录里有文件夹，文件夹里有 gif 的类别名称。类别文件夹内有大、中、小文件夹(根据质量类型)。

在脚本中，我们只需遍历包含 gif 的目录，并使用适当的命令压缩每个文件。

现在让我们写一个脚本来生成一个包含信息的数组。

```
import json import osGIFS_DIR = "/home/mixeden/Документы/Landingv2/"
COMPRESSOR_DIR = "/home/mixeden/Документы/gifsicle-static"
NOT_OPTIMIZED_DIR = "not_optimized"
OPTIMIZED_DIR = "optimized"
GIF_RESIZED_DIR = "gif_not_optimized_resized"
GIF_COMPRESSED_DIR = "gif_compressed"
COMPRESSION_TYPE = ["large", "middle", "small"]
OUTPUT = {}for (root, dirs, files) in os.walk(GIFS_DIR, topdown=True):
   if len(files) > 0 and GIF_COMPRESSED_DIR in root and NOT_OPTIMIZED_DIR not in root:
      files.sort()
      type = root.split(GIFS_DIR)[1].split(GIF_COMPRESSED_DIR)[0].replace("/", "")
      print(type) if type not in OUTPUT:
          OUTPUT[type] = [] if len(OUTPUT[type]) == 0:
         for file in files:
            OUTPUT[type].append(
               {
                  "large": {
                           "url": "",
                           "size": 0
                  },
                  "middle": {
                           "url": "",
                           "size": 0
                  }, 
                  "small": {
                           "url": "",
                           "size": 0
                  }
               }) for file in files:
            full_path = root + "/" + file
            bytes_size = os.path.getsize(full_path)
            kilobytes_size = bytes_size / 1000
            megabytes_size = kilobytes_size / 1000
            index = int(file.split("_")[1].replace(".gif", "")) - 1

            for typer in COMPRESSION_TYPE:
               if typer in root:
                  local_type = typer

            new_url = "gifs/" + full_path.replace(GIFS_DIR, "").replace("/" + GIF_COMPRESSED_DIR, "").replace("/" + OPTIMIZED_DIR, "")
            OUTPUT[type][index][local_type]['url'] = new_url
            OUTPUT[type][index][local_type]['size'] = megabytes_size print(OUTPUT)
print(json.dumps(OUTPUT, indent=4, sort_keys=True))
```

在这里，我们浏览文件夹，确定每个文件的大小，找出压缩类型，并将信息放入数组中。然后我们把这个数组输出到控制台(然后就可以复制了)。

# 结论

希望这篇文章能对你有所帮助。祝你们编码愉快，伙计们。