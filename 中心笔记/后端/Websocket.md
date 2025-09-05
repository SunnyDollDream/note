## 常见的消息推送方式

#### 轮询

![[Pasted image 20250715135258.png]]

#### 长轮询(http长连接就是用长轮询实现的)

![[Pasted image 20250715135303.png]]

#### SSE(server-sent event,服务器发送事件)

注意是由服务器向客户端发送数据

![[Pasted image 20250715135310.png]]

#### websocket

WebSocket是一种在基于TCP连接上进行全双工通信的协议

> 全双工(Ful Duplex):允许数据在两个方向上同时传输。
> 
> 半双工(Half Duplex):允许数据在两个方向上传输，但是同一个时间段内只允许一个方向上传输。

![[Pasted image 20250715135316.png]]

一开始由客户端发送http请求,请求将http请求升级为websocket请求,再由服务器返回一个101响应,之后才算建立了ws链接

## websocket的使用

> Tomcat的7.0.5 版本开始支持WebSocket,并且实现了Java WebSocket规范。

#### 引入依赖

```YAML
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

#### 编写配置类

```Java
//扫描添加有@ServerEndpoint注解的 Bean
@Configuration
public class WebSocketConfig {
    @Bean
    //注入ServerEndpointExporter，自动注册使用@ServerEndpoint注解的类
    public ServerEndpointExporter serverEndpointExporter(){
        return new ServerEndpointExporter();
    }
}
//编写配置类，用于获取HttpSession对象(因为用户信息还是在http中的)//这个会在触发@Onopen
//之前进入并过一遍里边的代码,可以在这里注入用户信息
public class GetHttpSessionConfigurator extends ServerEndpointConfig.Configurator { //继承一个内部类
@Override
public void modifyHandshake(ServerEndpointConfig sec, HandshakeRequest request, HandshakeResponse response){
        HttpSession httpSession=(HttpSession)request.getHttpSession();
        //将httpsession对象存储到配置对象中
        sec.getUserProperties().put(HttpSession.class.getName(),httpSession);
    }
}
//注册好第二个配置类后还需要在 @ServerEndpoint 注解中引入配置器
@ServerEndPoint(value ="/chat",configurator = GetHttpSessionConfigurator.class)
```

#### 编写EndPoint类

Java WebSocket应用由一系列的Endpoint组成。Endpoint是一个java对象，代表WebSocket链接的一端，对于服务端，我们可以视为处理具体WebSocket消息的接口(这个类是多例的)。

我们可以通过两种方式定义Endpoint:

第一种是编程式，即继承类javax.websocket.Endpoint并实现其方法。

第二种是注解式,即定义一个POJO,并添加@ServerEndpoint相关注解。

Endpoint实例在WebSocket握手时创建，并在客户端与服务端链接过程中有效，最后在链接关闭时结束。在Endpoint接口中明确定义了与其生命周期相关的方法，规范实现者确保生命周期的各个阶段调用实例的相关方法。生命周期方法如下:

![[Pasted image 20250715135328.png]]

#### [另一种方法](https://blog.csdn.net/qq_16430873/article/details/126664778)

#### 服务端接收客户端发送的数据

- 编程式:通过添加一个类,实现MessageHandler消息处理器接口,实现其中的对应方法来接收消息
    
- 注解式:在定义Endpoint时，通过@OnMessage注解指定接收消息的方法
    

#### 服务端推送数据给客户端

发送消息则由 RemoteEndpoint 完成，其实例由 Session(websocket链接成功后自动创建) 维护。

发送消息有2种方式发送消息

- 通过session.getBasicRemote 获取同步消息发送的实例，然后调用其 sendXxx()方法发送消息
    
- 通过session.getAsyncRemote 获取异步消息发送实例，然后调用其 sendXxx() 方法发送消息
    

```Java
@serverEndpoint("/chat/",configurator = GetHttpSessionConfigurator.class)//这里APIFOX会自己生成一个/,所以就加上了
@Component
public class chatEndpoint {
private
@onopen
//连接建立时被调用
public void onopen(Session sessjon, Endpointconfig config){//连接时被创建的session
    ...
}
@OnMessage
//按收到客户端发送的数据时被调用
public void onMessage(string message){//Message即为接收到的数据
    ...
}
@OnClose
//连接关闭时被调用
public void onclose(Session session){
    ...
}
```

案例:线上聊天室

![[Pasted image 20250715135339.png]]