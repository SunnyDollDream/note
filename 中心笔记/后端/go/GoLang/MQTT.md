>这里是客户端的配置,服务端是一个linux应用,效果类似MQ

[如何在 Golang 中使用 MQTT | EMQ](https://www.emqx.com/zh/blog/how-to-use-mqtt-in-golang)
安装
```bash
go get github.com/eclipse/paho.mqtt.golang
# 如果go版本不够可以去github页找版本号下之前的
# (如go get github.com/eclipse/paho.mqtt.golang@v1.5.0)
```
创建客户端
```go
func InitMQTT() {  
    var broker = AppConfig.MQTT.Host  // MQTT服务端的ip
    var port = AppConfig.MQTT.Port  //服务端端口
    opts := mqtt.NewClientOptions()  
    opts.AddBroker(fmt.Sprintf("tcp://%s:%d", broker, port))  
    opts.SetClientID(AppConfig.MQTT.ClientID)  // 设置当前连接在MQTT服务端的唯一标识
    //opts.SetUsername("emqx")  
    //opts.SetPassword("public")
    // 这两句是设置用户名和密码的,需要同时在服务端配置,这里不配是因为服务端关闭了验证    
    opts.SetDefaultPublishHandler(messagePubHandler)  //设置默认的消息回调函数
    opts.OnConnect = connectHandler  //连接成功的回调
    opts.OnConnectionLost = connectLostHandler  //断开的回调
    client := mqtt.NewClient(opts)  
    if token := client.Connect(); token.Wait() && token.Error() != nil {  
       panic(token.Error())  
    }  //尝试建立连接
    global.MQTT = &client  
}
```
回调函数
```go
var messagePubHandler mqtt.MessageHandler = func(client mqtt.Client, msg mqtt.Message) {  
    fmt.Printf("Received message: %s from topic: %s\n", msg.Payload(), msg.Topic())  
}  
  
var connectHandler mqtt.OnConnectHandler = func(client mqtt.Client) {  
    fmt.Println("Connected")  
}  
  
var connectLostHandler mqtt.ConnectionLostHandler = func(client mqtt.Client, err error) {  
    fmt.Printf("Connect lost: %v", err)  
}
```
通过客户端连接进行订阅
```go
func sub(client mqtt.Client) {
    topic := "topic/test"
    token := client.Subscribe(topic, 1, nil)
    token.Wait()
    fmt.Printf("Subscribed to topic %s", topic)
}

```
发布消息
```go
func publish(client mqtt.Client) {
    num := 10
    for i := 0; i < num; i++ {
        text := fmt.Sprintf("Message %d", i)
        token := client.Publish("topic/test", 0, false, text)
        token.Wait()
        time.Sleep(time.Second)
    }
}

```