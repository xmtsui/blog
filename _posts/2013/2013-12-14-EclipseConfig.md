---
layout: post
title: 教你配置Eclipse全局黑色主题
categories:
- SCM
---

IntelliJ IDEA出来之后，我完全是被他的Dark theme吸引过去的，因为想在eclipse上配色实在是麻烦。
编辑区域的高亮配色还好，Project Explorer部分的配色，就着实没辙了，只有修改系统自身的配色，这样其他的应用程序也跟着变了。。

由于开发环境大多基于Eclipse，再一次想到了改颜色。这次去搜了一下，结果看到国外有个大哥[Roger Dudler](http://blog.rogerdudler.com/post/38229973729/dark-juno-a-dark-ui-theme-for-eclipse-4)开发出来了个插件，可以解决这个问题。

##环境需求
* eclipse 4.2+ (目前是juno, kepler)

##全局黑色主题配置
1. [Dark Juno插件](http://rogerdudler.github.io/eclipse-ui-themes/)  
可以[点这里下载](https://github.com/downloads/rogerdudler/eclipse-ui-themes/com.github.eclipsecolortheme.themes_1.0.0.201207121019.zip)
2. 解压后，把jar包拷贝到eclipse的dropins文件夹下，然后重启。
3. 打开Preference，在General->Appearance界面选择Dark Juno

##编辑区域主题配置
1. [Eclipse color theme 插件](http://eclipsecolorthemes.org/)  
eclipse可以Install newsoftware 添加这个地址，然后安装http://eclipse-color-theme.github.io/update/
2. 安装好插件后重启，打开Preference，在General->Appearance->color theme界面选择你喜欢的主题。

##预览图
可以看下配置好后的预览图：darktheme+sublime text2主题
![eclipsedarktheme](/blog/assets/pic/eclipsedarktheme.png)
