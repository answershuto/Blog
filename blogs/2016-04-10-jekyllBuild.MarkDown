本文主要记录一下自己在用jekyll在osX平台搭建个人博客开发环境、部署页面的过程以及遇到的一些坑，<br>有误之处望请包涵指正。

>>![img](/img/jekyllBuild/octojekyll.png) 

1.jekyll是什么？
---

>简单地说jekyll就是一个简单免费的blog生成工具，类似WordPress，但是又不像WordPress一样需要一个php以及mysql部署的后台应用程序。简单来说jekyll可以生成一个静态的blog，它可以部署在Github上，同时也可以绑定自己的域名。


2.前期准备
---

>＊ 一个脚本编辑器 <strong>Sublime Text</strong>

>＊ Terminal(终端)

>＊ Github


3.买域名
---

>这步我没有做，直接附上[传送门](http://jingyan.baidu.com/article/fec4bce20d364bf2618d8b39.html)  (怎样购买GoDaddy域名).

>如果没有购买域名，那建站完成之后只能使用github上默认的域名访问自己的blog

>例如:[http://answershuto.github.io/](http://answershuto.github.io/)


4.注册github创建代码仓库
---

>### 1.注册github，这个简单直接上个[传送门](http://jingyan.baidu.com/article/455a9950abe0ada167277864.html).


>### 2.创建代码仓库

>>[1]登录[Github](https://github.com)

>>[2]创建代码仓库

>>![img](/img/jekyllBuild/newRepositery.png ) 

>>[3]将仓库名字命名为userName.github.io，之后会用这个域名访问自己的blog，比如我这里使用了[answershuto.github.io](http://answershuto.github.io/)

>>![img](/img/jekyllBuild/RepositeryName.png )

>>[4]点击Create repository


5.搭建本地调适环境以及安装jekyll
---

>### 1.由于为的mac环境osX上面已经自带的了Ruby的环境，所以直接安装jekyll

>### 2.打开终端，执行  $gem install jekyll 

>> 第一次搭建环境的时候完全不知道怎么搞啊，运行的时候一直报错，在网上找了好多方法，有的说要用翻墙啊什么的，最后使用了淘宝镜像解决了。

>### 3.在终端执行  $ jekyll new blog  可以新建一个jekyll生成的blog，当然也可以从网上或者github上找一些别人提供的开源模板，效果都不错，提供一个[传送门](http://jekyllthemes.org),用git clone到本地，或者是fork到自己的github上。

>### 4.将本地项目上传到之前建立的github代码仓库上。(注：发布到Github上后可能需要有一段时间的延迟才可以访问)

>> $git init

>> $git add README.md

>> $git commit -m "first commit"

>> $git remote add origin https://github.com/answershuto/answershuto.github.io.git

>> $git push -u origin master

>### 5.在浏览器中输入 userName.github.io即可,例如[answershuto.github.io](http://answershuto.github.io/)

>### 6.本地调试

>> 进入之前jekyll new blog 生成的目录下或者是git clone出来的目录下，

>> $cd blog

>> 执行 $jekyll serve

>> 在浏览器中输入 localhost:4000  (注：jekyll默认用4000端口，可以在配置中修改该端口号)

>### 接下来就可以愉快地修改自己blog的代码，打造一个自己喜欢风格的blog了.


6.jekyll博客基本用法
---

>### 1.jekull目录结构

>> _site目录是最终自动生成的目录.

>> _post目录为存放文章的目录，存放的格式为 年－月－日－题目.MarkDown.

>> _layouts目录为布局文件存放目录.

>> _includes目录存放一些组件，在其他文件中可以引用该目录中的文件

>> _config.yml存储全局配置 

>### 2.MarkDonw语法 附上学习[传送门](http://wowubuntu.com/markdown/#philosophy)

>### 3.接下来就可以根据一些前端的支持修改自己的页面，定制自己专属风格的blog。
