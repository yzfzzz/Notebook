# Hexo+GitHub+Vercel搭建个人博客网站

[TOC]

⭐前段时间动手搭了一个博客网站 [FengLibrary](https://www.yzfenglib.top/) ，主要是用了Hexo+GitHubPages+Vercel框架。

- 环境搭建：nodejs+Git；
- 写作：Typora,PicGo+阿里云OSS
- 域名管理：namesilo



## Hexo

### 安装nodejs

Hexo是一个快速、简洁且高效的博客框架。

因为Hexo是基于nodejs的，所以要使用Hexo，那么第一步肯定是安装nodejs

nodejs下载地址：http://nodejs.cn/

直接安装，完成后，在cmd上输入`node -v`和`npm -v`查看是否安装成功，这两条命令如果都输出了版本号，那么就表示安装成功了，如下:

```python
node -v
-> v16.14.0
npm -v
-> 8.3.1
```

nodejs准备就绪了，那么现在就可以有请第一位主角Hexo出场了



### 安装Hexo

安装完之后，因为nodejs仓库会被墙掉，所以我们需要更换为国内镜像仓库

在终端中执行：

`npm config set registry https://registry.npm.taobao.org`

执行命令

`npm install hexo-cli -g`

若出现Hexo的相关信息则表示Hexo安装成功



### 初始化Hexo博客（个人网站）文件夹

cmd命令进入你电脑的D盘，新建Hexo文件夹，如下：



![命令行显示](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021423335.png)



执行命令：`hexo init blog`

blog 为创建的目录，你可以自己定义。使用终端进入到创建好的文件夹blog

执行命令用于更新nodejs的模块：`npm install`

尝试执行命令：`hexo server`

如果有如下输出

```c
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```

说明Hexo的服务已经启动成功了，你可以在你的浏览器访问就会出现你的博客（个人网站）主页了

复制上文中的`http://localhost:4000`并在浏览器中打开

这样，你就可以看到你的网站在本地的部署

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021423931.webp" alt="Hexo界面" style="zoom:67%;" />





## GitHub

### 新建仓库

前往 [GitHub](https://github.com/) 注册属于你的账号 (请及时验证邮箱地址)

新建一个仓库，而且得确保你的仓库是 `public`，同时，仓库名最好是`用户名.github.io`

打开 `Git Bash` ，运行下面的命令 :

```C
ssh-keygen -t rsa -C "{{你的电子邮箱地址}}"
```

连续 3 次回车，最终会在用户目录下生成个包含公钥私钥等数据的目录 ( 一般是 `C:/Users/{{你的用户名}}/.ssh/` ) 。

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021424793.jpeg" alt="ssh密钥配置" style="zoom:80%;" />

打开这个目录，找到 `id_rsa.pub` 文件，用记事本打开并复制里面的内容

粘贴到GitHub仓库的 `Key` 中，`Title` 填你喜欢的，点击保存 ( `Add SSH Key` ) 

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021425424.jpeg" alt="在GitHub中输入密钥" style="zoom:80%;" />





### 设置 Git

运行下方命令 :

```python
git config --global user.name "{{你的 GitHub username}}"
git config --global user.email "{{你的 GitHub 注册邮箱地址}}"
```



### 验证是否成功

打开 `Git Bash` ，运行下面的命令 :

```python
ssh -T git@github.com # 此处邮箱地址不用改
```

如果提示 `Are you sure you want to continue connecting (yes/no)?` 请输入 `yes` 并回车。

```python
Hi {{你的 GitHub username}}! You've successfully authenticated, but GitHub does not provide shell access.
```

看到这个信息则说明配置成功。



### 部署到 GitHub

完成上面的步骤后，你应该能在本地进行预览了，接下来就是推送网站到 Github Pages 了，然后我们就能被其他人访问了。

只需要在我们刚才的博客根目录中的站点配置文件 `_config.yml` ，设置为你的个人仓库名即可

_config.yml在你的Hexo文件夹下面

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021425834.png" alt="config.yml地址" style="zoom: 67%;" />

记事本打开它（VSCode，NodePad++也OK）

找到最后一行，修改它

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021430181.png" alt="deploy修改" style="zoom:80%;" />

在你新建的仓库这里，可以找到repo，复制它

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021430661.png" alt="repo链接" style="zoom: 67%;" />



在Hexo文件夹这个位置，点击`GitBash`，然后依次执行以下命令

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021430498.png" alt="GitBash" style="zoom: 75%;" />

```python
hexo clean
hexo g
hexo d
```

完成上述步骤之后，主要使用如下命令，就能将我们本地的内容推送到远程 GitHub 仓库了，然后在浏览器中访问：

`https://用户名.github.io`



## 主题优化

你可以通过更换主题，并写上你的名字以便修改自己网站的信息并美化它

我用的是icarus主题：https://github.com/AlphaLxy/hexo-theme-icarus

你可以通过链接去下载它，并通过参考文档（[Icarus中文文档](https://ppoffice.github.io/hexo-theme-icarus/Configuration/icarus%E7%94%A8%E6%88%B7%E6%8C%87%E5%8D%97-cdn%E6%8F%90%E4%BE%9B%E5%95%86/)）改变它

最后效果如下:

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431290.png" alt="icarus主题" style="zoom:67%;" />



## 镜像加速

我们知道，GitHub是国外网站，所以有时候访问会很慢，这时，我们可以尝试用[vercel](https://vercel.com/ )加速博客访问。

### 注册账号

进入登陆页面：https://vercel.com/login，使用 GitHub 账号登陆即可、



### 导入项目

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431655.png" alt="vercel" style="zoom: 50%;" />

点击`Continue`，进入如下界面。

这大概就是大家看到的页面，点击 `New Project` 按钮，然后点击 `Browse All Templates →` 按钮，在第 4 行的第 1 个就是 Hexo

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431403.png" alt="vercel-Hexo"  />

点击进去，可以看到图片所示的内容

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431928.png" alt="映射仓库"  />

选择灰框勾选的内容，也就是你的 GitHub，然后点击 `Select` 按钮，然后点击 `GitHub` 在 `GIT SCOPE` 处选择你想要储存 Hexo 的用户，在 `REPOSITORY NAME` 处填上你想要储存 Hexo 的仓库(你Hexo存放的仓库)，并勾选 `Create private Git Repository` 选项，这个选项是将该仓库设为私有仓库，对他人不可见。然后点击 `Continue` 按钮，然后什么都不需要修改，直接点击 Deploy，等待它自动完成构建，3 分钟之内完成，并分发一个免费的二级域名，可以暂时用于使用（不会回收，但不建议使用）

![映射成功](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431674.png)

部署完成后，它就和你刚刚所填写的仓库绑定了，一旦你的仓库有什么变化，它就会自动同步部署，全过程大概 5 分钟可以完成部署。

这样，访问 Vercel 给你的域名（类似于`https://用户名.github.io-vercel/`），就能加速访问你的网站了



## 自定义域名

其实用上面的域名也可以访问，但为了显得专业点，我们可以买个域名绑定它（`https://用户名.github.io-vercel/`）

如：`https://www.yzfenglib.top/`

### 账户注册

进入 [Namesilo](https://link.zhihu.com/?target=https%3A//www.namesilo.com/%3Frid%3Dd27fa32do) ，点击右上角 **“Create New Account”** 注册账号。

\* 为必填内容，建议如实填写；勾选 “Keep my information private” 默认使用隐私保护，保护我们的注册信息。

==注意所有信息都必须是英文的==

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431208.png" alt="注册Namesilo" style="zoom: 67%;" />

最后输入验证码，**“Create New Account”**，会收到验证邮件，点击链接激活完成注册



### 购买流程

- **Step 1**

激活账户并登陆，进入主页，在搜索框中输入想要注册的域名，**“SEARCH”**

![验证](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431983.png)



- **Step 2**

勾选需要的域名后缀，**“REGISTER”**。如果该域名已被注册，会显示为橙色的 “Registered” 状态，绿色表示可注册。

![购买域名](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431546.png)



- **Step 3**

确认订单，设置域名续费规则、注册时长等。输入优惠码 `okoff` 或者 `go2think` ，点击 “Submit”应用，优惠一美元。

完成后点击 **“CONTINUE”** 进入下一步。



- **Step 4**

付款。支持支付宝、Paypal 等。支付宝付款：在右侧输入框填写支付宝绑定的邮箱，点击 **“GO”** 跳转扫码界面支付

![支付宝支付](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431037.png)

完成！🧡

我是在NameSilo购买的域名，所以就进入NameSilo的管理页面：

![域名解析](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431151.jpeg)

选中需要配置的域名，点击上方的Change Nameservers可以更换Name server（我这里已经改过了），点击右侧小小的蓝球可以配置DNS。

把Name Servers改成Vercel提供的两个server（把NameServer 3删除了，添上这两个，要一模一样）

![NameServer更改](https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431276.jpeg)

然后按Vercel的要求把DNS改为

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431497.jpeg" alt="DNS更改" style="zoom: 80%;" />



PS：NameServer、DNS可以在Vercel找到

进入这个Vercel deployment的界面：

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431449.jpeg" alt="进入deployment" style="zoom:67%;" />



点View Domains就会直接跳到配置域名的选项：

<img src="https://yzfzzz.oss-cn-shenzhen.aliyuncs.com/image/202204021431561.jpeg" alt="配置域名" style="zoom:80%;" />



输入你购买的域名（`https://www.yzfenglib.top/`）点击`add`就能绑定域名了

以后想要修改网站的话，只需要将改动push到GitHub上，vercel会自动把改动同步过来，完全不用管，超省心。

在一级域名配置好之后，也可以直接在vercel中使用二级域名，无需进行额外设置。



写作我用的是：Typora,PicGo+阿里云OSS，[Typora+PicGo+阿里云OSS搭建博客图床（超详细）](https://blog.csdn.net/muxuen/article/details/122441469#comments_20567956)配置写作环境



## 参考资料

[使用Vercel+GitHub快速构建Hexo博客](https://zhuanlan.zhihu.com/p/358739246)

[手把手教你使用Hexo+GiteePages搭建一个免费的个人博客（个人网站）-小白看了也能搞](https://www.jianshu.com/p/26a726baf70e)

[使用Vercel+GitHub快速构建Hexo博客](https://fmcf.cc/technology/400/)

[Namesilo 域名购买及使用教程（附 Namesilo 优惠码）](https://zhuanlan.zhihu.com/p/33921436)

[搭建个人网站（2）：Github和Vercel建站以及配置DNS](https://blog.csdn.net/qq_19363379/article/details/108119140)



