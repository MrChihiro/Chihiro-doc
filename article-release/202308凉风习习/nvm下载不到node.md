

# nvm下载不到node

## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]



## 简介

在安装 `8.9.0`的时候报错：

- `The process cannot access the file because it is being used by another process.`：该进程无法访问该文件，因为它正在被另一个进程使用。
- `Could not download npm for node v8.9.0.`：无法下载节点v8.9.0的npm。

```sh
C:\Windows\system32>nvm install 8.9.0
Downloading node.js version 8.9.0 (64-bit)...
Complete
Creating F:\java\nvm\temp

Downloading npm version 5.5.1... Download failed. Rolling Back.
F:\java\nvm\temp\npm-v5.5.1.zip
Rollback failed. remove F:\java\nvm\temp\npm-v5.5.1.zip: The process cannot access the file because it is being used by another process.
Could not download npm for node v8.9.0.
Please visit https://github.com/npm/cli/releases/tag/v5.5.1 to download npm.
It should be extracted to F:\java\nvm\v8.9.0
```



### 本文关键词

`The process cannot access the file because it is being used by another process.`、`Could not download npm for node v8.9.0.`



## 实现步骤

### 1 找到`settings.txt`

- 在nvm 安装目录中，例如我本地：`F:\java\nvm\settings.txt`。

- 修改:

  ```text
  node_mirror: http://npm.taobao.org/mirrors/node/
  npm_mirror: https://github.com/npm/cli/archive/
  ```

完整配置`settings.txt`

```text
root: F:\java\nvm
path: C:\Program Files\nodejs
node_mirror: http://npm.taobao.org/mirrors/node/
npm_mirror: https://github.com/npm/cli/archive/
```

### 2 重新安装

- 安装之前先卸载之前的包`F:\java\nvm\temp`

#### 2.1 卸载

```sh
nvm uninstall <version>
```

#### 2.2 安装

```sh
nvm install 8.9.0
```

### 3 设置npm镜像源`改变全局的注册`

- 设置成淘宝源

```sh
npm config set registry https://registry.npm.taobao.org
```

- 查看结果

```sh
npm config get registry
```

输出结果：

```txt
https://registry.npm.taobao.org/
```

