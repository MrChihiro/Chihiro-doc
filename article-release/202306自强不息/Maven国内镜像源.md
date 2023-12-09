# Maven配置国内镜像源

## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]

## 简介

Maven 是一个流行的 Java 项目构建工具，它依赖于互联网上的 Maven 中央仓库来下载和管理项目依赖库。但是，由于网络原因或其他问题，有时会导致从中央仓库下载依赖库的速度变慢或者无法下载，这就会影响项目的构建效率。

通过为 Maven 配置镜像源，可以使 Maven 从镜像源下载依赖库，而不是直接从中央仓库下载。这样做的好处在于：

1. 提高下载速度：由于国内的镜像源一般都部署在国内的服务器上，所以从镜像源下载依赖库的速度要快得多，可以大大提高项目构建的效率。
2. 避免访问限制：如果您在公司或学校等组织中使用 Maven 进行项目构建，可能会受到防火墙或代理服务器的限制。在这种情况下，配置了镜像源可以避免这些限制，从而顺利地下载和管理依赖库。
3. 减少对中央仓库的负担：由于 Maven 的用户数量庞大，中央仓库的访问量也非常大。如果所有用户都直接从中央仓库下载依赖库，就会给中央仓库带来很大的负担和压力。配置了镜像源后，可以将下载负载分摊到多个镜像源上，减轻中央仓库的压力。

## 实现代码

在maven的安装目录中找到`settings.xml`，默认目录是在`C:\Users\Star(用户名)\.m2\settings.xml`

```xml
<!-- 加在<mirrors>中添加-->
	<mirrors>
        <mirror>
            <id>mvnrepository</id>
            <mirrorOf>mvnrepository</mirrorOf>
            <url>http://mvnrepository.com/</url>
        </mirror>

        <!--自定义添加-->
        <mirror>
            <id>repo2</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://repo2.maven.org/maven2/</url>
        </mirror>

        <!--默认的中央仓库-->
        <mirror>
            <id>mirrorId</id>
            <mirrorOf>repositoryId</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://my.repository.com/repo/path</url>
        </mirror>
        
        <mirror>
            <id>96nexus</id>
            <mirrorOf>*</mirrorOf>
            <name>96nexus</name>
            <url>http://172.18.64.96:8080/nexus/content/groups/public/</url>
        </mirror>

        <mirror>
            <id>nexus-aliyun</id>
            <mirrorOf>central</mirrorOf>
            <name>nexus-aliyun</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
        </mirror>
        
        <!--    阿里仓库-->
        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>

        <!--    maven仓库官方-->
        <mirror>
            <id>nexus</id>
            <name>internal nexus repository</name>
            <!-- <url>http://192.168.1.100:8081/nexus/content/groups/public/</url>-->
            <url>http://repo.maven.apache.org/maven2</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>
```

