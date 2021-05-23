# Chrome浏览器的渲染机制

## 零、文章梗概

+ 浏览器的进化过程
+ 现代浏览器的特征与结构
+ chrome浏览器架构
+ chrome浏览器的渲染过程
+ 初窥WebKit

## 一、现代浏览器的进化

1. 1990年，蒂姆·伯纳斯·李开发了第一个网页浏览器WorldWideWeb，后改名为Nexus。WorldWideWeb浏览器支持早期的HTML标记 语言，功能比较简单，只能支持文本、简单的样式表、电影、声音、图片等资源的显示。
2. 1993年，马克·安德森领导的团开发了一个真正有影响力的浏览器Mosaic，这就是后来世界上最流行的浏览器Netscape Navigator。
3. 1995年，微软推出了闻名于世的浏览器Internet Explorer。第一次浏览器大战开始，持续两年
4. 1998年，Netscape公司开放Netscape Navigator源代码，成立了Mozilla基金会。第二次浏览器大战开始，持续八年
5. 2003年，苹果公司发布了Safari浏览器。
6. 2004年，Netscape公司发布了著名的开源浏览器Mozilla Firefox。
7. 2005年，苹果公司开源了浏览器中的核心代码，基于此发起了一个新的开源项目WebKit(Safari浏览器的内核)。
8. 2008年， Google公司已WebKit为内核，创建了一个新的浏览器项目Chromium。以Chromium为基础，谷歌发布了Chrome浏览器。 至于这两者的关系，可以简单地理解为:Chromium为实验版，具有众多新特性;Chrome为稳定版。



## 二、现代浏览器的特征

### I、网络

### II、资源管理

### III、网页浏览

### IV、多页面管理

### V、插件和扩展

### VI、账户和同步

### VII、安全机制

### VIII、开发者工具

