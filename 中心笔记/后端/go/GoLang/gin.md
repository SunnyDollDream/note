gin是一个go的web框架

```Go
func main() { 
    router := gin.Default() // 创建一个默认的路由

    router.GET("/index", func(context *gin.Context) { //设置路由规则为/index,配置处理逻辑,其中Context为上下文变量
       context.String(200, "Hello World!") //响应一个字符串 
    })

    err := router.Run(":8082") //在0.0.0.0:8082端口启用监听(0.0.0.0为本机的所有ip)
    if err != nil {
       return 
    }
    http.ListenAndServer(":8080",router) //原生的web方式也可以用,因为run就是对原生的封装
}
```

## 响应

.String()返回字符串响应

.JSON()可以返回json响应,输入一个任意类型的对象就可以(支持属性名tag修改json格式,json别名为"-"时为不显示),也可以为gin.H{"key":"value"...}直接传JSON串

.XML()响应xml

.YAML()响应yaml

.HTML()可以响应html页面,在使用前需要加载html模版所在的文件夹(router.LoadHTMLGlob("template/*")),之后在调用HTML(200,"index.html",gin.H{"k":"v"...}(填充html中的变量))

#### 响应文件

> 在GO中没有相对文件的路径,只有相对项目的路径,即不论你在哪个文件中使用./,都是从项目路径开始的

router中有,StaticFile("(网站访问路径)","(静态文件的路径)")和staticFS("<访问路径>",http.Dir("静态资源目录"))来配置静态资源目录

#### 重定向

context有方法.Redirect(),里边的响应内容可以写一个网址(可以是别的网站,也可以是自己项目里的api),这样在访问自己写的api时就会跳转到对应的网址(响应码应为3xx)

301 Moved Permanently

被请求的资源已永久移动到新位置，并且将来任何对此资源的引用都应该使用本响应返回的若干个 URI 之一。如果可能，拥有链接编辑功能的客户端应当自动把请求的地址修改为从服务器反馈回来的地址。除非额外指定，否则这个响应也是可缓存的。

302 Found

请求的资源现在临时从不同的 URI 响应请求。由于这样的重定向是临时的，客户端应当继续向原有地址发送以后的请求。只有在Cache-Control或Expires中进行了指定的情况下，这个响应才是可缓存的。

## 请求

context中有.Query(key String)方法,可以获取在路径里拼接的参数,key就是路径中的参数名,如果没有则返回一个空字符串,.GetQueryArray(key)也可以获取参数但是会在没有对应参数时返回第二个参数代表是否有当前参数(注意没有是指参数列表中没有,当参数值为null也是表示参数存在),.QueryArry(key)可以获取参数中所有名为key的值(返回值是一个切片),前边两种则只能获取一个,QueryMap

#### 动态参数

即使用Restful拼接在路径中的参数,GO中表示这种占位符的方法是/param/:key/,这样/param/value就会把value传入这个key中,调用context中的.Params(key)就可以获取对应值了

#### 表单参数

即body中的form-data和x-www-form中的参数,调用context.PostForm(key)即可得到

.PostFormArray()可以获取多个

.DefaultPostForm(key,默认值)可以给对应参数赋初始值

.MultiPartFile()(无参)可以获取所有 参数,并封装为一个结构体

#### 原始参数

这种接受方式一般用来接受json串,会以byte数组的方式返回,用.GetRawData()获取,输出时可以直接强转为String

.GetHeader()可以获取请求头中的参数

要解接收到的byte数组可以使用json中的,Unmarshal(byteArray),用一个结构体接受(对应的json标签要写上)

#### 参数绑定

绑定时传入的参数都为对应对象的指针

**绑定Json中的参数:**

这些方法都需要指定对象的json tag

1. **MustBind**
    

Context对象中有一些方法以Bind开头,这些方法是用于绑定代码中的对象和请求中的参数的,他们底层都是MustBind(),这种方法在绑定失败时会修改相应码,所以不推荐使用

2. **ShouldBind**
    

还有一些以ShouldBind开头的,这些方法会在绑定失败时返回错误对象,可以在代码中自己进行处理

#### 请求头

Context对象中有方法.GetHeader(key)可以获取请求头中的对应参数(key的首字母大小写不影响结果,单词之间需要用-连接),Context对象中有对象Request代表请求本身,其中又有属性Header(类型为map[string][]string)代表请求头,

其中有所有的请求头参数(GetHeader方法只能获取第一个对应的属性,第二种可以获取所有但是严格区分key的书写形式)

**绑定param参数:**

这些方法都需要指定form tag

1. ...Query
    

上边那些Bind方法都有...BindQuery(),这些方法可以绑定对应的param参数

**绑定动态参数:**

这些方法的tag为'uri'

1. ...Uri
    

这些方法可以绑定restful中的路径参数

**绑定表单参数:**

可以直接使用Bind()方法绑定这类表单参数,tag还是'form'

#### 响应头

Context对象中有方法.Header(key,v)可以设置之后返回的响应头中的内容,可以用来修改响应的格式

## 验证器(类似Validation)

验证器可以对Bind所要绑定的参数进行校验,bind方法只对类型进行检测

Binding tag可以为

![[Pasted image 20250715125918.png]]

gin增加了一些在web中常用的数据校验器
![[Pasted image 20250715125931.png]]


#### 自定义错误信息

调用error对象的.(validator.<错误类型>)可以获取所有校验器报错作为一个切片,通过其中的ValidationErrors对象就可以利用反射获取报错的是哪一个字段,此时就可反射得到自己写的tag,在里边写对应的报错信息返回就可以实现自定义错误信息了

```Go
package main

import (
  "fmt"
  "github.com/gin-gonic/gin"
  "github.com/gin-gonic/gin/binding"
  "github.com/go-playground/locales/zh"
  "github.com/go-playground/universal-translator"
  "github.com/go-playground/validator/v10"
  zh_translations "github.com/go-playground/validator/v10/translations/zh"
  "net/http"
  "reflect"
  "strings"
)

var trans ut.Translator

func init() {
  // 创建翻译器
  uni := ut.New(zh.New())
  trans, _ = uni.GetTranslator("zh")

  // 注册翻译器
  v, ok := binding.Validator.Engine().(*validator.Validate)
  if ok {
    _ = zh_translations.RegisterDefaultTranslations(v, trans)
  }

  v.RegisterTagNameFunc(func(field reflect.StructField) string {
    label := field.Tag.Get("label")
    if label == "" {
      label = field.Name
    }
    name := field.Tag.Get("json")
    return fmt.Sprintf("%s---%s", name, label)
  })
}

/*
{
  "name": "name参数必填",
}
*/

func ValidateErr(err error) any {
  errs, ok := err.(validator.ValidationErrors)
  if !ok {
    return err.Error()
  }
  var m = map[string]any{}
  for _, e := range errs {
    msg := e.Translate(trans)
    _list := strings.Split(msg, "---")
    m[_list[0]] = _list[1]
  }
  return m
}

type User struct {
  Name  string `json:"name" binding:"required" label:"用户名"`
  Email string `json:"email" binding:"required,email"`
}

func main() {
  r := gin.Default()
  // 注册路由
  r.POST("/user", func(c *gin.Context) {
    var user User
    if err := c.ShouldBindJSON(&user); err != nil {
      // 参数验证失败
      c.JSON(200, map[string]any{
        "code": 7,
        "msg":  "验证错误",
        "data": ValidateErr(err),
      })
      return
    }

    // 参数验证成功
    c.JSON(http.StatusOK, gin.H{
      "message": fmt.Sprintf("Hello, %s! Your email is %s.", user.Name, user.Email),
    })
  })

  // 启动HTTP服务器
  r.Run()
}
```

#### 自定义验证器

使用bind.Validator.Engine().(*validator.Validate)就可以定义一个Validate对象,再调用其.RegisterValidation("<对应的自定义tag>",处理的函数),这里的处理函数的形参需要为一个FieldLevel,返回值为bool,false表示失败,true表示验证通过

## 文件上传

#### 单文件

Context对象有方法.FormFile("<文件对应的参数名>"),会返回FileHeader对象和一个error对象,FileHeader中有方法可以获取文件的一些信息

要保存文件可以调用Context的.SaveUploadedFile(file FileHeader,"<文件上传路径(要带文件名,从项目根路径开始写)>")

还可以调用FileHeader对象的.Open()方法,这个方法可以返回一个File接口和error(这样读取的文件是只读的),然后使用io包下的.ReadAll(File),这个方法会返回一个字节数组,然后再使用os包下的Create("<文件相对路径>")创建一个空白的文件对象并返回一个File指针,通过io.Copy(目标文件,源文件)方法复制读取的内容到刚才创建的文件中,之后要把用os打开的文件close()掉

#### 多文件

Context中有方法.MultipartForm()方法会返回一个Form指针,其中有一个map,k为参数名,v为FileHeader的指针数组,之后再各自保存即可

## 文件下载

可以直接使用Context的.File相应返回一个文件回去,但是大多数时候这样直接返回浏览器并不会唤起下载功能,所以我们要修改响应头

```Go
c.Header("Content-Type", "application/octet-stream")              // 表示是文件流，唤起浏览器下载，一般设置了这个，就要设置文件名
c.Header("Content-Disposition", "attachment; filename="+"牛逼.png") // 用来指定下载下来的文件名
c.Header("Content-Transfer-Encoding", "binary")                   // 表示传输过程中的编码形式，乱码问题可能就是因为它
c.File("uploads/12.png")
```

## 中间件

Gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（Hook）函数。这个钩子函数就叫中间件，中间件适合处理一些公共的业务逻辑，比如登录认证、权限校验、数据分页、记录日志、耗时统计等

即比如，如果访问一个网页的话，不管访问什么路径都需要进行登录，此时就需要为所有路径的处理函数进行统一一个中间件

Gin中的中间件必须是一个HandlerFunc类型(即函数形参为Context指针),可以有多个函数,执行顺序为在形参中声明的顺序,当多个中间件都存在响应时,多个响应会拼接在一块响应两次(即响应后不会影响后续代码的执行),如果想让一次响应后的中间件不再执行,可以在一次响应后调用Context中的.Abort()方法,这样之后的所有中间件就都不会执行了

#### 响应中间件和请求中间件

Context有方法.Next(),在中间件中执行这个方法表示这个方法之前的为请求中间件,之后的为响应中间件,实际效果类似dater,即会在请求时只执行请求中间件的代码,都执行完后再去执行响应中间件的代码,执行顺序为先进后出,如果使用Abort打断则只会执行到最后一个中间件的响应中间件,类似拦截器链(调用Abort后会直接执行当前中间件的响应部分,在响应部分调用Abort没有效果)

#### 全局注册中间件

Engine对象有.Use()方法,可以传入一个中间件函数,这样这个中间件就会对所有的路由生效(即使请求不正确也会生效),也可以传多个

#### 中间件传递数据

Context对象有.Set(k,v)可以设置参数,这样就可以在别的中间件中使用.Get(k)获取这个参数,达到传递参数的效果(这里边存储的是一个空接口,所以如果需要使用需要强转)

#### gin的.Default()方法

这个方法返回的Engine其实是有log和recover中间件的,这两个中间件分别是用来记录日志到控制台和防止panic导致当前接口不可用的(不可用时请求不会有相应码),如果使用New方法就会获得一个没有任何中间件的Engine,此时报错会报原生错误,并且panic后就没有响应了,并且此时也不会向控制台输出日志了

#### 鉴权案例

```Go
package main

import (
  "github.com/gin-gonic/gin"
)

func JwtTokenMiddleware(c *gin.Context) {
  // 获取请求头的token
  token := c.GetHeader("token")
  // 调用jwt的验证函数
  if token == "1234" {
    // 验证通过
    c.Next()
    return
  }
  // 验证不通过
  c.JSON(200, gin.H{"msg": "权限验证失败"})
  c.Abort()
}

func main() {
  router := gin.Default()

  api := router.Group("/api")

  apiUser := api.Group("")
  {
    apiUser.POST("login", func(c *gin.Context) {
      c.JSON(200, gin.H{"msg": "登录成功"})
    })
  }
  apiHome := api.Group("system").Use(JwtTokenMiddleware)
  {
    apiHome.GET("/index", func(c *gin.Context) {
      c.String(200, "index")
    })
    apiHome.GET("/home", func(c *gin.Context) {
      c.String(200, "home")
    })
  }

  router.Run(":8080")
}
```

## 路由分组

```Go
package main

import "github.com/gin-gonic/gin"

func main() {
  router := gin.Default()

  r := router.Group("/api") //创建路由分组(RouterGroup对象)
  r.GET("/index", func(c *gin.Context) { //之后用这个路由分组就可以了
    c.String(200, "index")
  })
  r.GET("/home", func(c *gin.Context) {
    c.String(200, "home")
  })

  router.Run(":8080")
}
```

#### 路由分组注册中间件

RouterGroup对象也有.Use()方法可以设置全局中间件,因为中间件函数的参数是固定的,所以如果需要初始中间件时传递一些参数,可以再声明另一个函数,让其返回值为一个中间件函数,这样就可以自定义形参了

## 日志

#### gin内置日志

```Go
package main

import (
  "github.com/gin-gonic/gin"
  "io"
  "os"
)

func main() {
  // 输出到文件
  f, _ := os.Create("gin.log")
  gin.DefaultWriter = io.MultiWriter(f)
  
  // 如果需要同时将日志写入文件和控制台，请使用以下代码。
  //gin.DefaultWriter = io.MultiWriter(f, os.Stdout)
  
  router := gin.Default()
  router.GET("/", func(c *gin.Context) {
    c.JSON(200, gin.H{"msg": "/"})
  })
  router.Run()
}
```

gin默认是在debug模式下的,如果想要修改可以调用 gin.SetMode(gin.ReleaseMode(生产环境))或者设置环境变量来修改

#### 自定义日志格式

如果想要修改日志的格式,此时就不能使用Default的Engine了,需要.New然后自己重写LoggerWith

Foematter或LoggerWithConfig,实现其方法,直接return一个格式拼接后的字符串就可以(如fmt.Sprintf(格式字符串,参数...))

#### logrus

**下载:****`go get`** **`github.com/sirupsen/logrus`**

**常用方法:**

```Go
logrus.Debugln("Debugln")
logrus.Infoln("Infoln")
logrus.Warnln("Warnln")
logrus.Errorln("Errorln")
logrus.Println("Println")

// 输出如下
time="2022-12-17T14:02:01+08:00" level=info msg=Infoln   
time="2022-12-17T14:02:01+08:00" level=warning msg=Warnln
time="2022-12-17T14:02:01+08:00" level=error msg=Errorln 
time="2022-12-17T14:02:01+08:00" level=info msg=Println
```

**日志等级:**

logrus默认的日志输出等级是 info

```Go
PanicLevel  // 会抛一个异常
FatalLevel  // 打印日志之后就会退出
ErrorLevel
WarnLevel
InfoLevel
DebugLevel
TraceLevel  // 低级别
logrus.SetLevel(logrus.DebugLevel) //修改日志等级
```