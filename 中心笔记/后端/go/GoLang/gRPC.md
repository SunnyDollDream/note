![[Pasted image 20250715130102.png]]

gRPC(RPC : Remote Procedure Call)是一种高性能、开源的远程过程调用(RPC)框架。它允许分布在不同计算机上的应用程序能够像调用本地方法一样进行通信，从而实现了在分布式系统中进行高效的通信。gRPC使用 Google 开发的 Protocol Buffers(ProtoBuf)进行数据序列化，同时基于HTTP/2 协议进行通信，从而提供了更高效的通信方式。gRPC 支持双向流式数据传输，适用于实时性要求高的场景。

> Protocol Buffss是谷歌开源的一套成熟的数据结构序列化机制。
> 
> 你可以把他当成一个代码生成工具以及序列化工具。这个工具可以把我们定义的方法，转换成特定语言的代码。比如你定义了一种类型的参数,他会帮你转换成Golang中的stuct 结构体，你定义的方法，他会帮你转换成func 函数。此外，在发送请求和接受响应的时候，这个工具还会完成对应的编码和解码工作，将你即将发送的数据编码成gRPC能够传输的形式，又或者将即将接收到的数据解码为编程语言能够理解的数据格式。
> 
> 优势
> 
> 1.序列化后体积相比Json和XMIL很小，适合网络传输
> 
> 2.支持跨平台多语言
> 
> 3.消息格式升级和兼容性还不错
> 
> 4.序列化反序列化速度很快

## 使用

1. 安装环境:
    

https://github.com/protocolbuffers/protobuf/releases 并把bin配置到path环境变量中

使用protoc命令进行测试

2. 引入依赖:
    

GO:

go get google.golang.org/grpc

JAVA:

```XML
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-all</artifactId>
    <version>1.65.1</version>
</dependency>
<dependency>
    <groupId>com.google.protobuf</groupId>
    <artifactId>protobuf-java</artifactId>
    <version>3.11.4</version>
</dependency>
<dependency>
    <groupId>com.google.protobuf</groupId>
    <artifactId>protobuf-java-util</artifactId>
    <version>4.27.2</version>
</dependency>
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>annotations-api</artifactId>
    <version>6.0.53</version>
</dependency>
<dependency>
    <groupId>io.grpc</groupId>
    <artifactId>grpc-netty-shaded</artifactId>
    <version>1.65.1</version>
</dependency>
```

3. 这个是一个编译器,还需要配合各个语言的代码生成工具
    

GO:

```Go
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

4. 编写proto文件
    

在服务端和客户端分别添加proto文件夹,在其中新建.proto文件(内容也要一样,类似两端的接口)

```ProtoBuf
//这是在说明我们使用的是proto3语法。
syntax = "proto3";

//这部分的内容是关于最后生成的go文件是处在哪个目录哪个包中,.代表在当前目录生成,service代表了生成的go文件的包名是service.
option go_package = ".;service";

//然后我们需要定义一个服务，在这个服务中需要有一个方法，这个方法可以接受客户端的参数，再返回服务端的响应。
//其实很容易可以看出，我们定义了一个service，称为SayHello，这个服务中有一个rpc方法，名为SayHello。
//这个方法会发送一个HelloRequest，然后返回一个HelloResponse。
service SayHello {
  rpc SayHello(HelloRequest) returns(HelloResponse){}
}

//message关键字，其实你可以理解为Golanq中的结构体。
//这里比较特别的是变量后面的"赋值”。注意，这里并不是赋值，而是在定义这个变量在这个message中的位置,即序列号
message HelloRequest{
  string requestName = 1;
  //int64 age = 2;
}

message HelloResponse{
  string responseMsg = 1;
}
```

5. 执行命令生成代码
    

在.proto文件的同级目录下执行cmd命令

```ProtoBuf
protoc --go_out=. hello.proto //生成go的代码
protoc --go-grpc_out=. hello.proto //生成grpc代码
```

远程调用使用grpc代码部分,其中的代码逻辑需要自己补全,或者定义一个继承来实现

6. 实现业务逻辑
    

找到生成的go文件中的对应的服务端结构体,在另一个类中继承并重写其中的自定义方法

```Go
package main

import (
    "context"
    service "gRPC_learning/hello-server/proto"
    "google.golang.org/grpc"
    "net"
)

type server struct {
    service.SayHelloServer
}

func (s *server) SayHello(ctx context.Context, req *service.HelloRequest) (*service.HelloResponse, error) {
    println(req.RequestName) // 这段代码的输出会在服务端的终端上
    return &service.HelloResponse{ResponseMsg: "hello " + req.RequestName}, nil
}

func main() {
    //开启端口
    listen, err := net.Listen("tcp", ":9090")
    if err != nil {
       return
    }
    //创建gRPC服务
    grpcServer := grpc.NewServer()
    //在grpc服务端中区注册我们自己编写的服务
    service.RegisterSayHelloServer(grpcServer, &server{})
    //启动服务
    err = grpcServer.Serve(listen)
    if err != nil {
       return
    }
}
```

服务调用端需要进行连接

```Go
package main

import (
    "context"
    service "gRPC_learning/hello-server/proto"
    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials/insecure"
)

func main() {
    //连接到服务端,第二个参数为加密方式(此处为不加密)
    conn, _ := grpc.NewClient("127.0.0.1:9090", grpc.WithTransportCredentials(insecure.NewCredentials()))
    //手动关闭连接
    defer conn.Close()
    //建立连接
    client := service.NewSayHelloClient(conn)
    //执行rpc调用,第一个参数用于找到对应的上下文
    response, err := client.SayHello(context.Background(), &service.HelloRequest{RequestName: "Sunny"})
    if err != nil {
       println("error")
       return
    }
    println(response.GetResponseMsg())
}
```

## proto文件格式

#### message

protobuf中定义一个消息类型式是通过关键字 message字段指定的。消息就是需要传输的数据格式的定义

message 关键字类似于C++中的class,JAVA中的class,go中的struct

在消息中承载的数据分别对应于每一个字段，其中每个字段都有一个名字和一种类型

一个proto文件中可以定义多个消息类型

#### 字段规则

required:消息体中必填字段，不设置会导致编码异常。在protobuf2中使用，在protobuf3中被删去

optional:消息体中可选字段。protobuf3没有了required，optional等说明关键字，都默认为optional

Repeated :消息体中可重复字段，重复的值的顺序会被保留,在go中重复的会被定义为切片

#### 消息号

在消息体的定义中，每个字段都必须要有一个唯一的标识号，标识号是[1，2^29-1]范围内的一个整数

#### 嵌套消息

可以在其他消息类型中定义、使用消息类型，在下面的例子中，person消息就定义在Personlnfo消息内如

```ProtoBuf
message PersonInfo{
  message Person{
    string name =1;
    int32 Height =2;
    repeated int32 weight = 3;
  }
  repeated Person info =1
}
```

如果要在它的父消息类型的外部重用这个消息类型，需要PersonInfo.Person的形式使用它，如:

```ProtoBuf
message PersonMessage{
    PersonInfo.Person info =1;
}
```

#### 服务定义

如果想要将消息类型用在RPC系统中，可以在.proto文件中定义一个RPC服务接口，protocol buffer编译器将会根据所选择的不同语言生成服务接口代码及存根。

```ProtoBuf
service searchservice{
    //rpc 服务函数名(参数)返回(返回参数)
    rpc search(searchRequest)returns (searchResponse)
}
```

上述代表表示，定义了一个RPC服务，该方法接受SearchRequest返回SearchResponse

## 加密和认证

grpc的认证不是用户的身份认证,而是指多个server 和 多个client之间，如何识别对方是谁，并且可以安全的进行数据传输,常见的认证方式有

- SSL/TLS认证方式(采用http2协议)
    
- 基于Token的认证方式(基于安全连接)
    
- 不采用任何措施的连接，这是不安全的连接(默认采用http1)
    
- 自定义的身份认证
    

#### SSL加密

1. 安装[OpenSSL](https://slproweb.com/products/Win32OpenSSL.html)
    
2. 生成证书
    

相关命令,直接在对应文件夹下cmd即可

```Shell
#1、生成私钥
openssl genrsa -out server.key 2048

#2、生成证书 全部回车即可，可以不填
openssl req -new -x509 -key server.key -out server.crt -days 36500
# 国家名称
Country Name(2 etter code)[AU]:CN
# 省名称
State or Province Name(full name)[Some-State]:GuangDong
#城市名称
Locality Name(eg,city)[]:Meizhou
# 公司组织名称
Organization Name(eg, company)[Internet Widgits pty Ltd]:Xuexiangban
# 部门名称
organizational Unit Name(eg,section)[]:go
# 服务器or网站名称
Common Name(e.g. server FODN or yOUR name)[l:kuangstudy
#邮件
Email Address:24736743@gq.com

#3、生成 csr
openssl req -new -key server.key -out server.csr

#更改openssl.cnf (Linux 是openss1.cfg
#1)复制一份你安装的openss1的bin目录里面的openss1.cnf 文件到你项目所在的目录
#2)找到[CA_default]，打开 copy_extensions = copy (就是把前面的#去掉)
#3)找到[ reg 1,打开 reg extensions = v3 reg # The extensions to add to a certificate reguest
#4)找到[v3_reg]，添加 subjectAltName = @alt_names
#5)添加新的标签[alt_names]，和标签字段
DNS.1 =*.kuangstudy.com 表示用那个域名允许访问

#生成证书私钥test.key
openssl genpkey -algorithm RSA -out test.key

#通过私钥test.key生成证书请求文件test.csr(注意cfg和cnf)
openssl req -new -nodes -key test.key -out test.csr -days 3650 -subj "/C=cn/OU=myorg/O=mycomp/CN=myname" -config ./openssl.cfg -extensions v3_req
#test.csn是上面生成的证书请求文件。ca.crt/server.key是CA证书文件和key，用来对test,csr进行签名认证。这两个文件在第一部分生成。

#生成SAN证书 pem
openssl x509 -req -days 365 -in test.csr -out test.pem -CA server.crt -CAkey server.key -CAcreateserial -extfile ./openssl.cfg -extensions v3_req
```

执行完之后会有如下这些文件

![[Pasted image 20250715130129.png]]

其中常用的就是pem证书签名和key私钥

##### 修改服务端代码

```Go
func main() {
    creds, _ := credentials.NewServerTLSFromFile("D:\\GoLand\\gRPC_learning\\key\\test.pem",
       "D:\\GoLand\\gRPC_learning\\key\\test.key") //创建证书
    //开启端口
    listen, err := net.Listen("tcp", ":9090")
    if err != nil {
       return
    }
    //创建gRPC服务并传入证书
    grpcServer := grpc.NewServer(grpc.Creds(creds))
    //在grpc服务端中区注册我们自己编写的服务
    service.RegisterSayHelloServer(grpcServer, &server{})
    //启动服务
    err = grpcServer.Serve(listen)
    if err != nil {
       return
    }
}
```

##### 修改客户端代码

```Go
func main() {
    cred, _ := credentials.NewClientTLSFromFile("D:\\GoLand\\gRPC_learning\\key\\test.pem", "*.sunny.com") //指定访问路径,获取证书
    //连接到服务端,第二个参数为加密方式,传入自己的证书
    conn, _ := grpc.NewClient("127.0.0.1:9090", grpc.WithTransportCredentials(cred))
    //手动关闭连接
    defer conn.Close()
    //建立连接
    client := service.NewSayHelloClient(conn)
    //执行rpc调用,第一个参数用于找到对应的上下文
    response, err := client.SayHello(context.Background(), &service.HelloRequest{RequestName: "Sunny"})
    if err != nil {
       println("error")
       return
    }
    println(response.GetResponseMsg())
}
```

#### Token认证

##### 客户端修改

```Go
type ClientTokenAuth struct {
    credentials.PerRPCCredentials //这是一个接口,要实现其中的以下两个方法
}

func (c ClientTokenAuth) GetRequestMetadata(ctx context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
       "appId":  "Sunny", //这里的大写字母传到服务端后会变为小写,用大写会接受不到
       "appKey": "123456",
    }, nil
}
func (c ClientTokenAuth) RequireTransportSecurity() bool {
    return false //表示不器用TLS加密,启用返回true
}
func main() {
    //cred, _ := credentials.NewClientTLSFromFile("D:\\GoLand\\gRPC_learning\\key\\test.pem", "*.sunny.com")
    var opts []grpc.DialOption                                                    //将设置放在一个切片中
    opts = append(opts, grpc.WithTransportCredentials(insecure.NewCredentials())) //不使用加密,Token可以和TLS配合,如需打开按之前的写法打开即可
    opts = append(opts, grpc.WithPerRPCCredentials(new(ClientTokenAuth)))         //传递Token
    //连接到服务端,第二个参数为加密方式(此处为不加密)
    conn, _ := grpc.NewClient("127.0.0.1:9090", opts...) //展开切片
    //手动关闭连接
    defer conn.Close()
    //建立连接
    client := service.NewSayHelloClient(conn)
    //执行rpc调用,第一个参数用于找到对应的上下文
    response, err := client.SayHello(context.Background(), &service.HelloRequest{RequestName: "Sunny"})
    if err != nil {
       println(err.Error())
       return
    }
    println(response.GetResponseMsg())
}
```

##### 服务端配置拦截

服务端可以新建一个拦截器,这里直接放在业务逻辑中

```Go
func (s *server) SayHello(ctx context.Context, req *service.HelloRequest) (*service.HelloResponse, error) {
    md, ok := metadata.FromIncomingContext(ctx) //获取Token信息
    if !ok {
       return nil, errors.New("无Token")
    }
    var appId string
    var appKey string
    if v, ok := md["appid"]; ok {
       appId = v[0]
    }
    if v, ok := md["appkey"]; ok { //key要小写
       appKey = v[0]
    }
    if appId != "Sunny" || appKey != "123456" {
       return nil, errors.New("token拦截")
    }
    return &service.HelloResponse{ResponseMsg: "hello " + req.RequestName}, nil
}
```