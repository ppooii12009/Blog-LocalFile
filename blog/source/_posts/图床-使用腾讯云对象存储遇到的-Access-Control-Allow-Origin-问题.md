---
title: '图床: 使用腾讯云对象存储遇到的 "Access-Control-Allow-Origin" 问题'
date: 2020-01-16 15:12:11
tags: 
categories: 
    - tools
---
昨天用腾讯云的学生机搭好了丝滑流畅的饥荒服务器之后，又打算利用服务器顺便搭建图床。但考虑到服务器配置~~当然是打游戏重要了~~以及相对应的配置问题，最终还是选择直接购买腾讯云的对象存储服务  
毕竟 花钱省事  
<b>而且价格也不是很贵</b>  

不过很快出现了一个大锅：将本地图片链接换成图床链接之后，首页文章的封面无法显示  

<div align = center>
    <img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/%E5%9B%BE%E5%BA%8A-%E4%BD%BF%E7%94%A8%E8%85%BE%E8%AE%AF%E4%BA%91%E5%AF%B9%E8%B1%A1%E5%AD%98%E5%82%A8%E9%81%87%E5%88%B0%E7%9A%84-Access-Control-Allow-Origin-%E9%97%AE%E9%A2%98/%E5%9B%BE%E7%89%87%E9%94%99%E8%AF%AF.png">
    <center class = "caption">封面图片完全无法显示</center>  
</div>

利用chrome的开发者工具很快找到问题  

<div align = center>
    <img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/%E5%9B%BE%E5%BA%8A-%E4%BD%BF%E7%94%A8%E8%85%BE%E8%AE%AF%E4%BA%91%E5%AF%B9%E8%B1%A1%E5%AD%98%E5%82%A8%E9%81%87%E5%88%B0%E7%9A%84-Access-Control-Allow-Origin-%E9%97%AE%E9%A2%98/CORS%E9%94%99%E8%AF%AF.png" height = 50% width = 50%>
    <center class = "caption">控制台错误信息</center>  
</div>  

可以看到错误是由于CORS跨域访问请求中的 <b>"Access-Control-Allow-Origin"</b> 被阻止，从而引发了403错误，无法访问图片  

解决方法：在腾讯云<b>"对象存储"</b>控制台中，选择对应的存储桶，进入<b>配置管理</b>界面 --> 左侧菜单栏选择<b>基础配置</b> --> <b>跨域访问CORS设置</b>中进行对应设置  

<div align = center>
    <img src = "https://blog-imgbed-1252616544.cos.ap-shanghai.myqcloud.com/blog/%E5%9B%BE%E5%BA%8A-%E4%BD%BF%E7%94%A8%E8%85%BE%E8%AE%AF%E4%BA%91%E5%AF%B9%E8%B1%A1%E5%AD%98%E5%82%A8%E9%81%87%E5%88%B0%E7%9A%84-Access-Control-Allow-Origin-%E9%97%AE%E9%A2%98/CORS%E8%AE%BE%E7%BD%AE.png" height = 60% width = 60%>
    <center class = "caption">设置界面</center>
</div>  

<b>:warning: 域名必须以 http 或 https 打头，不可以省略，具体选择根据实际情况. 如果不做其他处理，github托管的个人主页默认是 http 打头</b>