# Docker Compose 创建本地环境
在MacOS 与 Windows下通过Docker Compose 配置文件，搭建本地开发环境
DNMP（Docker + Nginx + MySQL5,8 + PHP7,8 + Redis + ElasticSearch + MongoDB + RabbitMQ + KAFKA）是一款全功能的**LNMP一键安装程序**。

<details>
<summary>项目地址</summary>

- [GitHub 地址](https://github.com/jiangrx816/local)

</details>

<details>
<summary>MDNMP项目特点</summary>

1. `100%`开源 且 遵循Docker标准
2. 支持**多版本PHP**共存，可任意切换（PHP7、PHP8)
3. 支持绑定**任意多个虚拟域名**，且支持**HTTPS和HTTP/2**
4. **PHP源代码、MySQL数据、配置文件、日志文件**都可在宿主机中直接修改查看
5. 可一键选配常用服务：
    - 多PHP版本：PHP7、PHP8，如需其他的版本，可以自行按照`docker-compose`进行配置
    - Web服务：Nginx
    - 数据库：MySQL5、MySQL8、Redis、MongoDB、ElasticSearch、KAFKA
    - 消息队列：RabbitMQ
    - 辅助工具：Kibana、Logstash
6. 所有镜像源于[Docker官方仓库](https://hub.docker.com)，安全可靠
7. 一次配置，**Windows、Linux、MacOs**皆可使用

</details>





## 1.目录结构

```
/
├── data                        数据库数据目录
│   ├── elasticsearch           ElasticSearch 数据目录
│   ├── mysql                   MySQL5 数据目录
│   ├── mysql8                  MySQL8 数据目录
│   ├── redis                   Redis 数据目录
│   └── mongo                   MongoDB 数据目录
├── services                    服务构建文件和配置文件目录
│   ├── elasticsearch           ElasticSearch 配置文件目录
│   ├── mysql                   MySQL5 配置文件目录
│   ├── mysql8                  MySQL8 配置文件目录
│   ├── nginx                   Nginx 配置文件目录
│   ├── php                     PHP7配置目录
│   └── redis                   Redis 配置目录
├── logs                        日志目录
├── docker-compose.yml   Docker 服务配置示例文件
└── www                         PHP项目代码目录
│   ├── site1                   站点1文件目录
│   ├── site2                   站点2文件目录
```

## 2.快速使用
1. 本地安装
   - `git`
   - `Docker`(系统需为Linux，Windows 10 Build 15063+，或MacOS 10.12+，且必须要`64`位）
   - `docker-compose 1.7.0+`
2. `clone`项目：
    ```
    $ git clone https://github.com/jiangrx816/local.git
    ```
3. 创建对应的local环境：
   ```
   $ docker-compose -f docker-compose.yml up -d
    ```

## 3.常用命令（php-cli）
    ```bash
    ~ php -v
    PHP 7.2.13 (cli) (built: Dec 21 2018 02:22:47) ( NTS )
    Copyright (c) 1997-2018 The PHP Group
    Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
        with Zend OPcache v7.2.13, Copyright (c) 1999-2018, by Zend Technologies
        with Xdebug v2.6.1, Copyright (c) 2002-2018, by Derick Rethans
    ```
## 4.使用composer
**方法1：主机中使用composer命令**
1. 确定composer缓存的路径。比如，我的local下载在`~/local`目录，那composer的缓存路径就是`~/local/data/composer`。
2. 将对应 php composer 函数拷贝到主机的 `~/.bashrc`文件。
   > 这里需要注意的是，示例文件中的`~/local/data/composer`目录需是第一步确定的目录。
3. 让文件起效：
    ```bash
    source ~/.bashrc
    ```
4. 在主机的任何目录下就能用composer了：
    ```bash
    cd ~/local/www/
    composer create-project new project --no-dev
    ```
5. （可选）第一次使用 composer 会在 `~/local/data/composer` 目录下生成一个**config.json**文件，可以在这个文件中指定国内仓库，例如：
    ```json
    {
        "config": {},
        "repositories": {
            "packagist": {
                "type": "composer",
                "url": "https://mirrors.aliyun.com/composer/"
            }
        }
    }

    ```
**方法二：容器内使用composer命令**

还有另外一种方式，就是进入容器，再执行`composer`命令，以PHP7容器为例：
```bash
docker exec -it php /bin/sh
cd /www/localhost
composer update
```

## 4.管理命令
### 4.1 服务器启动和构建命令
如需管理服务，请在命令后面加上服务器名称，例如：
```bash
$ docker-compose up                         # 创建并且启动所有容器
$ docker-compose up -d                      # 创建并且后台运行方式启动所有容器
$ docker-compose up -d local-nginx          # 创建并且已后台运行的方式启动nginx、php、mysql容器


$ docker-compose start local-php                  # 启动服务
$ docker-compose stop local-php                   # 停止服务
$ docker-compose restart local-php                # 重启服务
$ docker-compose build local-php                  # 构建或者重新构建服务

$ docker-compose rm local-php                     # 删除并且停止php容器
$ docker-compose down                             # 停止并删除容器，网络，图像和挂载卷
$ docker exec -it local-nginx nginx -s reload                 #重启 Nginx, local-nginx 是容器名称
$ docker exec -it local-php /bin/sh   #进入容器
$ docker exec -it local-mysql /bin/bash   #在宿主机执行mysql
$ docker exec -it local-redis /bin/sh #在宿主机执行redis
$ docker exec -it local-php /bin/sh  #在宿主机执行php

# docker inspect 容器名称
$ docker inspect local-mysql
$ docker inspect local-elasticsearch


```

### 4.2 添加快捷命令
在开发的时候，我们可能经常使用`docker exec -it`进入到容器中，把常用的做成命令别名是个省事的方法。