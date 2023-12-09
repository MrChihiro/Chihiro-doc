# 【Windows】git多帐号配置

## 📔 千寻简笔记介绍

千寻简笔记已开源，Gitee与GitHub搜索`chihiro-notes`，包含笔记源文件`.md`，以及PDF版本方便阅读，且是用了精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

@[toc]

## 一、背景

作为一名出色的开发工程师，目前互联网代码托管平台众多同时有些平台已不支持账号和密码的直接gitbash操作。在我们托管平台多项目多，比如公司用的gitlab、而同时也参加一些开源项目在github、gitee等代码托管平台上；那么如何利用手中的一台开发机，同时支持多个代码托管平台的代码免密进行代码提交拉取等操作呢？这篇文章告诉你答案。

## 二、步骤

- 清除全局的帐号


```sh
git config --global --unset user.name
git config --global --unset user.email
```

在用户目录下的.ssh目录下生成ssh免密登录公钥和私钥

- .ssh/目录（```C:\Users\自己的用户名\.ssh```）下，右键Git Bash Here，打开git-bash窗口

```sh
ssh-keygen -t rsa -C "gitee邮箱地址" -f ~/.ssh/gitee_star_rsa
```

> -t 指定生成rsa类型的秘钥
>
> -C 指定该秘钥注释以便查阅
>
> -f 指定生成秘钥的名字，可以不指定该参数，默认就会生成2个文件：私钥id_rsa，公钥id_rsa.pub。由于需要生成两对私钥公钥，因此需要指定-f，否则生成两次后，私钥公钥会覆盖。

按三次回车后，同样在文件夹中看到了生成的Github私钥gitlab_rsa和公钥gitlab_rsa.pub

- 将公钥配置到对应的gitlab账号中

**公钥** 即.pub文件可以直接用文本打开，内容粘贴到github的Settings -> SSH and GPG keys -> New SSH Key，Title随便起，自己能认出来即可，Key里面填写复制的.pub里的内容，同样步骤操作github平台

```sh
ssh-keygen -t rsa -C "gitee邮箱地址" -f ~/.ssh/gitee_star2_rsa
```

## 三、创建config文件

- 在.ssh目录下创建config 文件，git通过这个文件才知道哪个私钥去对应哪个公钥。
- 缩进一个table，如果报错可以看下缩进。

```sh
Host star.gitee.com
    port 22
    User Star
    HostName gitee.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/gitee_star_rsa

Host tianhe.gitee.com
    port 22
    User Star
    HostName gitee.com
    PreferredAuthentications publickey
    IdentityFile  ~/.ssh/gitee_tianhe_rsa
```

> config文件部分参数含义，仅做记录
>
> Host：可以看作是一个你要识别的模式，对识别的模式，配置对应的主机名和ssh文件。（不重复即可）
>
> Port：自定义的端口。默认为22，可不配置
>
> User：自定义的用户名，默认为git，可不配置
>
> HostName：真正连接的服务器地址
>
> PreferredAuthentications：指定优先使用哪种方式验证，支持密码和秘钥验证方式
>
> IdentityFile：指定本次连接使用的密钥文件
>
> AddKeysToAgent：是否自动将 key 加入到 ssh-agent，值可以为 no(default)/confirm/ask/yes。如果是 yes，key 和密码都将读取文件并加入到 agent ，就像 ssh-add。其他人分别是询问、确认、不加入的意思。添加到 ssh-agent 意味着将私钥和密码交给它管理，让它来进行身份认证。
>
> UseKeychain：ssh密钥的密码存储在钥匙串中

## 四、测试ssh-key是否连通

```sh
ssh -T git@star.gitee.com
Hi yuncopy! You've successfully authenticated, but GitHub does not provide shell access.
ssh -T git@tianhe.gitee.com

```

成功的情况返回如上所示。