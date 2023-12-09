# Windows端口冲突停止Jar包

## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]

## 简介

开发中，偶尔会遇到jar包启动后非正常关闭，导致端口在后台占用，下面给出作者常用的解决方法

## 解决方案

### 1.1 查看指定端口的占用情况

```sh
netstat -aon|findstr "端口" 
netstat -aon|findstr "80" 
```

### 1.2 查看对应进程的任务

```sh
tasklist|findstr "进程ID"
tasklist|findstr "17556"
```

### 1.3 删除任务

```sh
taskkill /f /t /im java.exe
taskkill /f /t /im node.exe 
```



