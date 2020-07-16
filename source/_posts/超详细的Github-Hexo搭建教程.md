---
title: 超详细的Github+Hexo搭建教程
date: 2019-10-31 20:45:02
tags: 
   - Hexo
categories: 前端
---

![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/hexo.jpg)

<!-- more -->

## 前言

不管你是程序猿（媛），产品经理，设计师，运维工程师...还是从事其他职业，应该都想拥有一个属于自己的个人博客网站吧。如果你是，那么请跟随目录，搭建属于你的个人博客吧!一起来技术分享，记录生活...

## Hexo 是什么

[Hexo](https://hexo.io) 是一个基于 [Node.js](https://nodejs.org/en)的快速，简单月功能强大的博客框架。可以使用简单的命令生成静态网页，并托管到 Github 上。
#TODO

## 来搭建属于你的个人博客吧～

### Gihub 创建个人仓库

- 首先先登录到 [Github](https://github.com)。如果没有个人账号，先进行注册，注册完成后，点击登录进入 Github。
- 点击绿色的 **New** 按钮新建一个仓库，将仓库名称命为： 用户名.github.io，例如：qiruohan.github.io，这个写法是固定的。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_1.jpg)
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_2.jpg)

- 注意：仓库名称要和你的用户名保持一致，后缀.github.io 的作用是 Github 识别到.github.io 后缀就会为你自动开启[Github Page](https://pages.github.com/)，作为你个人博客的仓库。

然后项目就建成了，点击Settings，向下拉到最后有个GitHub Pages，点击Choose a theme可以选择一个主题。然后点击那个链接，就会出现自己的网页啦～

### 安装 Node.js

Node.js 是基于 [Chrome V8 JavaScript 引擎](https://v8.dev/) 构建的语言，是一项服务器端技术。
下载地址：[Node.js | Download](https://nodejs.org/en/download/)，下载当前操作系统的安装包，安装选项全部默认。注意下载的安装包中已经包含了环境变量以及 [npm](https://www.npmjs.com/)，所以安装完安装包后无需另外再下载 npm。

检测 Node.js 是否安装成功，在命令行中输入：**node -v**
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_3.png)

检测 npm 是否安装成功，在命令行中输入：**npm -v**
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_4.png)

显示版本号，那么就说明 node.js 安装成功了。

### 安装 Git

[Git](https://git-scm.com/) 是一个开源的分布式版本控制系统，旨在快速高效地处理从小型到大型项目的所有内容。具有便捷的创建本地分支，创建暂存区域，处理多个工作流等功能。简单来说，使用 Git 可以把本地文件同步到 Github 上，完成多人多空间的便捷式管理。

**Windows 下**安装下载地址：[Git | Downloads](https://git-scm.com/download/)，安装选项还是全部默认，安装完成后在命令行中输入 **git --version** 验证是否安装成功。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_5.png)
显示版本号，那么就说明 git 安装成功了。安装成功后，将你的Git与GitHub帐号绑定，鼠标右击打开Git Bash，设置user.name和user.email配置信息。
之后移步到 mac 下安装流程的**第三步：设置github的 username 和 email**，做接来下的操作。

**Mac 下安装**：
- 如果未安 homebrew，需要先安装 homebrew
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

- 安装 git
```
brew install git
```

- 检查 git 是否安装成功
```
git --version
```

- 安装成功后，先设置github的 username 和 email（github 在每次提交的时候都会记录他们）
```
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```

- 使用终端命令创建 ssh key
```
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```
然后直接三个回车即可，默认不需要设置密码
然后找到生成的.ssh的文件夹中的id_rsa.pub密钥，将内容全部复制

- 打开 [GitHub_Settings_keys](https://github.com/settings/keys) 页面，新建new SSH Key
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_10.png)

Title为标题，任意填即可，将刚刚复制的id_rsa.pub内容粘贴进去，最后点击Add SSH key。

- 在终端检测GitHub公钥设置是否成功，输入 ssh git@github.com。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_11.png)

如上则说明成功。

注意：这里之所以设置GitHub密钥原因是，通过非对称加密的公钥与私钥来完成加密，公钥放置在GitHub上，私钥放置在自己的电脑里。GitHub要求每次推送代码都是合法用户，所以每次推送都需要输入账号密码验证推送用户是否是合法用户，为了省去每次输入密码的步骤，采用了ssh，当你推送的时候，git就会匹配你的私钥跟GitHub上面的公钥是否是配对的，若是匹配就认为你是合法用户，则允许推送。这样可以保证每次的推送都是正确合法的。

### 安装 Hexo
Hexo 就是我们搭建个人博客所使用的框架，我们需要在合适的地方先创建一个文件夹，用来存放自己的博客文件，例如我命名为blog2。

使用命令行进入到该目录下，输入 `npm i hexo-cli -g` 安装 Hexo，安装成功后，会显示安装所使用的总时长。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_6.png)

安装完成后，初始化我们的博客，输入 `hexo init blog`。
注意：这里的命令都作用在刚刚创建的 blog2 文件夹下。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_7.png)

初始化时间可能会比较长，耐心等待...
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_8.png)

初始化完成后，会发现 blog2 下又新增了一个文件夹，名为 blog，与 `hexo init` 后面输入的文件名同名。我们进入新创建的文件夹 blog 下，输入以下三条命名来检测一下我们的网站雏形

```
hexo new test

hexo g

hexo s

```
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_9.png)

到这里，我们的个人博客就搭建完成啦！并且已经写出了我们的第一篇文章～

### hexo 常用命令

```
1.安装 Hexo
  npm install hexo -g
2.升级 Hexo
  npm update hexo -g
3.初始化博客
  hexo init "博客站点"
4.新建文章
  hexo n "我的博客"  或  hexo new "我的博客" 
5.生成博客
  hexo g  或 hexo generate
6.启动服务
  hexo s  或  hexo server
7.部署博客
  hexo d  或  hexo deploy
8.更改端口
  hexo server -p 5000
9.自定义 IP
  hexo server -i 192.168.1.1
10.清除缓存，若是网页正常情况下可以忽略这条命令
  hexo clean
11.新建页面
  hexo new page xxx
```
### 推送博客站点

上图只是本地的预览，如果想让大家都看到你的博客，就得把项目放在公网上被大家访问。打开博客根目录下的_config.yml文件，这是博客的配置文件，在这里你可以修改与博客相关的各种信息。这个文件称之为**站点配置文件**。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_12.png)

修改最后一行的配置：
```
deploy:
  type: git
  repo: https://github.com/qiruohan/qiruohan.github.io.git
  branch: master
```
repository修改为你自己的github项目地址。

这里其实就是给 hexo d 命令做相应的配置，让 hexo 知道要把你的博客部署在哪个位置，我们需要把项目部署到我们自己的GitHub的仓库里。

安装Git部署插件，输入命令：
```
npm install hexo-deployer-git --save
```

这时，我们分别输入三条命令：
```
hexo clean 
hexo g 
hexo d
```
打开浏览器，在地址栏输入你的放置个人网站的仓库路径，即 xxxx.github.io, 比如我的：qiruohan.github.io， 你就会发现你的博客已经上线了，可以在网络上被访问了。

### 更换主题

如果你不喜欢 Hexo 的默认主题，可以更换主题，hexo主题有很多，你可以从网上找到很多很好看的主题，每个主题也都有自己的安装教程，你可以试着看一看。

我这里使用的主题是[nexT](https://theme-next.iissnan.com/)，所以我说一下我的安装配置吧～

- 安装 nexT 主题，通过 git 命令将 nexT 克隆下来， 在博客站点目录下（我的是blog），使用 git clone 命令：
```
git clone https://github.com/theme-next/hexo-theme-next themes/next
```

- 等待克隆完毕，找到 themes 文件夹下的 next 文件， 这就是我们刚刚克隆下来的主题了。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_13.png)

- 返回根目录，找到我们的站点文件_config.yml，打开并修改里面的 theme 配置以使我们刚刚克隆下来的主题生效。

```
theme: next
```

修改theme: landscape为next，注意theme和next之间要有空格，否则无效。

正确设置好后，我们更换的主题就生效啦～每个主题都可以有自己个性化的配置，可以打开主题的_config.yml配置文件（注意不是站点配置文件），可以按照你的想法做一些个性化的配置，之后再次部署网站，hexo clean、hexo g、hexo d，查看效果。选择其他主题，按照上述过程即可实现。

### 发布文章

1.文章头设置


### MarkDown 语法

Markdown是一种可以使用普通文本编辑器编写的标记语言，通过简单的标记语法，具体语法参看：[Markdown 语法说明(简体中文版)](https://www.appinn.com/markdown/) 可以说十分钟就可以入门，非常简单发方便。当然，选择一个好的Markdown编辑器也是非常重要的，mac版推荐使用 MacDown 或者直接使用 VsCode 编写 Markdown 文件， 非常方便。

### 绑定域名

现在默认的域名还是xxx.github.io，而如果我们想使用个性化的域名，就需要绑定我们自己的域名，首先你需要购买一个域名，XX云都能买，国内主流的域名代理厂商也就阿里云和腾讯云。下面给大家演示阿里云的相关配置：

- 登录阿里云，进入管理控制台的域名列表，找到你的个性化域名，进入解析

![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_14.png)

- 添加解析

![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_15.png)

一共包括两条解析记录，记录类型都是CNAME，CNAME的记录值是：你的用户名.github.io，这里千万别弄错了。

- 登录GitHub，进入之前创建的仓库，点击settings，设置Custom domain，输入你的域名，点击保存。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_16.png)

注意：如果你把 Enforce HTTPS 钩上，github 会自动帮你升级为 https 的哦～

- 这时候你的项目根目录应该会出现一个名为CNAME的文件了。如果没有的话，打开你本地博客/source目录，手动创建一个CNAME文件，注意没有后缀。写上你的域名。
注意，只要写进你自己的域名即可。如果带有www，那么以后访问的时候必须带有www完整的域名才可以访问，但如果不带有www，以后访问的时候带不带www都可以访问。所以建议，不要带有www。
![image](https://cdn.jsdelivr.net/gh/qiruohan/qiruohan.github.io/uploads/i_17.png)

- 点击保存。保存成功后运行hexo g、hexo d传到github上。这时候打开浏览器在地址栏输入你的个性化域名将会直接进入你自己搭建的网站。

### 个性化配置

### 寻找图床


