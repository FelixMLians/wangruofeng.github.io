---
layout: post
title: "MacOSX10.11上用Octopress和GitHub搭建个人博客"
date: 2016-01-12 4:30:38 +0800
comments: true
tags: [iOS, Octopress, GitHub搭建个人博客, MacOSX10.11]
keywords: Octopress, iOS, GitHub搭建个人博客, Swift, Objective-c, MacOSX10.11
categories: iOS Skill
description: MacOSX10.11上用Octopress和GitHub搭建个人博客
---

序言经过一晚上的折腾，终于从`Hexo`成功转入`Octopress`,那么本文就来细说一下如何使用 `Octopress`+`GitHub Pages`搭建个人博客

### 为什么选择 Octopress & Github Pages?

* 免费且独立。把 Octopress 博客系统搭建到 Github Pages 虽是免费，但不失独立性，即便 Github 全站关闭，你也将有一份本地全站备份，随时可以重新恢复。不必受托管商之气，而且还免费，如果你愿意，甚至可以自行插入广告挣钱。
* 版本控制。写文章，建网站，做软件都需要修改，但有时候改完了又会后悔，如果有时光机就好了，Git 就是你的时光机。当然如果你不想了解这些看上去很唬人的 IT 名词，只是想写博客的话，请在需要的时候再研究这条的内容。
* 相对其他托管到 Github 上的博客程序，Octopress 更加成熟易上手。打个比方，[Jekyll](http://jekyllcn.com) 可以说是毛坯房，[Hexo](https://hexo.io) 和 [Octopress](http://octopress.org) 算是简装修，但相比 Hexo，Octopress 有更多装修范例和更多熟练的装修工人，更容易获取帮助。当然如果你只想住精装修的房子，那不得不花点钱上 [WordPress](https://www.google.com.hk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=8&cad=rja&uact=8&ved=0ahUKEwiOv6rSnqbKAhVGKJQKHefnDGAQFghGMAc&url=https%3A%2F%2Fzh.wikipedia.org%2Fzh-cn%2FWordPress&usg=AFQjCNGIb2sxhqKIuZ22-NHO6nclaYYOSQ&sig2=GoW6MP3lSb9RasgVQGFxnw) 了
* 使用 Markdown。[Markdown](https://zh.wikipedia.org/wiki/Markdown) 是现在最为流行的轻量级标记语言，也是已故的天才 Aaron Swartz 留给世人最好的礼物，窃以为每个在互联网上发布文章的人都该掌握。
* 按照官方的说法，Octopress 是个`A blogging framework for hackers`(「为黑客设计的博客框架」)，这很酷，你不觉得吗？

如果你之前没有写过博客，打算开始搭建自己第一个博客的话，其实也不妨试试 Octopress，免费还能学到东西，何乐而不为？


本文是在`OS X EI Capitan`系统上搭建一个基于`Octopress`的个人博客系统，记录搭建过程的各种坑，希望对有想搭建个人博客的朋友有所帮助。

本文是建立在你有Shell指令基础和Git操作基础之上，如果不了解的话，需要查阅相关资料。

先解释几个专业术语：

* [Ruby](https://www.ruby-lang.org/zh_cn/)
	* Ruby 是一种编程语言。Octopress 是用 Ruby语言 实现的。我们不需要对它有太多了解，只需要正确安装 Ruby 的环境（Ruby版本必须不低于1.9.3-p0，后面会详细介绍）及按步骤执行指令即可。
* [RubyGems](https://rubygems.org/)
	* RubyGems（简称 gems）是一个用于对 Ruby组件进行打包的 Ruby 打包系统。它可以用来查找、安装、升级和卸载软件包。我们也是通过它安装Octopress包的
* [RVM](https://rvm.io/)
	* RVM  是Ruby Version Manager的简称，是一款 Ruby 语言安装、管理的工具。我们对 Ruby 的操作是通过它的指令完成的。
* [Jekyll](http://jekyll.bootcss.com/)
	* Jekyll 一个简单的博客形态的静态站点生产机器。	Jekyll 有一套模板目录，可以将 Markdown文件（或者Textile）转换为静态网页，并生成一个完整的可发布的静态网站。
	* 同时，我们可以将产生的静态网站布置到 GitHub Pages 上，生成个人博客站点。
	* 想了解更多内容可以查看[中文文档](http://jekyll.bootcss.com/docs/home/)。
* [Octopress](http://niumeng.me/blog/2015/08/26/create-your-website-1-init/www.github.com/imathis/octopress)
	* Octopress 是基于 Jekyll 的博客框架。他们的关系就像 jQuery 与 js 的关系一样。
	* 它为我们提供了现成的美观的主题模板，并且配置简单，使用方便，大大降低了我们建站的门槛。
* [Git](http://rogerdudler.github.io/git-guide/index.zh.html)
	* 分布式版本控制工具，跟它有点类似的是SVN，只是两者使用场景不一样。
* [GitHub](https://www.github.com/)
	* GitHub 是全球最热的开源社区，程序界的 Facebook。它为我们提供代码托管服务，以及我们搭建博客所需要的 Pages 服务。
* [GitHub Pages](https://pages.github.com/)
	* GitHub Pages 是 GitHub 提供的一项服务。它用于显示托管在 GitHub 上的静态网页。所以我们可以用 Github Pages 搭建博客，当然我们也可以把项目的文档和主页放在上面。

通过以上内容，我们大概能够明白 Octopress 建站的原理：
我们使用基于 Jekyll 的 Octopress 站点生成工具，生成本地的静态网站。然后将静态网站托管到 GitHub 为我们提供的 GitHub Pages 服务上。访问 **username**.github.io 即可显示你的个人博客站了

---
明白了上面这些内容，下面进行具体的搭建工作：
### 第一步：安装 Ruby
Mac自带Ruby环境打开终端，安装 RVM ，终端执行指令：

	$ curl -L https://get.rvm.io | bash -s stable --ruby

接下来我们要查看自己的 Ruby 环境

	$ ruby -v

如果你的 Ruby 版本不低于 1.9.3-p0 可以忽略 Ruby 的安装（或升级），直接跳到安装 RubyGems 。 否则，我们执行之后的操作，终端执行指令：

	$ rvm install 1.9.3
	$ rvm use 1.9.3

然后安装 RubyGems， 终端执行指令：

	$ rvm rubygems latest

到这里第一步完成。我们可以再执行一次第一条指令 `ruby -v` 来查看当前 Ruby 的版本了。

### 第二步：安装Octopress
因为Mac系统自动git环境，所以我们不需要考虑git的安装。直接将 Octopress的项目clone到本地，在终端执行指令：

	$ git clone git://github.com/imathis/octopress.git octopress

完成后进入 octopress 的目录

	$ cd octopress

接下来，安装依赖：

	$ gem install bundler
	# 这时你可能会遇到没有权限的问题，那么我们需要加上sudo重新执行，并输入密码。
	$ sudo gem install bundler
	# 接下来执行：
	$ bundle install

这时你可能还会遇到问题如下：

	Fetching gem metadata from https://rubygems.org/...........
	Resolving dependencies...

	Gem::RemoteFetcher::FetchError: SocketError: getaddrinfo: Name or service not known (https://rubygems.org/gems/rake-10.4.2.gem)
	An error occurred while installing rake (10.4.2), and Bundler cannot continue.

这是因为被墙了，解决办法有两个： 一个是，可以使用自己的翻墙工具； 另一个，淘宝做了一个gem的镜像。我们需要在Octopress的文件目录下找到Gemfile文件，将其中的`source 'https://rubygems.org/'`改为`source 'https://ruby.taobao.org/'`

再重新运行`bundle install`就可以了。 这段内容可以参考 [bundle install 提示如下，是需要翻墙解决么](http://www.imooc.com/qadetail/59105)。

下面就可以安装 Octopress 的默认主题了，终端执行指令：

	rake install

这样一个最基本的个人博客站就产生了。

![Octoress init](http://ww3.sinaimg.cn/mw690/64124373gw1ezxvho05u3j20lx0ab764.jpg)

可以看出，现在显示得都是预设值，并不是我们想要的，所以需要修改Octopress目录下的_config.yml文件。

文件目录`_config.yml`

描述：保存配置数据。很多配置选项都会直接从命令行中进行设置，但是如果你把那些配置写在这儿，你就不用非要去记住那些命令了。

_config.yml 文件共分为3个部分内容

* Main Configs
* Jekyll & Plugins
* 3rd Party Settings

目前，我们只需要关注第一部分Main Configs。

	# ----------------------- #
	#      Main Configs       #
	# ----------------------- #

	#网站地址
	url: http://wangruofeng.github.io

	#网站标题
	title: 王若风的技术博客

	#网站副标题
	subtitle: 天天向上.

	#网址作者，通常显示在页尾和每篇文章的尾部
	author: Ace

	#搜索引擎
	#simple_search: http://google.com/search

	#网站的描述，出现在HTML页面中的 meta 中的 description
	#description:




对应填入你的个人信息，其中url为必填的，一般填GitHub仓库对应的连接，其内容大致就是 **username**.github.io ，这个地址我们会在后面步骤中获得。

### 第三步：集成 GitHub Pages

1.注册 Github 账号

这个没什么好说的，早晚需要，去 <http://github.com> 注册吧。

2.在 GitHub 上创建一个代码参考，项目名称命名规则为 **username**.github.io，username必须与用户名称一致。

3. 域名指向（可选）
如果你有自己的域名可用，可以在这时就配置好，毕竟解析起来需要一段时间，不如在我们搭建博客的时候让它开始，这样我们搭建完成后，基本上就可以直接用自有域名访问了。

我的是在[万网](http://wanwang.aliyun.com/domain/?utm_medium=text&utm_source=baidu&utm_campaign=st1&utm_content=se_2485)申请的，原价一年100多点现在有活动便宜的几块的都有，想要个性域名的可以去注册一个。

* 如果你用的是顶级域名，比如 wangruofeng007.com, 请创建两个 A 记录 (A Record) 分别指向 `192.30.252.153` 和 `192.30.252.154`.

* 如果你使用二级域名，比如 blog.wangruofeng007.com, 请将该域名的 CNAME 指向 [your_username].github.io, 把其中的 [your_username]换成你自己在 Github 上的用户名。

* 如果你暂时没有域名，这一步可以暂时不用管

Ps. 在创建过程中最好不要`添加忽略文件`和`README`文件。因为我们要把本地的 git 仓库同步到 GitHub 远程仓库中。如果再远程仓库中添加了其他文件，需要我们执行 pull 操作。除非你能非常熟练的使用 git ，否则不建议你制造不必要的麻烦。
接下来将本地代码仓库同步到 GitHub 上，执行终端指令：

	$ rake setup_github_pages

它会要求你绑定远程仓库的地址，此时只需要输入即可：

	$ git@github.com:username/username.github.com.git

这样就会将 Octopress 生成的静态站点与 GitHub 进行绑定了。

之后我们创建第一篇文章：

	rake new_post["title"]

然后会有一个名为 yyyy-mm-dd-Post-Title.markdown 的文件在 octopress/source/_posts 目录下生成，其中 yyyy-mm-dd 是你当时的日期。然后执行以下命令：

	cd source/_posts/
	vim yyyy-mm-dd-Post-Title.markdown

即可用 vim 编辑器编辑的刚才的文章了，好吧我知道你作为这篇文章的读者并不是一个能熟练使用 vim 的人，那么请在命令行输入 q！退出这个编辑器。如果你不想假装是个黑客的话，其实发布文章并不需要这么麻烦。

我们直接打开 `octopress/source/_posts` 文件夹，找到刚才生成的文件，用你喜欢的 Markdown 编辑器（免费的我推荐 [Mou](http://mouapp.com/)或者[Atom](https://atom.io)）或者文本编辑器打开，对文章内容进行编辑。

打开文件后，你会发现文章开头有这么一段信息:
	---  
	layout: post  
	title: "Post Title"  
	date: yyyy-mm-dd hh:mm:ss  
	comments: true  
	categories:  ""
	---

这其实是这篇文章的元数据：layout 暂时不要理会；title 是这篇文章显示在最终网页上的标题；date 部分是详细的文件生成时间，如 2014-01-28 03:35:00；comment 部分表示是否允许评论，目前显示是允许，如果想关闭评论，请改为 false；categories 指这篇文章的分类目录，请在后面引号中输入,不用引号也可以，多个分类用`空格`隔开，如果没有该目录，则会自动生成。请不要删除这段信息，在这段信息下面开始你的文章内容

这件事情给我们的启发是，以后发布文章，其实并不需要使用终端命令行生成文件。可以直接将自己写好的文章放到这个文件夹下面，当然请按照 `yyyy-mm-dd-Post-Title.markdown` 这样的文件格式命名，同时记得在文章前面添加元数据信息。这种做法生成的文章与上面的方法无异

生成的新文章在source/_post/目录下，文件名构成为时间和标题的拼音。我们可以用Markdown编辑器对文章进行修改。
之后生成静态站点，终端执行指令：

	# 生成静态站点
	$ rake generate

如果你想预览本地的站点，可以执行终端指令：

	# 预览静态站点
	$ rake preview

此时，可以使用浏览器打开`localhost:4000` 查看效果。如果没有问题可以将静态站点同步到 GitHub 远程仓库中，终端执行指令：

	# 同步内容
	$ rake deploy

你会发现我们的静态站点已经被 push 到 GitHub仓库的 master 分支上。稍等几分钟，访问 `username.github.io` (或者 `username.github.com`)，就会发现你的个人博客站已创建成功了。
如果你还想给自己的本地资源文件（如Markdown文件等内容）也同步到 GitHub 中，可以执行以下指令：

	$ git add .
	$ git commit -m "comment"
	$ git push origin source

这样我们的资源文件就会同步到 GitHub 的 source 分支了。

### 使用自己的域名（可选）
如果你有自己的域名，并且想指向这个新博客的话，请首先确保执行了第三步的`域名指向（可选）`的内容。如果没有执行，可以随时执行。
然后执行下面的命令，注意把 your-domain.com 换成你自己的域名。

	echo 'your-domain.com' >> source/CNAME

这句话的意思是在`source`分支下创建一个CNAME的文件并且将`your-domain.com`写入文件

然后再次执行以下命令：

	rake generate
	rake deploy

或者二合一

	rake gen_deploy

这样你就可以使用自己的域名了。域名解析需要一段时间，如果没有马上生效，请不要着急。如果长时间没有生效，请确保完整执行了 `域名指向（可选）`和`使用自己的域名（可选）`的内容。


现在我们完成了个人博客的初级搭建，足够满足我们的基本需求。

参考资料：

* 唐巧的博客: [象写程序一样写博客：搭建基于github的博客]（http://blog.devtang.com/blog/2012/02/10/setup-blog-based-on-github/）
* 觅珠人: [第一篇博文：用Octopress搭建博客系统](http://tchen.me/posts/2012-12-16-first-blog.html)
* 破船之家: [你好！github页面](http://beyondvincent.com/blog/2013/07/27/107-hello-page-of-github/)
* opoo.org: [Octopress 博客系统 —— a Blogging Framework for Hackers](http://opoo.org/octopress/)
* Ocotpress: [Octopress Documentation](http://octopress.org/docs/)
* Havee’s Space: [为 Octopress 添加多说评论系统](http://havee.me/internet/2013-02/add-duoshuo-commemt-system-into-octopress.html)
* colors4.us: [Octopress博客从零开始 III](http://colors4.us/blog/2012/01/08/octopressbo-ke-cong-ling-kai-shi-iii/)
* 浮生猎趣:[用Octopress在Github搭建博客，并绑定独立域名](http://blog.lessfun.com/blog/2013/11/27/create-blog-in-github-page-using-octopress-and-binding-domain/)
* Aster0id的个人博客:[制作个人博客站（一）：Mac系统下使用 Octopress + GitHub Pages 搭建个人博客](http://niumeng.me/blog/2015/08/26/create-your-website-1-init/)


备注：欢迎转载，但请一定注明出处！ <http://blog.wangruofeng007.com>
