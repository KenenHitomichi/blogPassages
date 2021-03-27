---
title: MC Forge Mod编写初探（一）：基于Eclipse的脚手架搭建
date: 2021-01-23 01:26:47
categories:
	- Minecraft
tags:
	- Forge
	- Java
---

## 前言

作为刚刚入坑Minecraft Mod编写的萌新，我觉得有必要记录一下我从头开始的自学过程，罗列一些学习过程中遇到的问题与解决方案，以供自己和他人日后进行参考。而作为最初的也是最容易出错的环境搭建过程，我认为一个详细的过程记录以及问题陈列是至关重要的。

------



## Java版本选择与下载

### 相关说明

很多人都觉得各种软件的版本当然是越新越好，其实不然。不同的教程或许仅适用于程序的某一个版本，每个程序的不同版本也可能存在不同的bug与运行方式。因此，选择程序版本是至关重要的一步，也是绝大部分错误产生的地方。

单纯针对Minecraft而言，Java8可以说是最成熟的运行与开发环境。因此，我十分推荐使用java8进行mod的开发（当然，java8的具体版本仍然见仁见智，我目前使用的是jdk8u275。需要着重强调的是，下载时请务必区分jdk（Java SE development Kit）和jre（Java Runtime Environment）。如果仅仅只是运行MC（或者其他Java程序），那么下载jre就足够了。但对于Mod编程来说，我们需要的是Java的开发工具，也就是jdk。

Java8推荐直接从Oracle官网下载。点击 <a href="https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html">这里</a> 进入下载（现在从官网下载需要注册账号）。

当然，也可以选择在清华等镜像源进行下载，但要注意的是，不要下载单纯的openj9的adoptopenjdk的镜像，要么使用官网版本，要么使用hotspot。原因将在讲述forge mdk的时候提到。

### 常见问题

Q：为什么我提示Java版本过高，无法启动MC？

A： MC无法支持Java12及以上的版本，请下载并使用Java8。

Q：启动器为何提示我无法找到Java？

A：请确保PATH路径以及JAVA_HOME等变量的正确设置。

------



## Forge MDK 下载与搭建

### 相关说明

点击 <a href="files.minecraftforge.net">这里</a> 下载 Forge MDK。

需要注意的是，新版本的Forge MDK与旧版本的搭建方式并不相同。我使用的是2847版本，如果使用的是不同的版本，请严格按照Forge官方档案进行。

接下来我会具体展示我进行的所有步骤：

1. 将下载下来的mdk解压到一个文件夹
2. 在该文件夹中打开命令行
3. 运行指令： gradlew setupDecompWorkspace
4. 显示成功后，运行指令：gradlew eclipse

在所有步骤都成功后，mdk的搭建就算完成了。由于forge的maven一言难尽，所以下载的速度可能会比较慢，需要耐心等待。

### 常见问题

Q：为什么我显示Failed？

A：大部分Failed一般是由于网络原因，可以考虑使用科学的方法或者多尝试几次。

Q：为什么我一直显示 Build Faild: Cannot Find Hunk Target？

A：这就是前文提到的，由于Forge MDK内部的问题，在使用Openj9的Java环境下会出现该报错。请参考第一节更换Java版本并使用命令 gradlew cleanCache 来清空缓存。最后再尝试相关指令。

Q：为什么提示找不到gradlew相关命令？

A：gradlew程序应当是从下载的MDK压缩包中解压出来的。请确保命令行当前路径为解压到的文件夹。在windows控制台中，可以尝试将gradlew换成 .\gradlew。

Q：为什么我提示没有setupDecompWorkspace？

A：在最新版本MDK中，不需要再运行gradlew setupDecompWorkspace，直接使用gradlew eclipse后导入即可。然而由于我采用的是2847版本，因此对最新版本后续的配置不甚了解，还请对照forge官方文档食用。

------



## Eclipse下载与配置

### 相关说明

在成功完成java和MDK的相关步骤后，我们可以说离成功只有一步之遥。接下来就是Eclipse的安装。

可以点击 <a href="https://www.eclipse.org/downloads/packages/release/2019-12/r">这里</a> 下载Eclipse。注意，Eclipse的版本必须为2019-12，最新的版本并不支持Java8。

当然，你也可以选择从国内镜像站下载Eclipse，只需确保版本为2019-12。

在打开Eclipse后，会新建一个workspace（不要将workspace直接设置为mdk解压到的那个文件夹，但是它的上级目录是可以的）。

随后，在Eclipse->File->Import->General中，选择Existing Projects into Workspace，将root directory设置为mdk解压到的文件夹，并导入相关工程。在确保没有error后，脚手架的搭建就完成了👏👏👏。

### 常见问题

Q：为什么我点击运行的时候显示无法运行？

A：确保程序没有错误的情况下，很可能是因为没有选择运行方式。点击菜单中的Run->Run Configurations->Java Application，选择xxx_Client（xxx是你的项目名），然后点击Run即可。

Q：为什么我的Run Configuration中没有xxx_Client，或者一运行就程序崩溃？

A：请确保MDK版本为2847。如若不是，请根据Forge官方文档进行配置；如果没有问题的话，可以考虑重新下载mdk并通过cleanCache清除缓存后重新搭建。

Q：为什么我无法导入项目？

A：如果项目成功显示在可导入项目中但点击导入不可，请确保workspace目录与mdk解压到的目录不一致，并且gradlew eclipse运行成功。

Q：为什么我的example报错，找不到net.minecraft相关的包？

A：请确保gradlew setupDecompWorkspace成功运行。

------



## 总结

在配置环境的时候，由于并没有完全按照教程来，所以也算是掉了很多的坑，遇到了各种各样的问题，感觉到水温逐渐升高。但幸好最后还是成功完成了相应的配置，虽然浪费了很多时间，也算是有惊无险吧。不想用Eclipse的同志可以使用IntelliJ IDEA，也是相当不错的一款IDE，有免费的版本可以使用，虽然我在这次搭建中用的是Eclipse但还是推荐一下。相关的教程可以在网上查阅。

如果正苦苦配置环境而总遇到莫名错误的你能够看到这篇文章并得到解答，也算是完成了这篇记录的使命，我会非常的开心。如果有什么别的问题遇到或者文章有什么错漏，欢迎在评论里询问、补充。

这也算我新博客的第一篇文章，也是新的开始吧。

<br>

Author：Hitomichi

<span style="color: red">注：欢迎转载，请标明出处</span>

