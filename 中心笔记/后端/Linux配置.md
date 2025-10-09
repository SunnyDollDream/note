# Docker
## MySQL
```bash
#
docker pull mysql
# 创建挂载目录
mkdir -p /root/mysql/{data,conf,logs}
# 设置目录权限（避免权限问题）
chmod -R 755 /root/mysql
#
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -v /root/mysql/data:/var/lib/mysql \
  -v /root/mysql/conf:/etc/mysql/conf.d \
  -v /root/mysql/logs:/var/log/mysql \
  -e MYSQL_ROOT_PASSWORD=123456vbnm \
  --restart unless-stopped \
  mysql
```
## Redis
```bash
docker pull redis
# 创建配置文件和数据的目录
mkdir -p /root/redis/conf
mkdir -p /root/redis/data

# 进入配置目录
cd /root/redis/conf
# 下载官方配置文件模板（建议版本与镜像一致）
wget http://download.redis.io/redis-stable/redis.conf
# 赋予配置文件的读写权限
chmod 666 redis.conf
```
还需要修改几个配置项(vim redis.conf)
```conf
# bind 127.0.0.1  # 注释掉这行，允许远程连接
protected-mode no  # 改为no，关闭保护模式（若需外网访问）
daemonize no       # 确保为no，因为Docker本身在后台运行
requirepass your_strong_password  # 设置一个强密码
appendonly yes     # 改为yes，开启数据持久化
```
启动
```bash
docker run -d \
  --name redis \
  -p 6379:6379 \
  -v /root/redis/conf/redis.conf:/etc/redis/redis.conf \
  -v /root/redis/data:/data \
  --restart unless-stopped \
  redis \
  redis-server /etc/redis/redis.conf
```
# 语言
## GO
更新apt安装包
```bash
apt update
```
检查apt自带的Go版本是多少
```bash
apt list golang
```
安装
```bash
apt install golang
```
>这样下的不是最新的

检查
```bash
go version
```
