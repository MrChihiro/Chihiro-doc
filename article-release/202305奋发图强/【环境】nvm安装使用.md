# 【环境】nvm安装使用

## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]

## 前言

在开发和管理多个Node.js版本时，NVM（Node Version Manager）是一个非常有用的工具。NVM允许您在同一台计算机上安装和切换不同的Node.js版本，使您能够轻松地在不同项目之间进行切换。本文将指导您如何安装和使用NVM。

## 安装

第一步是安装NVM。以下是在Linux和Mac OS上安装NVM的步骤：

### 1、打开终端（Terminal）。

### 2、使用curl命令下载NVM安装脚本。在终端中输入以下命令：

```cmd
bashCopy code
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```

### 3、执行安装脚本后，重启终端或运行以下命令以应用更改：

```cmd
bashCopy code
source ~/.bashrc
```

对于Mac OS用户，可能需要使用~/.bash_profile而不是~/.bashrc。

###  4、验证NVM安装是否成功。在终端中运行以下命令：

```cmd
cssCopy code
nvm --version
```

如果显示NVM的版本号，则表示安装成功。

## 使用

接下来，我们将学习如何使用NVM安装和管理Node.js版本。

### 1、安装Node.js版本。在终端中输入以下命令：

```cmd
phpCopy code
nvm install <node_version>
```

### 2、将`<node_version>`替换为您想要安装的特定Node.js版本号。例如，要安装Node.js的最新稳定版本，您可以输入：

```cmd
Copy code
nvm install stable
```

### 3、查看已安装的Node.js版本。在终端中运行以下命令：

```cmd
bashCopy code
nvm ls
```

它将显示所有已安装的Node.js版本，当前使用的版本将带有前缀`->`。

切换Node.js版本。如果您想切换使用不同的Node.js版本，可以使用以下命令：

```cmd
perlCopy code
nvm use <node_version>
```

### 4、将`<node_version>`替换为您想要切换到的特定Node.js版本号。例如，要切换到已安装的Node.js 14.17.0版本，您可以输入：

```cmd
perlCopy code
nvm use 14.17.0
```