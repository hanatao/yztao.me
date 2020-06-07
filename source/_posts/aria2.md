---
title: 部署 aria2 作为主要下载工具
date: 2017-04-22
tags: 
- aria2
categories: 
- 技术宅
---

![](https://hanatao-1254384827.cos.ap-shanghai.myqcloud.com/aria2-1.png)

这篇文章写作的目的是为了详细记录我部署 aria2 作为我的 MacBook Pro 的主要下载工具的过程，以作为备份。当然有跟我有同样需求的网友也可以按照步骤进行部署工作。

<!--more-->

## 目录

> 1. 安装 Homebrew  
> 2. 安装 aria2  
> 3. 配置 aria2  
> 4. 启动 aria2  
> 5. 加载UI  
> 6. 配置 aria2 为一键启动  

### 安装 Homebrew

Homebrew是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，通过 Homebrew 我们可以很容易的来安装 aria2。

安装 Homebrew 的方法很简单，我们只需要打开 macOS 自带的终端（Terminal），在终端中输入以下指令：
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
等待其显示 `==> Installation Successful!` ，安装就完毕了。安装完毕后，我们就可以通过在终端中输入 `brew install 软件名` 来安装支持在 Homebrew 中安装的软件了。

### 安装 aria2

 打开终端，在终端中输入 `brew install aria2` ，等待其安装完毕。我们可以通过输入 `brew list` 来查看已通过 Homebrew 安装的应用，因为是按照首字母顺序排列的，所以如果正常安装成功的话，aria2 应该会出现在列表的前面。

### 配置 aria2

aria2 拥有两种运行方式，一种方式是直接在终端中以命令行模式下载，一种是以 RPC 模式在后台常驻，等待下载请求。第一种模式因为需要用到命令行比较繁琐，不推荐。这里主要介绍的是第二种运行方式。

要让 aria2 以 PRC 模式运行，首先我们需要为其编写一个配置文件。新建一个配置文件并将其命名为 `aria2.conf` ，并将下面的内容复制进该文件中：

```
#用户名
#rpc-user=user
#密码
#rpc-passwd=passwd
#上面的认证方式不建议使用,建议使用下面的token方式
#设置加密的密钥
#rpc-secret=token
#允许rpc
enable-rpc=true
#允许所有来源, web界面跨域权限需要
rpc-allow-origin-all=true
#允许外部访问，false的话只监听本地端口
rpc-listen-all=true
#RPC端口, 仅当默认端口被占用时修改
#rpc-listen-port=6800
#最大同时下载数(任务数), 路由建议值: 3
max-concurrent-downloads=5
#断点续传
continue=true
#同服务器连接数
max-connection-per-server=5
#最小文件分片大小, 下载线程数上限取决于能分出多少片, 对于小文件重要    min-split-size=10M
#单文件最大线程数, 路由建议值: 5
split=10
#下载速度限制
max-overall-download-limit=0
#单文件速度限制
max-download-limit=0
#上传速度限制
max-overall-upload-limit=0
#单文件速度限制
max-upload-limit=0
#断开速度过慢的连接
#lowest-speed-limit=0
#验证用，需要1.16.1之后的release版本
#referer=*
#文件保存路径, 默认为当前启动位置
dir=/User/xxx/Downloads
#文件缓存, 使用内置的文件缓存, 如果你不相信Linux内核文件缓存和磁盘内置缓存时使用, 需要1.16及以上版本
#disk-cache=0
#另一种Linux文件缓存方式, 使用前确保您使用的内核支持此选项, 需要1.15及以上版本(?)
#enable-mmap=true
#文件预分配, 能有效降低文件碎片, 提高磁盘性能. 缺点是预分配时间较长
#所需时间 none < falloc ? trunc << prealloc, falloc和trunc需要文件系统和内核支持    file-allocation=prealloc
```

复制的同时注意将里面的 `dir=/User/xxx/Downloads` 中的 `xxx` 替换为 macOS 中的用户名称，这样配置过后下载的文件就会直接进入 macOS 中的下载文件夹了。之后在文稿文件夹中新建一个名为 `aria2` 的文件夹，将上一步骤生成的 `aria2.conf` 文件保存进去。

### 启动 aria2

要想让 aria2 通过 RPC 模式运行，我们需要在终端中输入 `aria2c --conf-path=<PATH>` ，将 `<PATH>` 换成刚刚保存的 `aria2.conf` 的绝对路径。如果之前的步骤都是按照我写的这个方法去做，则 `<PATH>` 就是 `/Users/xxx/Documents/aria2/aria2.conf` 。可以使用 -D 参数使Aria2在后台运行,即使关闭终端也不会停止运行。 因此，完整的终端命令就应该是：
```
aria2c --conf-path= /Users/xxx/Documents/aria2/aria2.conf
```
记得要将 `xxx` 替换成 macOS 系统用户名。

### 加载UI

通过上面的步骤，我们的 aria2 就已经安装成功并部署完毕，在后台静静地运行了。但是这又有一个问题，我们很难直观的进行文件的下载、查看下载进程，并对其进行基本的设置，这时候我们就需要为 aria2 套用一套管理界面了。我所使用的管理界面是 [Aria2 WebUI](https://github.com/ziahamza/webui-aria2) 。点击 [Aria2 WebUI](https://github.com/ziahamza/webui-aria2) 进入它的 GitHub 页面，点击右侧的 Clone or Download ，在弹出的界面里点击 Download ZIP ，我们就能够把整个 Aria2 WebUI 打包下载下来了。

![](https://hanatao-1254384827.cos.ap-shanghai.myqcloud.com/aria2-2.png)

将下载下来的 ZIP 文件解压至 `/Users/xxx/Documents/aria2` ，会在 `aria2` 文件夹中生成一个名为 `webui-aria2-master` 的文件夹，打开文件夹中的 `index.html` ，我们就能在浏览器中显示 aria2 的UI界面了。

![](https://hanatao-1254384827.cos.ap-shanghai.myqcloud.com/aria2-3.png)

### 配置 aria2 为一键启动

通过以上的步骤，我们就能够在自己的电脑中正常的使用 aria2 了。但是还有一个不方便的地方：我们每次重启电脑，都需要在终端中输入我们第四步工作中的终端命令来启动 aria2 ，这无疑不符合我这种懒人的行为准则。因此我们要将其设置成一键启动。

我们通过 macOS 系统自带的 Automator 来完成设置 aria2 的启动。我们打开 Automator ，新建一个应用程序，在“实用工具”中选择“运行 Shell 脚本”，将其拖入右侧流程框内。删去脚本框中的 `cat` 并键入以下代码：

```
#!/bin/bash
aria2c --conf-path="/Users/xxx/Documents/Aria2/aria2.conf" -D
open /Users/Eskimo/Documents/Aria2/webui-aria2-master/index.html
exit
```

记得要将 `xxx` 替换成 macOS 系统用户名。之后将工作流保存为应用程序，放在方便的地方。这样双击该生成的应用程序，我们就能直接启动 aria2 并在浏览器中打开 WebUI 了。