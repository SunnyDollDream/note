[Go-redis](https://redis.uptrace.dev/zh/) 是 Golang 中用于与 Redis 交互的强大工具, 支持 Redis Server 的 Golang 客户端。

## 使用

#### 安装:

`go get -u` `github.com/go-redis/redis`

#### 配置文件

创建文件夹config,下边放这些配置.go,内有

```Go
package config

import (
 "github.com/go-redis/redis"
)

var RedisClient *redis.Client

func InitRedis() {
 RedisClient = redis.NewClient(&redis.Options{ //配置
 Addr: "localhost:6379",
 DB:   0,  // 默认数据库(use default DB)
 Password: "",
 }) //连接

 _, err := RedisClient.Ping().Result() //测试连接
 if err != nil {
 panic("Failed to connect to Redis")
 }
}
```

也可以写在yaml中

然后在global包里的global.go中声明一个全局变量,并在配置中为全局变量赋值,这样就可以到处使用了

#### 常用操作

1. `SET` 命令用于设置给定 key 的值(过期时间可以使用time包下的时间单位)。
    
2. `INCR` 将 key 中储存的数字值增一。
    

若 key 不存在,那么 key 的值会先被初始化为 0,然后再执行操作。(`0` -> `1`)

3. `DECR` 则将 key 中储存的数字减一。
    
4. `GET` 命令用于获取指定 key 的值。
    

get不存在的key时会返回redis.Nil错误

Go-redis活用:

```Go
// Get
val, err := rdb.Get("key").Result()
fmt.Println(val)
// or
get := rdb.Get(ctx, "key")
fmt.Println(get.Val(), get.Err())
// 自增方法
RedisDB.Incr(likeKey).Err()
// 执行原生redis语句
val, err := rdb.Do(ctx, "get", "key").Text()
fmt.Println(val, err)
// Do() 方法返回Cmd类型,其中有各种转化为其他类型的方法
```