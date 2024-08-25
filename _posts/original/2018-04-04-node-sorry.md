---
layout: page
title: 'The Node Version of the Recently Popular Sorry GIF Maker'
categories: [tech]
tags: [original]
excerpt: "The Node.js version of the recently popular sorry GIF maker. The server uses Koa and fluent-ffmpeg, and the API follows RESTful conventions."
---

> Author: Li Pinghai

The Node.js version of the recently popular sorry GIF maker, inspired by [Xuty's original Ruby version](https://github.com/xtyxtyx/sorry). This document also references the original documentation.

[Node-sorry repository](https://github.com/LiPinghai/node-sorry)

[Online demo](http://gif.lipinghai.cn/index.html?from=segmentfault)

Feel free to open an issue if you have any questions, and please star the project if you like it. Thank you!

![Image](http://imgsrc.baidu.com/forum/pic/item/a98c4bc6a7efce1b86b10ccca351f3deb58f6585.gif)

## Project Description

* The server uses Koa and fluent-ffmpeg, with subtitles and GIFs stored in the cache directory.
* The API follows RESTful conventions.
* The pages are rendered with EJS, and the build script `build.js` generates the pages and resources in the `dist` directory.
* Project configuration is in `config.js` (remove or replace the analytics code for deployment).

## Source Code Structure

```
├── package
├── package.lock
├── common                  # Utilities
├── server                  # Node.js source code
├── view                    # Page source code
├── template                # GIF templates
├── config.js               # Configuration
├── build.js                # Page build script
├── README.md
└── cache                   # GIF and subtitle cache
```

## Other Versions

- [Original Ruby version](https://github.com/xtyxtyx/sorry) by @Xuty
- [Python version](https://github.com/East196/sorrypy) by @East196
- [Java version](https://github.com/li24361/sorryJava) by @li24361
- [C# ASP.NET version](https://github.com/shuangrain/SorryNet) by @shuangrain
- [WeChat Mini Program](https://github.com/CoXier/iemoji-wechat) by @CoXier

## API

To create a GIF:
```
POST {host}/api/{template_name}/
{
    subtitle:['OK',.....]
}

# Returns the GIF's hash
-> 200 
{
  status: 200,
  data: 'c2f4069ed207dc38e0f2d9359a2fa6b7'
}
```

To get the GIF:
```
GET {host}/api/{template_name}/{gif_hash}
```

Currently supported template names are:
```
- sorry
- wangjingze
```

## Deployment Guide

### Installation
```
npm i
```
`@ffmpeg-installer/ffmpeg` may fail to install; try running `npm i` a few more times.

### Build the Pages
```
npm run build
```

### Deployment

For local development, use `npm run server`.

For online deployment, it's recommended to use `pm2` for management. First install it with `npm i pm2 -g`, then start the project with `pm2 start server/index.js`.

## Creating Subtitle Templates (template.ass)

First, use Aegisub to create subtitles for the template video and save them as `template.ass`. (You can refer to this [Aegisub tutorial](https://tieba.baidu.com/p/1360405931).)
![Image](https://dn-coding-net-production-pp.qbox.me/56a213df-9ff7-41e0-9b6c-96b1f0fe2cb6.png)

Then replace the text with the template string `<%= sentences[n] %>`.
![Image](https://dn-coding-net-production-pp.qbox.me/6b07bc65-c3d7-4251-aad2-bd7b05af9102.png)
