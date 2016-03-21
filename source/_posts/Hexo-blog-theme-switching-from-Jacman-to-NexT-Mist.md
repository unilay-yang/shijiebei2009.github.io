title: "Hexo博客主题从Jacman切换到NexT.Mist"
date: 2016-03-20 23:53:34
tags: [Hexo]
categories: Git/GitHub

---

####杂感
难得一个周末，经过五天的工作之后，本来是打算周六周日在睡觉中度过，因为最近工作确实挺累，倒不是工作有多繁忙，而是路途遥远，每天上下班路上花费三个小时，特别是早高峰挤地铁，那真是个体力活啊，每天都是七号线静安寺转二号线，换乘之前，并不能太深入地铁车厢内部，否则到了换乘站你可能根本就挤不下车去了。

最近博客流量有渐增趋势，可能是之前未被百度收录，而近期被百度收录的缘故吧，可见我天朝还是百度的天下，哎，万恶的墙啊（Great Firewall of China）！流量渐增，访客自然增加，所以也会有猎头加我微信，我也在考虑要不要把我的个人信息撤掉，貌似泄露的有点多了。同时也会有一些人因为博客的内容加我，请教谈不上，可能是一些同学在做相同的事情的过程中遇到了一些问题，而我可能刚好做过这方面的事情而已，希望有事的同学或朋友直接电联我即可，因为这是我认为最节约我们双方时间的沟通方式，确实没有太多时间去和你在微信上慢慢切磋。

貌似也有段时间没有发文了，主要是希望以后可以进一步提高博文质量，尽量发表一些互联网上还不存在或者没有解决方案类的文章，这样不至于人云亦云，还可以给别人提供比较多的参考价值，至于互联网上已经泛滥成灾的一些信息，以后在博文中尽量不再予以提供，不做重复发明轮子的工作。

扯远了，本来是打算睡觉度周末来着，但是博客的**Jacman**主题越看越觉得不顺眼，始终觉得有一种**too young**、**too simple**、**too naive**的感觉。而我素来想要一个简洁的主题，终于发现**NexT.Mist**主题足够优雅、足够简洁、是我喜爱的风格，于是果断手贱，开始折腾起来，还好在有以前折腾的基础之上，再次折腾就比较简单了，好多东西已经驾熟就轻了，本着能省力就省力的原则，很快更改完毕，主要还有一些细节在调整中。

说一点最近有待提高的地方就是对时间的把控，每天都有大把的时间花费在了各种软文之中，越来越觉得实在是一种浪费。自从微信火了之后，相信大家越来越觉得时间不够用了，到处都是软文，到处都是鸡汤，到处都是某人生活中的苦难，我不是说苦难不好，也不是说你的苦难不应该公之于众，而是在这个社会，苦难太多，我们不能靠着每天去消费苦难而存活，你有时间把你的苦难公之于众供大家消费，倒不如去干点活来的实在，这就是我的观点，也是我本人行动的格言，所以在这个大家即将毕业的季节，同学们旅游的旅游，回家的回家，玩的玩，泡妞的泡妞，憋在宿舍整天打游戏的也大有人在，而我还是整天苦逼哈哈，一天不拉的跑去公司上班，赶着来回三个多小时的地铁，每天挤得像狗一样累的半死，不为别的，只因为我知道，我不努力没人替我努力，而且还有那么好的媳妇在等着我去给她幸福呢。

所以我还是那句话，人生的竞赛没有公平可言，但是只要起步了，就只顾风雨兼程。在不忘记享受沿途风景的时候，大家也要记得赶路哦。

最后把我本次更改主题参考的文章放在末尾，有需要的自行点击，不谢。
####NexT.Mist主题相关
本次更改主题主要参考如下内容
- [NexT官方指南](http://theme-next.iissnan.com/)
- 在博文标题下面添加文章热度方法参考[这里](http://prozhuchen.github.io/2015/10/01/Hexo%E5%8D%9A%E5%AE%A2%E7%AC%AC%E4%B8%89%E7%AB%99/)
- 在站点概览中添加友链参考[这里](http://prozhuchen.github.io/2015/10/04/Hexo%E5%8D%9A%E5%AE%A2%E7%AC%AC%E4%B9%9D%E5%8F%88%E5%9B%9B%E5%88%86%E4%B9%8B%E4%B8%89%E7%AB%99/)
- 修改侧边栏头像下面的字体及颜色参考[这里](http://fancyluo.com/2015/09/18/2015-09-18-hexo-next-update/)，修改的文件具体位置是在`D:\hexo\themes\next\source\css\_common\components\sidebar\sidebar-author.styl`中的**font-size**，将其调大即可。
```css
.site-description {
  margin-top: 5px;
  font-size: 16px;
  color: $site-author-name-color;
  /*
  font-size: $site-description-font-size;
  color: $site-description-color;
  */
}
```
- 修改代码块中字体大小，主题默认的代码块字体偏小，找到`D:\hexo\themes\next\source\css\_common\components\highlight\highlight.styl`，修改**font-size**属性即可
```css
$code-block
  background: highlight-background
  margin: 20px 0
  padding: 15px
  overflow: auto
  font-size 15px
  color: highlight-foreground
  line-height:  $line-height-code-block
```
- 为博客添加搜索框，进入[swiftype](https://swiftype.com/)，点击自己的**Engine**->**Install Search**->**Search Field**后的**edit**，选择**Element ID**，填**#st-search-input**之后选**Save**，继续跳转后点**Activate Swiftype**按钮即可完成**swiftype**的所有配置了。拷贝你的**Engine**的**Install Search**中得到的代码如下
```javascript
< script type="text/javascript">
  (function(w,d,t,u,n,s,e){w['SwiftypeObject']=n;w[n]=w[n]||function(){
  (w[n].q=w[n].q||[]).push(arguments);};s=d.createElement(t);
  e=d.getElementsByTagName(t)[0];s.async=1;s.src=u;e.parentNode.insertBefore(s,e);
  })(window,document,'script','//s.swiftypecdn.com/install/v2/st.js','_st');

  _st('install','这里是KEY','2.0.0');
</script>
```
    然后在**Next.Mist**主题下的`_config.xml`中启用即可如下
```
swiftype_key: 这里是KEY
```
- 把侧边栏头像变成圆形，并且鼠标停留在上面发生旋转效果，参考[这里](http://fancyluo.com/2015/09/18/2015-09-18-hexo-next-update/)，具体修改文件的位置是`D:\hexo\themes\next\source\css\_common\components\sidebar\sidebar-author.styl`中的内容如下
```css
.site-author-image {
  display: block;
  margin: 0 auto;
  max-width: 96px;
  height: auto;
  border: 2px solid #333;
  padding: 2px;

  /* start*/
  border-radius: 50%
  webkit-transition: 1.4s all;
  moz-transition: 1.4s all;
  ms-transition: 1.4s all;
  transition: 1.4s all;
  /* end */
}
/* start/
.site-author-image:hover { 
  background-color: #55DAE1;
  webkit-transform: rotate(360deg) scale(1.1);
  moz-transform: rotate(360deg) scale(1.1);
  ms-transform: rotate(360deg) scale(1.1);
  transform: rotate(360deg) scale(1.1);
}
/* end */
```
- 设置社交链接，参考[这里](http://isnow.space/2016/01/29/Next%E4%B8%BB%E9%A2%98%E5%92%8CHexo%E6%9B%B4%E6%90%AD%E9%85%8D%E5%93%A6/)
- 添加右上角**Fork me on GitHub**，将如下代码添加到`D:\hexo\themes\next\layout\_layout.swig`底部的**body**标签之内即可，注意修改href为你自己的链接
```html
<a href="https://github.com/shijiebei2009"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://camo.githubusercontent.com/365986a132ccd6a44c23a9169022c0b5c890c387/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6769746875622f726962626f6e732f666f726b6d655f72696768745f7265645f6161303030302e706e67" alt="Fork me on GitHub" data-canonical-src="https://s3.amazonaws.com/github/ribbons/forkme_right_red_aa0000.png"></a>
```
- 需要添加音乐播放器和**High一下**的参考[这里](http://jijiaxin89.com/2015/08/21/%E7%8E%A9%E8%BD%AChexo%E5%8D%9A%E5%AE%A2%E4%B9%8Bnext/)
- 更详细的配置参考[这里](http://www.zerounix.com/2015/09/29/install-hexo-for-blog/)
- Hexo更改默认Google字体库，参考[这里](http://rainnie.me/2016/03/11/Hexo-%E6%9B%B4%E6%94%B9%E9%BB%98%E8%AE%A4Google-%E5%AD%97%E4%BD%93%E5%BA%93/)
- 利用git解决hexo博客多PC间同步问题，参考[这里](http://rainnie.me/2016/03/13/%E5%88%A9%E7%94%A8git-%E8%A7%A3%E5%86%B3hexo%E5%8D%9A%E5%AE%A2%E5%A4%9APC-%E9%97%B4%E5%90%8C%E6%AD%A5%E9%97%AE%E9%A2%98/)