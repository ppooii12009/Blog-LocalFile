---
title: 图床-首篇文章封面 'Access-Control-Allow-Origin' 问题
date: 2020-02-29 20:51:50
tags: 
    - Linux那些事儿
categories:
    - tools
---

之前通过跨域访问CORS设置解决掉了绝大部分图片不能显示的问题，但在blog首页第一篇封面作为背景的文章，其封面依然无法显示，仍然报同样的 `Access-Control-Allow-Origin` 问题

> 浏览器从一个域名的网页去请求另一个域名的资源时，域名、端口、协议任一不同，都是跨域
>
> 换句话说，只要协议、域名、端口有任何一个不同，都被当作是不同的域，之间的请求就是跨域操作

虽然不知道是什么操作导致的跨域请求(首页的域名已经被加入跨域访问白名单)，但为了防止中途有可能的更换端口、二级甚至多级目录不同导致的显示问题，对之前的跨域请求再做修改

```html
http://magivoker.com
http://ppooii12009.github.io
http://magivoker.com/*
http://ppooii12009.github.io/*
http://magivoker.com:*
http://ppooii12009.github.io:*
```

将来源Origin加入二级目录和端口的通配符，问题得以解决