---
title: hexo+yilia主题使用微博评论箱
date: 2017-05-16 17:11:11
tags:
	- hexo
	- yilia
	- 评论系统
	- 技术
---

昨天新搭好博客之后就一直想要添加一个留言版方便和大家交流（虽然跟没有人），我使用的是[litten大神的yilia主题](https://github.com/litten/hexo-theme-yilia)，里面本身就帮助我们添加了畅言、多说、网易云跟帖、Disqus四种评论系统，我们只需要修改主题根目录下的`_config.yml`文件，配置**appid**就可以使用了，非常方便。

但是。。。我的环境是gitpage+hexo，并且没有自己的域名，而畅言需要域名的备案号，无法申请；网易云跟帖判断github.io url已存在，无法申请；多说将在6月关闭，不推荐使用；Disqus在墙内使用不方便，不推荐使用。。。于是我只能另谋出路。
<!--more-->
经过多方搜索，我发现[友言](http://www.uyan.cc/getcode)和[微博评论箱](http://open.weibo.com/widget/comments.php)两个选择不错，于是开始添加，先说说友言的配置吧。

### 友言踩坑

其实友言没什么好坑的，直接注册后都不用邮箱认证就能自动分配到**uid**，然后获取到代码直接插入到界面中就可以了（不会修改主题代码的可以参考下面微博插入代码的方法）。但是我插入之后无论是本地测试还是更新到gitpage上测试都没有出现评论框，场面异常尴尬，查了很多资料也没有个解释，于是保留了代码并且给客服反馈了，等有了解决方案再来更新。

### 微博评论箱接入

然后讲讲微博评论箱的接入。首先是去[新浪微博开放平台](http://open.weibo.com/)申请个帐号（当然直接微博登录也是可以的）。接着[创建网站](http://open.weibo.com/webmaster/add)：

![创建网站](/imgs/weibo_create_web.png)

这里填入网站名称和域名可以是gitpage的地址，例如:小明的博客；djming.github.io。

验证方式我选择了第一种：在首页添加标签。首先复制上面这段代码，然后找到主页所在的页面，由于我之前没有接触过这类开发，查了很久文档才找到:![head地址](/imgs/head_path.png)
我们就是要修改这个`head.ejs`文件，这里面包含了我们博客页面的`<head>`标签内容，`hexo-theme-yilia`是我的主题文件夹名，如果用的不是这个主题可以自行去找，根据hexo官方文档的规则，主题目录应该是大致相同的。
![微博配置](/imgs/weibo_config1.png)
在这里随便找个地方把`<meta>`标签插进去就行了，然后执行`hexo d`把我们的项目更新到gitpage，访问自己博客确认头信息已经被修改![确认头信息已被修改](/imgs/weibo_confirm_head.png)
接着就点击`验证并添加`进入了漫长的等待，不知是不是微博的服务器访问gitpage特别慢，这个过程我去查询了解决友言问题的各种资料，还顺便出去给朋友送了个东西，回来看见页面还卡在这，一刷新提示`网站名称已存在`才明白已经创建成功了，现在进入`我的应用`就可以看见自己刚才申请的网站啦！

接下来我们回到[评论箱API](http://open.weibo.com/widget/comments.php)网站可以看到这里已经和我们第一次进来不同了，需要**注意**的是在这个页面的第一步第二部中配置的不同会直接导致第三步`复制代码`框中产生的代码不同，因为评论框的样式都被写在了WBML中，第三步复制代码需要增加XML命名空间，这里依然进入我们刚才编辑的`head.ejs`，将`<html>`标签修改为`<html xmlns:wb="http://open.weibo.com/wb">`，并且在head中添加`<script src="http://tjs.sjs.sinajs.cn/open/api/js/wb.js?appkey=你的appkey" type="text/javascript" charset="utf-8"></script>`，最后就是将评论箱的代码复制到评论箱应该出现的地方了，在我使用`yilia`主题中，我直接写进了`/theme/yilia/layout/_partial/article.ejs`的末尾，然后执行`hexo clean && hexo g && hexo server`查看效果。![评论框](/imgs/pinglun.png)

其实可以在hexo服务运行时修改配置文件以及.md文件，再刷新页面就可以看到效果了，不需要每次clean再重启。

总结一下申请评论箱的坑：
	
1. 验证过程太慢，不过是能够申请成功的，你可以喝杯咖啡。
2. 我选择留言框的样式是不显示边框，但是实际效果还是显示了，我的解决方法是将最后需要部署WBML修改为`<wb:comments url="auto" border="n" brandline="n" fontsize="12" width="auto" appkey="你的APPKEY" ></wb:comments>`也就是添加了`border="n"`
3. 身份验证和审核有点麻烦，需要身份证扫描照。

最后要说的是新浪的评论箱需要进行用户身份认证后再上线，其实也比畅言的好不了多少，不过对于没有域名也不打算买的小伙伴可以尝试一下，希望友言的问题能够尽快解决，如果你知道什么好的留言系统也请告诉我哟~

---

写完以上文字后我才想起有个叫做[来必力（来♂比利）](https://livere.com)的评论系统忘记了，于是去测试了一下。。。异常简单，注册完成后插入代码到页面就行了，而且有个人免费，用户可以不登录留言。。。请忘了我上面写的东西吧。。。