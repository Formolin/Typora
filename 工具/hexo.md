# 搭建博客

## 服务器配置

```sh
git --version // 如无，则安装
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
yum install -y git
```

```sh
useradd joker //用户名
passwd joker // 设置密码123456
su joker // 这步很重要，不切换用户后面会很麻烦
cd /home/joker/
mkdir -p projects/blog // 项目存在的真实目录
mkdir repos && cd repos
git init --bare blog.git // 创建一个裸露的仓库
cd blog.git/hooks
vi post-receive // 创建 hook 钩子函数，输入了内容如下

```

```sh
#!/bin/sh
git --work-tree=/home/joker/projects/blog --git-dir=/home/joker/repos/blog.git checkout -f
```

```sh
chmod +x post-receive
exit // 退出到 root 登录
chown -R joker:joker /home/joker/repos/blog.git // 添加权限
```

```sh
在本地电脑 找一个空白文件夹，如果能把空仓库拉下来，就说明 git 仓库搭建成功了
git clone joker@server_ip:/home/joker/repos/blog.git
```

建立`ssh`信任关系，在**本地电脑**

```sh
ssh-copy-id -i  /Users/liujiang/.ssh/id_rsa.pub joker@server_ip

ssh joker@server_ip // 测试能否登录,此时的 ssh 登录 git 用户不需要密码
exit
```





如果报错：ERROR Deployer not found: git

在站点目录下

```sh
[root@instance-6k0i5s0c ~]# cd /home/joker/projects/blog/
[root@instance-6k0i5s0c blog]# npm install hexo-deployer-git --save
```

## 本机配置

#### 安装Node.js

#### 安装Git

#### 安装Hexo

在合适的地方新建一个文件夹，用来存放自己的博客文件

定位到该目录下，输入`npm i hexo-cli -g`安装Hexo。会有几个报错，无视它就行

安装完后输入`hexo -v`验证是否安装成功。

然后就要初始化我们的网站，输入`hexo init`初始化文件夹，接着输入`npm install`安装必备的组件。

这样本地的网站配置也弄好啦，输入`hexo g`生成静态网页，然后输入`hexo s`打开本地服务器，然后浏览器打开<http://localhost:4000/>，就可以看到我们的博客

打开博客根目录下的`_config.yml`文件，这是博客的配置文件，在这里你可以修改与博客相关的各种信息。

修改最后一行的配置：

```
deploy:
  type: git
  repository: joker@server_ip:/home/joker/repos/blog.git //服务器仓库地址
  branch: master
```

### 写文章、发布文章

首先在博客根目录下右键打开git bash，安装一个扩展`npm i hexo-deployer-git`。

然后输入`hexo n "demo1"`，新建一篇文章。

然后打开`blog\source\_posts`的目录，可以发现下面多了一个文件夹和一个`.md`文件，一个用来存放你的图片等数据，另一个就是你的文章文件啦。

编写完markdown文件后，根目录下输入`hexo g`生成静态网页，然后输入`hexo s`可以本地预览效果，最后输入`hexo d`上传到github上。这时打开你的github.io主页就能看到发布的文章啦。





