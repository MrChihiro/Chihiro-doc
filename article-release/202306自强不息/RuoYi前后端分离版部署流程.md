# RuoYi 前后端分离版部署流程

## 📔 千寻简笔记介绍

千寻简文库已开源，Gitee与GitHub搜索`chihiro-doc`，包含笔记源文件`.md`，以及PDF版本方便阅读，文库采用精美主题，阅读体验更佳，如果文章对你有帮助请帮我点一个`Star`～

更新：`支持在线阅读文章，根据发布日期分类。`

@[toc]

特邀嘉宾：`仲琪`

## 一 前端准备

### 1.修改`router`

```js
export default new Router({
  base: '/xxxx-admin', // 去掉url中的#
  mode: 'history',
  scrollBehavior: () => ({ y: 0 }),
  routes: constantRoutes
})
```

### 2. 修改`vue.config.js`

```js
publicPath: process.env.NODE_ENV === "production" ? "/xxxx-admin/" : "/xxxx-admin/",
```

### 3.修改后端访问地址：`.env.production`

```js
# 管理系统/生产环境
VUE_APP_BASE_API = 'http://ip:端口/别名-api/'
```

### 4.修改配置：

`request.js`

```js
# 搜索：store.dispatch('LogOut')
        store.dispatch('LogOut').then(() => {
        // 修改xxxx
          location.href = '/xxxx-admin/index';
        })
```

`Navbar.vue`

```js
# 搜索：this.$store.dispatch('LogOut')
        this.$store.dispatch('LogOut').then(() => {
        // 修改xxxx
          location.href = '/xxxx-admin/index';
        })
```

### 5.构建项目

```yml
npm run build:prod
```

## 二 Nginx配置

查看nginx 位置

```sh
whereis nginx
# nginx: /usr/local/nginx

cd /usr/local/nginx/conf

# 引入配置文件
include ./conf.d/xsili.net.www.conf;
```



修改配置文件`nginx.conf`

```yml
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  doc.buble.cn;
		charset utf-8;

	location /xxx-admin {
     		# 修改路径
            alias   /home/deploy/test/java-xxx/fe/admin/dist;
            # 修前端访问路径
            try_files $uri $uri/ /xxx-admin/index.html;
            index  index.html index.htm;
        }
     location /xxx-h5 {
     		# 修改路径
            alias   /home/deploy/test/java-xxx/fe/h5/dist;
            # 修前端访问路径
            try_files $uri $uri/ /xxx-h5/index.html;
            index  index.html index.htm;
        }
        location /ywzx-move-h5 {
            alias   /home/deploy/test/java-ywzx/fe/move-h5/dist;
            try_files $uri $uri/ /ywzx-move-h5/index.html;
            index  index.html index.htm;
        }
		location /xxx-api/ {
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header REMOTE-HOST $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            # 后端访问地址，加前缀
            proxy_pass http://localhost:端口号/xxx-api/;
     	 }
      # 文件上传路径
      location /test/xxx/profile/{
      		 # 服务器文件上传路径
              alias /home/deploy/test/java-xxx/be/uploadPath;
      }
    }
}
```

刷新nginx 配置文件

```
cd /usr/local/nginx/sbin
# 刷新配置文件
./nginx -s reload
```



## 三 后端准备

### 1.修改`application-prod.yml`

```yml
# 改动1
ruoyi:
    # 服务器文件上传路径
    profile: /home/deploy/test/java-xxx/be/uploadPath
    
# 改动2
# 开发环境配置
server:
    # 服务器的HTTP端口，默认为8080
    port: 端口号
    servlet:
        # 应用的访问路径
        context-path: /xxx-api/
# 改动3 修改数据源

# 改动4 修改redis
```

### 2.修改日志存放路径：`logback.xml`

```yml
    <!-- 日志存放路径 -->
	<property name="log.path" value="./logs" />
```

### 3.Maven 打包

```shell
# 清理环境，清除target文件夹
maven clean

# 打包
maven packag

# 打包到的地址
项目路径下的 xx-admin/target/xxx-admin.jar
```

## 四 上传部署服务器

- 前端上传路径：/home/deploy/test/java-xxx/fe/admin/dist
- 后端上传Jar包路径：/home/deploy/test/java-xxx/be/

### 1. 后端启动

```bash
# 去到指定目录
cd /home/deploy/test/java-xxx/be/
# 创建文件夹
mkdir uploadPath
# 新建脚本：ry.sh
touch ry.sh
```

### 2.复制下面内容到ry.sh

```bash
#!/bin/sh
# ./ry.sh start 启动 stop 停止 restart 重启 status 状态
# xxx-admin.jar 上传的jar包名称
AppName=xxx-admin.jar

# JVM参数
# -Dspring.profiles.active=prod 指定启动环境
# -Druoyi.profile=/home/deploy/test/java-xxx/be/uploadPath 指定文件上传路径
JVM_OPTS="-Dname=$AppName  -Dspring.profiles.active=prod -Druoyi.profile=/home/deploy/test/java-xxx/be/uploadPath  -Duser.timezone=Asia/Shanghai -Xms512m -Xmx1024m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=512m -XX:+HeapDumpOnOutOfMemoryError -XX:+PrintGCDateStamps  -XX:+PrintGCDetails -XX:NewRatio=1 -XX:SurvivorRatio=30 -XX:+UseParallelGC -XX:+UseParallelOldGC"
APP_HOME=`pwd`
LOG_PATH=$APP_HOME/logs/$AppName.log

if [ "$1" = "" ];
then
    echo -e "\033[0;31m 未输入操作名 \033[0m  \033[0;34m {start|stop|restart|status} \033[0m"
    exit 1
fi

if [ "$AppName" = "" ];
then
    echo -e "\033[0;31m 未输入应用名 \033[0m"
    exit 1
fi

function start()
{
    PID=`ps -ef |grep java|grep $AppName|grep -v grep|awk '{print $2}'`

	if [ x"$PID" != x"" ]; then
	    echo "$AppName is running..."
	else
		nohup java $JVM_OPTS -jar $AppName > /dev/null 2>&1 &
		echo "Start $AppName success..."
	fi
}

function stop()
{
    echo "Stop $AppName"

	PID=""
	query(){
		PID=`ps -ef |grep java|grep $AppName|grep -v grep|awk '{print $2}'`
	}

	query
	if [ x"$PID" != x"" ]; then
		kill -TERM $PID
		echo "$AppName (pid:$PID) exiting..."
		while [ x"$PID" != x"" ]
		do
			sleep 1
			query
		done
		echo "$AppName exited."
	else
		echo "$AppName already stopped."
	fi
}

function restart()
{
    stop
    sleep 2
    start
}

function status()
{
    PID=`ps -ef |grep java|grep $AppName|grep -v grep|wc -l`
    if [ $PID != 0 ];then
        echo "$AppName is running..."
    else
        echo "$AppName is not running..."
    fi
}

case $1 in
    start)
    start;;
    stop)
    stop;;
    restart)
    restart;;
    status)
    status;;
    *)

esac

```

### 3.部署

```bash
# 给脚本添加执行的权限
chmode +x ry.sh
# 启动jar
./ry.sh start
# 重启
./ry.sh restart
# 停止
./ry.sh stop
```



