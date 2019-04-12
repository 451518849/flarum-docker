## flarum是一个很好的开源论坛，很有前途。

[这里是flarum的docker仓库](https://github.com/mondediefr/docker-flarum)

### 第一步：下载docker镜像

`docker pull mondedie/docker-flarum:0.1.0-beta.8.1-stable
`

### 第二步编写docker-compose.yml：下面给出一个可运行的flarum的docker-compose.yml

	version: "3"
	
	services:
	  flarum:
	    image: mondedie/docker-flarum:0.1.0-beta.8.1-stable
	    container_name: flarum
	    ports:
	      - "80:8888"
	    env_file:
	      - /root/flarum/flarum.env
	    volumes:
	      - /root/flarum/mnt/assets:/flarum/app/public/assets
	      - /root/flarum/mnt/extensions:/flarum/app/extensions
	      - /root/flarum/mnt/nginx:/etc/nginx/conf.d
	    depends_on:
	      - mariadb
	
	  mariadb:
	    image: mariadb:10.1
	    container_name: mariadb
	    volumes:
	      - /root/flarum/mnt/mysql:/var/lib/mysql
	    environment:
	      - MYSQL_ROOT_PASSWORD=xxxxx
	      - MYSQL_DATABASE=flarum
	      - MYSQL_USER=root //这里最好是root，如果不是root可能会有问题


### 第三步编写flarum.env，它是配置脚背，内容如下：

	DEBUG=true
	FORUM_URL=http://xxxxx.com //你的论坛域名
	
	# Database configuration
	DB_HOST=mariadb
	DB_NAME=flarum
	DB_USER=root
	DB_PASS=xxx
	DB_PREF=flarum_
	DB_PORT=3306
	
	# User admin flarum (environment variable for first installation)
	# /!\ admin password must contain at least 8 characters /!\
	FLARUM_ADMIN_USER=xxxxx //管理员账号
	FLARUM_ADMIN_PASS=xxxx  //管理员密码
	FLARUM_ADMIN_MAIL=xxxxxx //你的邮箱
	FLARUM_TITLE=xxxx 论坛名称
	
	
	
### 第四步：运行flarum docker-compose up -d

### 遇到的问题
1. 由于flarum用PHP写的，docker镜像中使用了composer作为配置工具。如果你在china，很多第三方库都安装不了，你需要进入容器中然后进入到flarum文件夹下手动更换PHP的镜像源。尽量修改当前项目的，composer.json 配置文件。如果更换全局的配置文件可能会有问题。镜像地址：[PHP中国全量镜像](https://pkg.phpcomposer.com/)或者flarum运行起来后执行一下命令
`docker exec -ti flarum extension config repo.packagist composer https://packagist.phpcomposer.com`

2. 由于数据库的启动延迟问题运行起来后需要等一会才能访问，第一次运行的时间比较久，大概1-2分钟后才能访问。如果不能访问，可以尝试重新运行几次。

### flarum安装插件的方法：

`docker exec -ti flarum extension require some/extension
`

### flarum移除插件的方法：

`docker exec -ti flarum extension remove some/extension`
