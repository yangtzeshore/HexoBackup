---
title: hexo博客搭建和文件删除备份
date: 2024-10-14 14:30:31
tags: 
- Blog
categories:
- Blog
---

# hexo博客搭建和文件删除备份

### 痛苦的事件

​	    最近有些时间，更新一下荒废的博客，没想到hexo本地的博客源文件之前被误删除了。查了网上很多资料，还写了脚本爬网页转md，都没有办法解决，最后还是乖乖的复制html然后转到md，耗时两三天，终于解决了问题。不过，还是丢失了几篇文章，还有一些文章的格式短时间内，也没办法恢复了。

​	    最新的博客，更新了主题，没有继续使用next，使用了fluid主题，更加好看，没有那么呆板；另外，还添加了utterances评论功能，目前看比之前的discuz好用一些，就是加载慢。

​		这里hexo搭建很简单，网上已经说了很多了，简单安装npm和hexo后，初始化就好了。评论插件的话，utterances的配置有些特殊，我的主题是fluid，在utterances[官网](https://utteranc.es/)，点击配置好自己的博客仓库后，将生成的配置，一一复制到fluid的config文件里面，配置文件有个comment部分，格式都写好了（当然评论要打开即配置true），把复制的配置写进去，重新hexo deploy就好，打开网页后，稍等几秒钟，目前utterances的加载还是挺慢的。



### 备份

​		为了防止再次出现博客文件丢失的惨痛局面，有个备份的小技巧，如下（参考了[文章](https://wangjintian.com/2020/12/08/hexo%E5%8D%9A%E5%AE%A2%E5%A4%87%E4%BB%BD/)）。

1. 安装备份插件

   ```
   $ npm install hexo-git-backup@0.0.91 --save  //Hexo version = 2.x.x
   $ npm install hexo-git-backup --save  //Hexo version > 3.x.x
   ```

2. 到 Hexo 博客根目录的 `_config.yml` 配置文件里添加以下配置：(我使用了fluid主题，所以需要在`_config.fluid.yml`中同步配置)。

   ```
   backup:
     type: git
     theme: fluid
     message: Back up my blog
     repository:
       github: git@github.com:coneycode/hexo-git-backup.git,hexo
   ```

   参数解释：

   theme：你要备份的主题名称

   message：自定义提交信息

   repository：仓库名，注意仓库地址后面要添加一个分支名，比如我创建了一个 hexo 分支

3. 在github仓库新建分支，分支名与上述配置对应

4. 此时github仓库中应存在 master(或main)和hexo ，在提交的时候只需在 master 中使用以下命令备份你的博客即可：

   ```
   $ hexo backup
   ```

   或者使用以下简写命令也可以：

   ```
   $ hexo b
   ```



### 后续

​		后面会继续阅读utterances的官网，看看有没有好用的功能，把自己的博客丰富起来。



### 参考文章

- [1] [hexo博客备份](https://wangjintian.com/2020/12/08/hexo%E5%8D%9A%E5%AE%A2%E5%A4%87%E4%BB%BD/)