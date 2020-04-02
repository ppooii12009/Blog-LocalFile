---
title: problems of downloading videos from bilibili with you-get
date: 2020-03-29 20:03:57
categories: 
	- tools
cover: https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/problems-of-downloading-videos-from-bilibili-with-you-get/cover.png
---

( 本来应该几天前就写好的，一不小心就咕咕咕了好几天到现在.....

前几天用pr剪了一个生日视频，需要从B站搞点名场面素材来，但众所周知B站是不提供下载功能的. 想起了若干个月前帮某姐妹下载B站视频用来比赛，当时DDL太紧，而且也只有一个视频要下载，于是权宜之计是用手机的缓存功能先把视频缓存到本地，然后找到对应的本地存储目录，再把缓存下来的音频和视频流混流到一起

( 但不得不说B站真有你的，缓存都要音视频分开

但这次时间稍微充裕了一点点，况且不止一个视频，而且用 Android 手机的原生文件系统去找某个在好几层目录下的媒体文件实在是太麻烦了，于是去知乎上 search 了一下，搞到一手黑科技 ***you-get*** 

[传送门 : you-get]: https://github.com/soimort/you-get	"这是一扇神奇的传送门哦~"

如果网络不好或者不会用梯子的同学

[传送门 : 怎样在电脑上下载哔哩哔哩的视频？ - 见长的回答 - 知乎]: https://www.zhihu.com/question/41367609/answer/625032725	"不用梯子的传送门~"

就在我兴冲冲安装好 you-get 然后打开命令行准备爽一下的时候，问题出现了

<div align = center>
	<img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/problems-of-downloading-videos-from-bilibili-with-you-get/error1.png">
    <center class = "caption">问题似乎出在url有多余的参数</center>
</div>



猜想是由于URL的格式不对，因为我是从搜索功能进入这个页面的，和一般的bilibili的链接格式上会差一些参数. 于是我从up主的主页又进入了一次这个视频页面，得到了一般格式的URL

```http
https://www.bilibili.com/video/BV14x411t7r5
```

再次尝试，然鹅

<div align = center>
	<img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/problems-of-downloading-videos-from-bilibili-with-you-get/error2.png">
    <center class = "caption">oops, something went wrong</center>
</div>
之后的一阵子我一度懵逼了几十分钟，忽然敏锐的发现了视频的标识符从 av 号变成了 BV 号，紧接着又去B站看了一下，还真就巧了....在我做视频的两天前刚刚宣布所有的av号转为BV号......

但官方也同时表示，原有视频的av号会继续保留，并且仍然可以使用av号访问相应的视频。所以问题大概率是you-get只能解析用av号表示的链接，而对于B站的新改动带来的问题还没有来得及update

我当天也去了 Pull requests 部分看了一下，已经有人反映并尝试修复这个问题，且 all checks have passed, no conflicts. 今天我又去看了一下，又有了很多新的关于此问题的 pull request，很多已经 closed. 我当天看到的第一个 request 也已经被 merge 了

<div align = center>
	<img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/problems-of-downloading-videos-from-bilibili-with-you-get/merge.png">
    <center class = "caption">最新版本应该已经修复了这个问题，但我并没有实测</center>
</div>



回到av号和BV号的问题上来，虽然官方表示仍然保留所有的av号，但我并没有在网页端里找到任何明显的标识av号的地方，后来抱着试一试的心态点开了开发者工具....

开发者工具NB！

`Ctrl+F`  在开发者工具里使用搜索功能，直接输入 "av" , 搜索的第一条结果应该是一个 `<meta>` 标签，标签的 `content` 值就是带av号的视频链接地址

<div align = center>
	<img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/problems-of-downloading-videos-from-bilibili-with-you-get/av_address.png">
    <center class = "caption">双击复制即可</center>
</div>

回到命令行，用av号格式的URL即可正常下载视频

u1s1，速度还真挺快