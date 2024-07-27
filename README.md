# Docker Compose 创建本地环境
通过Docker Compose 配置文件，搭建本地开发环境

## 运行

```shell

docker-compose -f docker-compose-local.yml up -d

 ```

## 查询ip
```shell
# docker inspect 容器名称
docker inspect mysql
docker inspect elasticsearch

 ```