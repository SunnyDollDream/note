go-zero 是一个集成了各种工程实践的 web 和 rpc 框架.

## 环境安装

安装goctl(go-zero的命令行工具)

```Shell
go install github.com/zeromicro/go-zero/tools/goctl@latest
```

安装grpc工具

```Shell
goctl env check --install --verbose --force
```

## 微服务架构

以下命令应该在对应的微服务目录下执行

安装go-zero依赖

```Shell
go get -u github.com/zeromicro/go-zero@latest
```

快速创建一个api服务

```Shell
goctl api new api
```

其中的.api文件声明了服务中接口的详细信息,logic文件下的go文件用于书写详细逻辑

快速创建rpc服务

```Shell
goctl rpc new rpc
```

## Etcd

Etcd是一个高可用的分布式键值存储系统，主要用于共享配置信息和服务发现。它采用Raft一致性算法来保证数据的强一致性，并且支持对数据进行监视和更新

#### 安装

linux安装

源码安装

https://blog.csdn.net/Mr_XiMu/article/details/127923827

yum安装

版本一般比较老

docker安装

```Go
docker run --name etcd -d -p 2379:2379 -p 2380:2380 -e ALLOW_NONE_AUTHENTICATION=yes bitnami/etcd:3.3.11 etcd
```

基本命令

```Go
// 设置或更新值
etcdctl put name 张三
// 获取值
etcdctl get name
// 只要value
etcdctl get name --print-value-only
// 获取name前缀的键值对
etcdctl get --prefix name
// 删除键值对
etcdctl del name
// 监听键的变化
etcdctl watch name
```

## 使用

#### 服务端

1. 新建proto文件
    

```ProtoBuf
syntax = "proto3";

package user;

option go_package = "./user";

message IdRequest {
  string id = 1;
}

message UserResponse {
  // 用户id
  string id = 1;
  // 用户名称
  string name = 2;
  // 用户性别
  bool gender = 3;
}

service User {
  rpc getUser(IdRequest) returns(UserResponse);
}
```

执行这条命令生成rpc相关文件

`goctl rpc protoc user/rpc/user.proto --go_out=user/rpc/types --go-grpc_out=user/rpc/types --zrpc_out=user/rpc/`

2. 修改yaml文件,ListenOn为服务的端口(改为127.0.0.1,不要用0.0.0.0),Hosts:为etcd的服务端ip和端口
    
3. 完善生成的rpc文件的业务逻辑
    

#### 服务调用端

1. 完善api文件
    

```ProtoBuf
type (
  VideoReq {
    Id string `path:"id"`
  }

  VideoRes {
    Id   string `json:"id"`
    Name string `json:"name"`
  }
)
service video {
  @handler getVideo
  get /api/videos/:id (VideoReq) returns (VideoRes)
}
```

2. 执行这条指令生成api代码
    

`goctl api go -api video/api/video.api -dir video/api/`

3. 配置rpc服务提供端的地址,在config下的config文件新增zrpc.RpcClient属性声明对应的rpc服务,
    

```Go
type Config struct {
    rest.RestConf
    UserRpc zrpc.RpcClientConf //新增,这个config和下边业务依赖中的config是同一个
}
```

4. 完善业务依赖,即svc里的servicecontext文件
    

```Go
package svc

import (
  "github.com/zeromicro/go-zero/zrpc"
  "go_test/user/rpc/userclient"
  "go_test/video/api/internal/config"
)

type ServiceContext struct {
  Config  config.Config
  UserRpc userclient.User //rpc服务的主体结构体
}

func NewServiceContext(c config.Config) *ServiceContext {
  return &ServiceContext{
    Config:  c,
    UserRpc: userclient.NewUser(zrpc.MustNewClient(c.UserRpc)), 
  }
}
```

5. 完善yaml文件
    

```Go
Name: video
Host: 127.0.0.1
Port: 8083
UserRpc:
  Etcd:
    Hosts:
      - 101.126.153.13:2379 //etcd地址
    Key: user.rpc //对应业务的key
```

4. 运行(需要保证rpc业务在运行),业务对应的路径在router.go中
    

## api

api文件就是对这个服务所有api的描述,包括服务名，函数名，路径，请求方法，请求参数，响应参数

```Go
syntax = "v1"

info(
    title: "type title here"
    desc: "type desc here"
    author: "type author here"
    email: "type email here"
    version: "type version here"
)

type LoginRequest{ //首字母大写
    Username string `json:"username"`
    Password string `json:"password"`
}
type LoginResponse{
    Code int `json:"code"`
    Data string `json:"data"`
    Msg string `json:"msg"`
}
type UserInfo{
    UserId uint `json:"user_id"`
    Username string `json:"username"`
}
type UserInfoResponse {
    Code int `json:"code"`
    Data UserInfo `json:"data"`
    Msg string `json:"msg"`
}

service users{
    @handler login //@handler对应的是生成出来的函数名
    post /api/user/login (LoginRequest) returns (LoginResponse) //post对应请求该方法的路由, 其后第一个括号为入参(没有可以不写),第二个为返回值
    @handler userInfo
    post /api/user/userInfo returns (UserInfoResponse)
}
```

之后执行命令即可生成对应的文件

#### 响应封装

上边的写法中为每一个响应都声明了一个单独的结构体,但是这种方法十分繁琐,我们可以在项目根目录下声明一个common/response/enter.go定义一个用于封装的方法来统一封装响应

```Go
package response

import (
    "github.com/zeromicro/go-zero/rest/httpx"
    "net/http"
)

type Body struct {
    Code int    `json:"code"`
    Data any    `json:"data"`
    Msg  string `json:"msg"`
}

func Response(r *http.Request, w http.ResponseWriter, res any, err error) { //前两个参数对应的是默认的返回逻辑中的参数,后边为实际响应内容
    if err != nil {
       body := Body{
          Code: 10086,
          Data: nil,
          Msg:  "error",
       }
       httpx.WriteJson(w, http.StatusInternalServerError, body)
    }
    body := Body{
       Code: 0,
       Data: res,
       Msg:  "success",
    }
    httpx.WriteJson(w, http.StatusOK, body)
}
```

之后去handler中把默认的一段响应逻辑替换为自己写的方法即可实现统一封装

**模板定制化:**

当然官方提供了修改模板的方式，避免每次生成都要去改

https://go-zero.dev/docs/tutorials/customization/template

先全局搜一下 `handler.tpl`这个文件

如果没有就先用这个命令生成

```Go
goctl template init
```

修改为：

```Go
package handler

import ("net/http""github.com/zeromicro/go-zero/rest/httpx""go_test/common/response"
    {{.ImportPackages}}
)

func {{.HandlerName}}(svcCtx *svc.ServiceContext) http.HandlerFunc {return func(w http.ResponseWriter, r *http.Request) {
        {{if .HasRequest}}var req types.{{.RequestType}}if err := httpx.Parse(r, &req); err != nil {
            httpx.Error(w, err)return
        }{{end}}
        l := logic.New{{.LogicType}}(r.Context(), svcCtx)
        {{if .HasResp}}resp, {{end}}err := l.{{.Call}}({{if .HasRequest}}&req{{end}})
        {{if .HasResp}}response.Response(r, w, resp, err){{else}}response.Response(r, w, nil, err){{end}}
    }
}
```

#### 路由前缀

可以在api文件中使用@server来声明公共前缀,这样之后的路由就不用写了

```Go
@server (
    prefix: /api/users //这里prefix后边不能有空格
)
service users {
    @handler login //@handler对应的是生成出来的函数名
    post /login (LoginRequest) returns (string) //post对应请求该方法的路由, 其后第一个括号为入参(没有可以不写),第二个为返回值

    @handler userInfo
    post /userInfo returns (UserInfoResponse)
}
```

#### JWT

同样是在@server中,可以加上jwt属性,其值为Auth,并且这个注解的生效范围是自声明处直到下一个@server,所以如果有不想鉴权的接口可以把service拆成两份分别打注解

配置jwt还需要在yaml中配置

```YAML
Auth:
  AccessSecret: SIPC115
  AccessExpire: 3600 //单位为秒
```

之后还需要在common中新建一个用来签发Token的工具类

```Go
package jwts

import (
  "errors"
  "github.com/golang-jwt/jwt/v4"
  "time"
)

// JwtPayLoad jwt中payload数据
type JwtPayLoad struct {
  UserID   uint   `json:"user_id"`
  Username string `json:"username"` // 用户名
  Role     int    `json:"role"`     // 权限  1 普通用户  2 管理员
}

type CustomClaims struct {
  JwtPayLoad
  jwt.RegisteredClaims
}

// GenToken 创建 Token
func GenToken(user JwtPayLoad, accessSecret string, expires int64) (string, error) {
  claim := CustomClaims{
    JwtPayLoad: user,
    RegisteredClaims: jwt.RegisteredClaims{
      ExpiresAt: jwt.NewNumericDate(time.Now().Add(time.Hour * time.Duration(expires))),
    },
  }

  token := jwt.NewWithClaims(jwt.SigningMethodHS256, claim)
  return token.SignedString([]byte(accessSecret))
}

// ParseToken 解析 token
func ParseToken(tokenStr string, accessSecret string, expires int64) (*CustomClaims, error) {

  token, err := jwt.ParseWithClaims(tokenStr, &CustomClaims{}, func(token *jwt.Token) (interface{}, error) {
    return []byte(accessSecret), nil
  })
  if err != nil {
    return nil, err
  }
  if claims, ok := token.Claims.(*CustomClaims); ok && token.Valid {
    return claims, nil
  }
  return nil, errors.New("invalid token")
}
```

之后再在需要签发token的地方去生成就可以了

```Go
func (l *LoginLogic) Login(req *types.LoginRequest) (resp string, err error) {
    // todo: add your logic here and delete this line
    auth := l.svcCtx.Config.Auth //读取配置文件
    token, err := jwt.GenToken(jwt.JwtPayLoad{
       UserID:   1,
       Username: req.Username,
       Role:     1,
    }, auth.AccessSecret, auth.AccessExpire)
    if err != nil {
       return "error", err
    }
    return token, nil
}

func (l *UserInfoLogic) UserInfo() (resp *types.UserInfoResponse, err error) {
    // todo: add your logic here and delete this line
    userId := l.ctx.Value("user_id") //这个方法可以从上下文中读取到Token中的信息,类似threadlocal
    println(userId)
    username := l.ctx.Value("username").(string)

    return &types.UserInfoResponse{
       UserId:   1,
       Username: username,
    }, nil
}
```

P.S.这里的Token不在请求头里,而是Auth的Beared Token里

**自定义鉴权失败响应：**

默认的鉴权失败是只会返回一个401的响应码,我们可以修改校验失败以后的响应内容,只需要在对应的主类中添加一个回调函数即可

```Go
func main() {
  flag.Parse()

  var c config.Config
  conf.MustLoad(*configFile, &c)

  server := rest.MustNewServer(c.RestConf, rest.WithUnauthorizedCallback(JwtUnauthorizedResult)) //这里需要让回调函数生效
  defer server.Stop()

  ctx := svc.NewServiceContext(c)
  handler.RegisterHandlers(server, ctx)

  fmt.Printf("Starting server at %s:%d...\n", c.Host, c.Port)
  server.Start()
}

// JwtUnauthorizedResult jwt验证失败的回调
func JwtUnauthorizedResult(w http.ResponseWriter, r *http.Request, err error) {
  fmt.Println(err) // 具体的错误，没带token，token过期？伪造token？
  httpx.WriteJson(w, http.StatusOK, response.Body{10087, nil,"鉴权失败" })
}
```

#### swagger

1. 安装
    

`go install` `github.com/zeromicro/goctl-swagger@latest`

2. 生成app.json
    

如果没有doc目录，需要创建

```Go
goctl api plugin -plugin goctl-swagger="swagger -filename app.json(生成的文件的名字) -host localhost:8084 -basepath /" -api user.api(微服务的api文档的名字) -dir ./doc(生成到的目录)
```

3. 对应的api文件中也可以添加注解来生成更详细的文档
    

```Go
@server(
  prefix: /api/users
)
service users {
  @doc(
    summary: "用户登录" //中文名
  )
  @handler login
  post /login (LoginRequest) returns (string)
}

@server(
  jwt: Auth
  prefix: /api/users
)
service users {
  @doc(
    summary: "获取用户信息"
  )
  @handler userInfo
  get /info returns (UserInfoResponse)
}
```

改完需要重新生成api文档

## Mysql

#### 原生操作Mysql

1. 把对应的数据库的建表语句放在项目中(.sql)
    
2. 生成代码,生成的go代码，自动为我们生成了增删改查的代码,如果有额外需求可以自己增加原生sql来执行
    

`goctl model mysql ddl --src user.sql --dir .`

> 自己写的方法需要在Model的接口中加上对应的方法

3. 代码使用
    

在config里面写上mysql配置

```Go
package config

import "github.com/zeromicro/go-zero/rest"type Config struct {
  rest.RestConf
  Mysql struct {
    DataSource string
  }
  Auth struct {
    AccessSecret string
    AccessExpire int64
  }
}
```

4. 在yaml中写上数据库的信息
    

```YAML
Name: users
Host: 127.0.0.1
Port: 8084
Mysql:
  DataSource: root:123456vbnm@tcp(127.0.0.1:3306)/go_zero_db?charset=utf8mb4&parseTime=True&loc=Local
Auth:
  AccessSecret: SIPC115
  AccessExpire: 3600
```

5. 在servicecontext中建立连接
    

```Go
package svc

import (
  "github.com/zeromicro/go-zero/core/stores/sqlx"
  "go_test/v1/api/internal/config"
  "go_test/v1/model"
)

type ServiceContext struct {
  Config     config.Config
  UsersModel model.UserModel //声明变量
}

func NewServiceContext(c config.Config) *ServiceContext {
  mysqlConn := sqlx.NewMysql(c.Mysql.DataSource) //连接
  return &ServiceContext{
    Config:     c,
    UsersModel: model.NewUserModel(mysqlConn), //建立对应操作对象
  }
}
```

之后就可以通过上下文调用这个Model操作数据库了

#### Gorm

使用GORM最好自己写model,因为自动生成的标签和gorm的标签不一样

1. 新建model
    

```Go
type UserModel struct {
    gorm.Model
    UserName string `gorm:"size:32" json:"userName"`
    Password string `gorm:"size:64" json:"password"`
}
```

2. 连接数据库(连接的方法可以写在common里)
    

```Go
package init_db

import (
  "fmt"
  "gorm.io/driver/mysql"
  "gorm.io/gorm"
)

// InitGorm gorm初始化
func InitGorm(MysqlDataSource string) *gorm.DB {
  db, err := gorm.Open(mysql.Open(MysqlDataSource), &gorm.Config{})
  if err != nil {
    panic("连接mysql数据库失败, error=" + err.Error())
  } else {
    fmt.Println("连接mysql数据库成功")
  }
  return db
}
```

3. 在配置类中添加Mysql结构体用于存储数据库地址
    
4. 把gorm的数据库连接注册到go-zero的上下文中
    
5. 在主程序中正常使用即可
    

## Rpc

#### 使用

1. 编写proto文件
    
2. `goctl rpc protoc user.proto --go_out=./types --go-grpc_out=./types --zrpc_out=.`生成rpc文件(go-zero结合grpc会自动生成server控制类中的调用logic文件的代码,所以可以直接写logic)
    
3. 在需要调用的客户端的yaml中注册该rpc服务
    

```Go
Name: users
Host: 0.0.0.0
Port: 8888
UserRpc:
  Etcd:
    Hosts:
      - 127.0.0.1:2379
    Key: user.rpc
```

4. 在上下文和配置类中依赖注入rpc服务,在logic中从上下文调用即可
    

> 如果使用路径参数,需要在请求对应的方法的请求结构体中新加一个字段用于接受这个字段值,使用`path:"<在路径中的名字>"`标签,再将这个请求体传入方法中来调用其中的路径字段值,直接从上下文.value是获取不到的