Go语言是一门编译语言,即会把代码编译为可执行文件

## GO语言的特点

1. 高性能、高并发
    
2. 语法简单、学习曲线平缓
    
3. 丰富的标准库
    
4. 完善的工具链
    
5. 静态链接
    
6. 快速编译
    
7. 跨平台
    
8. 垃圾回收
    

## GO的语法

#### 框架

```Go
package main

import (
    "fmt"
)

func main() { //println拼接只要当做多个参数就可以了
    fmt.Println("hello world") //GO的语句以 行 为分隔,不需要加分号了
    //这个包下还有print和printf(支持占位符和格式控制%.3f)
}
```

go在运行时会从main包下的main函数开始执行,如果没有就会报错

#### 命令行工具

```PowerShell
go run example/01-hello/main.go # 运行
# 运行结果: hello world
go build example/01-hello/main.go # 编译为二进制文件
./main # 运行
# 运行结果: hello world
```

#### 读取用户输入

```Go
reader := bufio.NewReader(os.Stdin) //获取输入流对象
input, err := reader.ReadString('\n') //读入一行
if err != nil {
    fmt.Println("An error occured while reading input. Please try again", err)
    return
}
input = strings.Trim(input, "\r\n") //除去多余回车
```

P.S.这样只是读入了一个字符串,后续处理要自己进行

#### 运算符

go的运算符与其他语言基本一致,其中自增运算符只支持后置不支持前置

#### 变量
![[Pasted image 20250715130227.png]]
![[Pasted image 20250715130233.png]]
![[Pasted image 20250715130239.png]]
GO是一门强类型语言,但是自行指定变量类型的方式类似python,为var b,c int = 1,2(这是有具体类型的,不是python的只有标记作用).这是因为声明变量时var类似C++的auto类型,会自动判断所接收变量的类型并定义,所以平时声明变量的方式如下:

```Go
package main

import (
    "fmt"
    "math"
)

func main() {

    var a = "initial" //自动判断

    var b, c int = 1, 2 //强制指定
    //还可以这样声明(const同理)
    var(
        a = 1
        b = 2
    )
    var d = true

    var e float64 //float32和64类似float和Double的区别

    f := float32(e) //另一种声明方式,效果类似var自动声明(这里还有强制类型转化?)

    g := a + "foo" //字符串支持+拼接
    fmt.Println(a, b, c, d, e, f) // initial 1 2 true 0 0
    fmt.Println(g)                // initialapple

    const s string = "constant" //常量声明(注意只有const没有var了,const不再只是一个修饰符)
    const h = 500000000 //常量是没有类型的,会根据上下文自动判断此处应该用的类型
    const i = 3e20 / h
    fmt.Println(s, h, i, math.Sin(h), math.Sin(i))
    
    var a [5]int //声明int数组
    a[4] = 100 
    fmt.Println("get:", a[2])
    fmt.Println("len:", len(a)) //获取元素个数
    myArray := []int{1,2,3,4} //这样可以定义一个动态数组,效果类似切片
    b := [5]int{1, 2, 3, 4, 5} //直接初始化
    fmt.Println(b)

    var twoD [2][3]int //二维数组
    for i := 0; i < 2; i++ {
        for j := 0; j < 3; j++ {
           twoD[i][j] = i + j
        }
    }
    fmt.Println("2d: ", twoD)
}
```

GO语言中未初始化的变量会赋默认值

![[Pasted image 20250715130300.png]]

Go中只有布尔值为true才为真,false才为假,不能用其他值代替(如0或' ')

在main函数外声明变量只能使用var,此时声明的变量作用域为package,对当前package下的所有方法都可见

在整形变量类型前加一个u可以代表无符号整形.

int 和 uint 是针对日标设备优化的类型:

- 在树莓派 2、比较老的移动设备上，int 和 uint 都是 32 位的:
    
- 在比较新的计算机上，int 和 uint 都是 64 位的
    

但是int,int64和int32是三种不同的数据类型,并不能混为一谈

#### 流程控制

1. If-else: GO的if语句条件不用再加括号(可以加,但是会被编译器删掉),还有就是必须有大括号,不能因为只有一条语句就不加大括号(Go中仍然存在逻辑短路)
    

```Go
package main

import "fmt"

func main() {

    if 7%2 == 0 {
       fmt.Println("7 is even")
    } else {
       fmt.Println("7 is odd")
    }

    if 8%4 == 0 {
       fmt.Println("8 is divisible by 4")
    }

    if num := 9; num < 0 { //条件中支持定义变量
       fmt.Println(num, "is negative")
    } else if num < 10 {
       fmt.Println(num, "has 1 digit")
    } else {
       fmt.Println(num, "has multiple digits")
    }
}
```

2. for循环:GO里只有for循环,支持continue和break,条件语句也不用加括号,其中仍然是三段式,可以省略其中任意部分,而且省略时也不需要保留分号了(P.S.for和if等的条件语句内不能使用var声明变量,只能使用短声明:=声明变量)
    

```Go
package main

import "fmt"

func main() {

    i := 1
    for { //什么都不写就相当于true,死循环
       fmt.Println("loop")
       break
    }
    for j := 7; j < 9; j++ {
       fmt.Println(j)
    }

    for n := 0; n < 5; n++ {
       if n%2 == 0 {
          continue
       }
       fmt.Println(n)
    }
    for i <= 3 {
       fmt.Println(i)
       i = i + 1
    }
}
```

3. switch-case:GO中的switch也不需要加括号,直接写变量名就可以,而且switch中的case也不需要额外加break了,执行完一个case不会继续向下执行了会自动退出,如果要让他继续向下执行需要执行fallthrough关键字(会执行下一个case体里的内容),另外,switch的变量也可以不写,在case中写条件语句,这样可以实现类似多个if的效果
    

```Go
package main

import (
    "fmt"
    "time"
)

func main() {

    a := 2
    switch a {
    case 1:
       fmt.Println("one")
    case 2:
       fmt.Println("two")
    case 3:
       fmt.Println("three")
    case 4, 5:
       fmt.Println("four or five")
    default:
       fmt.Println("other")
    }

    t := time.Now()
    switch { //什么都不写
    case t.Hour() < 12:
       fmt.Println("It's before noon")
    default:
       fmt.Println("It's after noon")
    }
}
```

#### 容器类型

1. 切片:切片(slice)类似Vector之类的可变数组,使用方式如下:
    

```Go
package main

import "fmt"

func main() {
    var slice []int //声明一个空切片,他的容量为0,想要判断数组是否为空可以让其==nil进行比较
    s := make([]string, 3) //声明,相当于申请了三个数组元素的空间
    s[0] = "a"
    s[1] = "b"
    s[2] = "c"
    fmt.Println("get:", s[2])   // c
    fmt.Println("len:", len(s)) // 3
    
    s = append(s, "d") //append的本质是在数组s后加上一个元素,不够则重新申请一个数组并返回他的指针
    s = append(s, "e", "f") //所以需要用s再去接收一次,可以同时添加多个值
    fmt.Println(s) // [a b c d e f]

    c := make([]string, len(s))
    copy(c, s) //复制数组
    fmt.Println(c) // [a b c d e f]
    //类似python,输出指定区间的值,左闭右开,可以缺省,但是不支持负数索引
    fmt.Println(s[2:5]) // [c d e]
    fmt.Println(s[:5])  // [a b c d e]
    fmt.Println(s[2:])  // [c d e f]
    //使用这种方式获取的其实是对应部分的头指针,所以在修改时会把原切片的值也进行修改
    good := []string{"g", "o", "o", "d"}
    fmt.Println(good) // [g o o d]
}
```

make函数还有第三个参数,可以指定切片的容量,可以使用cap()得到一个切片的容量,此时会直接为切片申请容量个数的内存,但是大于长度的下标值是无法访问的,需要使用.append()来增加长度才可以访问,容量满了以后才会再去申请一个cap大小的内存,所以cap就是每次额外申请容量时的个数,不指定容量时和长度一致

如果想要单纯的拷贝数组的值,可以使用copy(goal,source)来深拷贝

2. map:GO的map是无序的,遍历时和键值大小和插入顺序都没关系,会随机输出
    

```Go
package main

import "fmt"

func main() {
    m := make(map[string]int)   //声明,括号里的是key的类型,后边的是值的类型
    m["one"] = 1 //支持类似数组的访问方式
    m["two"] = 2
    fmt.Println(m)           // map[one:1 two:2] //比较数组会多一个map在前边
    fmt.Println(len(m))      // 2
    fmt.Println(m["one"])    // 1
    fmt.Println(m["unknow"]) // 0

    r, ok := m["unknow"] //在获取键值时,可以加一个ok变量,他是一个bool,代表这个key对应的键值是否存在.
    fmt.Println(r, ok) // 0 false //表示键值不存在

    delete(m, "one") //删除元素

    m2 := map[string]int{"one": 1, "two": 2} //初始化赋值
    var m3 = map[string]int{"one": 1, "two": 2}
    fmt.Println(m2, m3)
}
```

map作为参数类型时是使用的引用传递

#### for-range遍历

GO可以使用for-range(类似python)的方法进行遍历,使用方法如下:

```Go
package main

import "fmt"

func main() {
    nums := []int{2, 3, 4}
    sum := 0
    for i, num := range nums { //可以用两个变量,遍历的类似一个键值对
        sum += num
        if num == 2 {
            fmt.Println("index:", i, "num:", num) // index: 0 num: 2
        }
    }
    fmt.Println(sum) // 9

    m := map[string]string{"a": "A", "b": "B"}
    for k, v := range m {
        fmt.Println(k, v) // b 8; a A
    }
    for k := range m { //也可以只写一个,就只遍历key
        fmt.Println("key", k) // key a; key b
    }
}
```

#### 函数

GO的函数返回值类型是后置的,并且支持返回多个值,但是只有第一个值是真正的返回值,其余的是错误信息,使用方法如下:

```Go
package main

import "fmt"

func add(a int, b int) int { //函数返回值类型在后边
    return a + b
}

func add2(a, b int) int { //声明形参时若都为同一类型可以这样声明
    return a + b
}

func exists(m map[string]string, k string) (v string, ok bool) { //返回多个值,返回类型也要声明多个并加括号
    v, ok = m[k]
    return v, ok
}
//函数的返回值可以命名也可以匿名,命名了的返回值可以不写在return中,而是给他在函数中访问并赋值
//这样只要写return就可以,会自动返回
func main() {
    res := add(1, 2)
    fmt.Println(res) // 3

    v, ok := exists(map[string]string{"a": "A"}, "a")
    fmt.Println(v, ok) // A True
}
```

**defer:**

defer是go中一种延迟调用机制，defer后面的函数只有在当前函数(可以是main函数)执行完毕后才能执行，将延迟的语句按defer的逆序进行执行，也就是说先被defer的语句最后被执行，最后被defer的语句，最先被执行，通常用于释放资源。,return会先于defer执行,并且defer可以写在return后边,仍然可以正常执行

```Go
func func1(){
    fmt.Println("我是 func1")
}
func func2(){
    fmt.Println("我是 func2")
}
func func3(){
    fmt.Println("我是 func3")
}
func main(){
    defer func1()
    defer func2()
    defer func3()
    fmt.Println("main1")
    fmt.Println("main2")
}
```

如果函数有参数的话,延迟函数的参数在defer声明时就决定了,即使defer传入的参数是一个变量,但是之后的程序改变这个变量并不会影响defer函数的输出.

```Go
func main(){
   i:= 0
   defer func(a int) { //支持匿名定义
                fmt.Println(a) //输出还是0
        }(i)
    i++
}
```

但是如果传入的是一个引用,那么就可以通过这个引用找到修改后的值

传参时如果形参类型为定长数组,在定义参数类型时需要写死数组的长度,并且传入的长度也只能是这个,长度的数组,传参时是将实参依次拷贝到形参中,所以也不能再函数中修改数组内容.但是使用变长数组就是引用传递了,可以在函数中修改实参的内容

#### 指针

GO虽然支持了指针,但是相对于C对指针的功能也有限制,只能使用指针去修改其指向的值,其用法如下:

```Go
package main

import "fmt"

func add2(n int) { //并不能正确执行,GO的形参也是值传递
    n += 2
}

func add2ptr(n *int) { //声明指针类型
    *n += 2 //解引用
}

func main() {
    n := 5
    add2(n)
    fmt.Println(n) // 5
    add2ptr(&n) //取地址
    fmt.Println(n) // 7
}
```

#### 结构体/类

GO也有类似C的结构体,其使用方法如下:

```Go
package main

import "fmt"

type user struct { //声明(Type相当于typedef,用于定义一种自定义类型)
    name     string
    password string
}

func main() {
    a := user{name: "wang", password: "1024"} //初始化,可以缺省属性名
    b := user{"wang", "1024"}
    c := user{name: "wang"}
    c.password = "1024" //修改属性
    var d user //声明变量
    d.name = "wang"
    d.password = "1024"

    fmt.Println(a, b, c, d)                 // {wang 1024} {wang 1024} {wang 1024} {wang 1024}
    fmt.Println(checkPassword(a, "haha"))   // false
    fmt.Println(checkPassword2(&a, "haha")) // false
}

func checkPassword(u user, password string) bool {
    return u.password == password
}

func checkPassword2(u *user, password string) bool {
    return u.password == password
}
```

GO的结构体更像是C++的类,你可以为结构体声明方法

```Go
package main

import "fmt"

type user struct {
    name     string
    password string
}

func (u user) checkPassword(password string) bool { //之前的结构体是在形参里的,前置即可声明为结构体方法,此时在函数声明里也还是可以使用结构体变量的
    return u.password == password
}

func (u *user) resetPassword(password string) { //带指针可以实现在函数体中对属性进行修改,更符合类的概念
    u.password = password
}

func main() {
    a := user{name: "wang", password: "1024"}
    a.resetPassword("2048") //就可以这样调用了
    fmt.Println(a.checkPassword("2048")) // true
}
```

结构体作为函数参数类型时使用值传递

声明了方法的结构体就是类,结构名首字母大写说明当前类在当前包下是公开的,小写则是私有的

#### 继承

GO的继承只要在另一个类中写一个父类的类名即可,这就代表当前类继承了父类,可以重写父类的方法,在调用时如果重写了就使用重写的方法,如果没有则调用父类中的方法

对应有继承的类,初始化时需要在大括号中先声明父类,如s := superMan{Human{"lisi","female"},88}

#### 多态

单纯类是不能实现多态的,GO中需要声明接口才能实现多态,go中的接口本质是一个指针(所以使用接口声明变量或形参时,要接收实体类需要对其取指针),指向当前具体的类型,要继承自一个接口不需要像继承类一样在类体内声明类名,只要实现了对应的方法就可以视为是实现了对应接口,就可以用这个接口类型的变量接收一个实现类的对象了

```Go
type Animal interface {
    Sleep() //抽象方法
    GetColor() string
    GetType() string
}
```

类似java中的Object,Go中所有的类和变量都可以视为继承自interface{}这个空接口(带着{}整个是一个类型),也就是说,使用这个类型可以接收所有的变量,要想要校验这种变量的类型,可以 变量.(类型)来检验,会返回两个值分别为括号里写的类型和是否为当前类型

#### 反射
![[Pasted image 20250715130325.png]]

一个变量里类似这种结构,即一个包含type和value的pair,其中type是静态类型或动态类型,让接口变量接收一个其他类型的变量就是类似把指针指向了这个pair

go中有reflect包,其中有.ValueOf()和.TypeOf(),分别用于获取输入的接口的的值和类型(接口为空则会返回0和nil),返回的类型有.NumFiled()可以获取对应类中字段的数量,.Field(index)可以获取对应下标上的字段类型,对返回值再.Interface()就可以得到对应字段的值.NumMethod()可以获取方法的数量,.Method(index)可以获取对应的方法,其中有属性Name(方法名)和Type(属于哪个类)

#### 标签

对于类体的属性,可以在声明后加'k : v'的方式给属性加tag,对属性做一些说明,反射同样也可以获取这些tag,调用.Tag.Get(key)就可以获取对应字段上的tag的对应Value

  

#### 错误处理

GO的异常可以作为返回值在错误信息中返回,在主函数中就可以通过对err的处理进行异常处理,但是这里的异常更像是自己throw的,而不是系统异常

```Go
package main

import (
    "errors"
    "fmt"
)

type user struct {
    name     string
    password string
}

func findUser(users []user, name string) (v *user, err error) { //多返回值,多一个error类型
    for _, u := range users {
       if u.name == name {
          return &u, nil //nil类似null
       }
    }
    return nil, errors.New("not found") //返回错误,使用errors.NEW(msg Stirng)创建
}

func main() {
    u, err := findUser([]user{{"wang", "1024"}}, "wang") //接收异常
    if err != nil { //处理异常
       fmt.Println(err)
       return
    }
    fmt.Println(u.name) // wang

    if u, err := findUser([]user{{"wang", "1024"}}, "li"); err != nil { //先声明变量,再进行判断
       fmt.Println(err) // not found
       return
    } else {
       fmt.Println(u.name)
    }
}
```

对于需要自定义简单错误的情况,可以使用errors.New(msg stirng)的方法创建一个错误(fmt.Errorf可以格式化错误信息)

Go还可以对error进行嵌套,叫做wrap和Unwrap,可以形成错误的跟踪连,格式化时使用%w做占位符

errors.Is(err,<要检验的错误类型>)可以校验错误链中是否包含要检验的错误类型,errors.As(err,<要校验的错误类型>)可以从链路中获取对应类型的错误(如果有的话)并返回

Panic()可以抛出panic,出现panic表示程序出现会导致程序崩溃的错误,但是这种方式应该尽量避免使用

![[Pasted image 20250715130337.png]]

要处理panic可以使用recover(),这个方法只能在defer中使用,可以获取出现的错误,可以在defer中打印日志,嵌套的时候就不会生效了,并且只能对当前的协程生效

## 常用库

#### rand

math/rand

其中有Intn(n)可以返回一个范围内的整数(0-n,左闭右开)

#### strings

```Go
package main

import (
    "fmt"
    "strings" //Strings依赖
)

func main() {
    a := "hello"
    fmt.Println(strings.Contains(a, "ll"))                // true 包含
    fmt.Println(strings.Count(a, "l"))                    // 2 出现次数
    fmt.Println(strings.HasPrefix(a, "he"))               // true 前缀校验
    fmt.Println(strings.HasSuffix(a, "llo"))              // true 后缀校验
    fmt.Println(strings.Index(a, "ll"))                   // 2 查找子串下标
    fmt.Println(strings.Join([]string{"he", "llo"}, "-")) // he-llo 字符串数组拼接
    fmt.Println(strings.Repeat(a, 2))                     // hellohello 重复
    fmt.Println(strings.Replace(a, "e", "E", -1))         // hEllo 替换指定字符
    fmt.Println(strings.Split("a-b-c", "-"))              // [a b c] 分隔字符串
    fmt.Println(strings.ToLower(a))                       // hello 转小写
    fmt.Println(strings.ToUpper(a))                       // HELLO 转大写
    fmt.Println(len(a))                                   // 5 取长度
    b := "你好"
    fmt.Println(len(b)) // 6 一个中文对应多个字符
}
```

#### fmt

```Go
package main

import "fmt"

type point struct {
    x, y int
}

func main() {
    s := "hello"
    n := 123
    p := point{1, 2}
    fmt.Println(s, n) // hello 123 
    fmt.Println(p)    // {1 2}

    fmt.Printf("s=%v\n", s)  // s=hello 类似C的printf,格式化输出
    fmt.Printf("n=%v\n", n)  // n=123 %v可以代指任意类型的变量
    fmt.Printf("p=%v\n", p)  // p={1 2}
    fmt.Printf("p=%+v\n", p) // p={x:1 y:2} 详细信息(结构体属性名)
    fmt.Printf("p=%#v\n", p) // p=main.point{x:1, y:2}  更详细(结构体名和所在包)
 
    f := 3.141592653
    fmt.Println(f)          // 3.141592653
    fmt.Printf("%.2f\n", f) // 3.14
}
```

占位符:

- %T可以输出变量类型
    
- %b可以输出bit位数据(二进制形式输出)
    

#### encoding/json 序列化

```Go
package main

import (
    "encoding/json"
    "fmt"
)

type userInfo struct {
    Name  string //要使用自动序列化,结构体的属性名需要为大写开头
    Age   int `json:"age"` //可以使用这种方式指定序列化后的格式
    Hobby []string
}

func main() {
    a := userInfo{Name: "wang", Age: 18, Hobby: []string{"Golang", "TypeScript"}}
    buf, err := json.Marshal(a) //序列化为byte数组
    if err != nil {
       panic(err)
    }
    fmt.Println(buf)         // [123 34 78 97...] 直接输出会输出16进制数,需要强制转化为字符串
    fmt.Println(string(buf)) // {"Name":"wang","age":18,"Hobby":["Golang","TypeScript"]}

    buf, err = json.MarshalIndent(a, "", "\t") //相较于直接序列化,他会加一些格式
    if err != nil {            //分别为用什么结尾,用什么开头
       panic(err)
    }
    fmt.Println(string(buf))

    var b userInfo
    err = json.Unmarshal(buf, &b) //反序列化,传入的是指针
    if err != nil {
       panic(err)
    }
    fmt.Printf("%#v\n", b) // main.userInfo{Name:"wang", Age:18, Hobby:[]string{"Golang", "TypeScript"}}
}
```

#### time 时间处理

```Go
package main

import (
    "fmt"
    "time"
)

func main() {
    now := time.Now() //获取当前时间
    fmt.Println(now) // 2022-03-27 18:04:59.433297 +0800 CST m=+0.000087933
    t := time.Date(2022, 3, 27, 1, 25, 36, 0, time.UTC) //构造一个Time对象.其中有方法
    t2 := time.Date(2022, 3, 27, 2, 30, 36, 0, time.UTC)
    fmt.Println(t)                                                  // 2022-03-27 01:25:36 +0000 UTC
    fmt.Println(t.Year(), t.Month(), t.Day(), t.Hour(), t.Minute()) // 2022 March 27 1 25
    fmt.Println(t.Format("2006-01-02 15:04:05"))    //格式化,括号里的只能写这个时间,是写死在time包里的              // 2022-03-27 01:25:36
    diff := t2.Sub(t) //两个时间相减,返回Duration对象表示时间段
    fmt.Println(diff)                           // 1h5m0s 时间段经过的时间
    fmt.Println(diff.Minutes(), diff.Seconds()) // 65 3900 
    t3, err := time.Parse("2006-01-02 15:04:05", "2022-03-27 01:25:36") //字符串转为Time对象
    if err != nil {
       panic(err)
    }
    fmt.Println(t3 == t)    // true
    fmt.Println(now.Unix()) // 1648738080 获取时间戳
}
```

#### strconv 数字与字符串转化

```Go
package main

import (
    "fmt"
    "strconv"
)

func main() {
    f, _ := strconv.ParseFloat("1.234", 64) //精度
    fmt.Println(f) // 1.234

    n, _ := strconv.ParseInt("111", 10, 64) //前边那个数的进制,传0自动推测
    fmt.Println(n) // 111

    n, _ = strconv.ParseInt("0x1000", 0, 64)
    fmt.Println(n) // 4096

    n2, _ := strconv.Atoi("123") //十进制字符串转数字,itoa可以转字符串
    fmt.Println(n2) // 123

    n2, err := strconv.Atoi("AAA") //会报错
    fmt.Println(n2, err) // 0 strconv.Atoi: parsing "AAA": invalid syntax
}
```

#### os/exec 获取进程

```Go
package main

import (
    "fmt"
    "os"
    "os/exec"
)

func main() {
    // go run example/20-env/main.go a b c d
    fmt.Println(os.Args)           // [/var/folders/8p/n34xxfnx38dg8bv_x8l62t_m0000gn/T/go-build3406981276/b001/exe/main a b c d] cmd执行文件的目录和参数
    fmt.Println(os.Getenv("PATH")) // /usr/local/go/bin...
    fmt.Println(os.Setenv("AA", "BB")) //设置和获取环境变量

    buf, err := exec.Command("grep", "127.0.0.1", "/etc/hosts").CombinedOutput() //并发执行cmd并回调
    if err != nil {
       panic(err)
    }
    fmt.Println(string(buf)) // 127.0.0.1       localhost
}
```

## 并发
![[Pasted image 20250715130508.png]]
![[Pasted image 20250715130513.png]]
![[Pasted image 20250715130526.png]]
![[Pasted image 20250715130534.png]]
![[Pasted image 20250715130539.png]]
![[Pasted image 20250715130544.png]]

一个线程可以分为由cpu控制的内核空间和用户控制的用户空间,所以go把这两部分分开了(CPU只能感知线程),通过一个协程调度器,让一个线程可以控制多个协程,减少了上下文切换的成本

#### 复用线程

**Work stealing机制:**

即当多个本地队列中存在空闲的队列,此时这个空闲的处理器会尝试从别的本地队列中偷取协程,如果也没有就会尝试从全局队列获取

**Hand off机制:**

当一个处理器中正在处理的协程处于阻塞状态时并且本地队列中还有协程在等待,那么这个处理器就会直接将这个阻塞的协程交给线程处理,然后自己唤醒一个新的线程,并将自己绑定到新的线程上,处理等待的协程

#### **利用并行**

**GOMAXPROCS:**

这是一个全局变量,用于控制处理器的最大数量,默认为CPU数量除以2

#### 抢占

对于一个协程,可以执行的时间片为10ms,之后就会切换别的协程执行(没有优先级)

协程:用户态，轻量级线程，栈 KB 级别

线程:内核态，线程跑多个协程，栈 MB 级别(进程为GB级别的)

协程可以认为是轻量化的线程,一个线程上可以并发多个协程

GO要开启协程只需要在对应函数前加上一个go关键字即可

在函数的子函数中如果要退出当前线程,return是不行的,可以调用runtime.GOexit()来结束当前协程

#### Channel

要想获取别的协程的返回值,就需要使用Channel
![[Pasted image 20250715130554.png]]

GO提倡通过通信共享内存而不是通过共享内存而实现通信,即通过通道传输数据来达到共享一块内存的效果,而不是共享一块内存后在这块内存中进行通讯(仍然保留),因为这样会存在共享资源同时被修改的问题.

如果在其他线程中要从一个协程的Channel中获取值但是此时对应的协程还没有返回,要读的线程就会被阻塞

声明:

chan T: 泛型为T的双向通道(可读可写)

chan<- T: 泛型为T的单向发送通达(只写)

<-chan T: 泛型为T的单向接收通道(只读)

创建(初始化):

Channel的创建:`make(chan 元素类型，[缓冲大小])`

无缓冲通道(同步通道) make(chan int)

有缓冲通道 make(chan int,2)
![[Pasted image 20250715130605.png]]

通道的缓存就类似消息队列的容量,可以让生产者和消费者之间的通讯不同步

![[Pasted image 20250715130614.png]]

对于无缓存通道,如果协程向里边放入了数据但是没有其他协程等待获取数据,那么这个放入数据的协程就会被阻塞直到有协程取出数据,并且一般取出数据的协程会先继续运行

![[Pasted image 20250715130624.png]]

而对于有缓存的管道,就只有在管道缓存已满或者管道中无数据时才会被阻塞

取数据时还可以用两个变量接收,第二个代表当前管道是否已经开启
![[Pasted image 20250715130636.png]]

对于Channel使用range后,如果Channel没有关闭,那么range就会阻塞直到读到数据.

![[Pasted image 20250715130645.png]]

Go仍然保留了lock的概念,可以用sync.Mutex来声明锁,使用.Lock()和.UnLock()来实现上锁和解锁

![[Pasted image 20250715130654.png]]

对于线程的同步,可以使用WaitGroup中的三个方法来实现

![[Pasted image 20250715130700.png]]

它可以起到计数器的功能,开启协程+1;执行结束-1;主协程阻塞直到计数器为0

使用sleep时不确定线程的确切执行时间,而使用waitGroup就可以阻塞到所有协程结束,避免出现存在某一线程未处理结束而导致后续业务出现问题

![[Pasted image 20250715130745.png]]
![[Pasted image 20250715130749.png]]


#### Select

为了防止操作Channel时发生阻塞,可以使用Select语块来控制,不使用Select去控制两个Channel时如果其中有一个状态不正常,就会阻塞在哪里而不能获取另一个Channel的状态,而使用Select就可以正常监控多个Channel的状态

![[Pasted image 20250715130757.png]]

#### WaitGroup

`WatiGroup`是`sync` package 中的一个`struct`, 用来收集需要等待执行完成的`goroutine`。

三个方法:

`Add()`: 用来设置或添加要等待完成的 goroutine 数量 例子:

`Add(2)`或两次调用`Add(1)`都会设置等待计数器的值为 2, 即为要等待 2 个 goroutine 完成。

自然, `Done()`则表示需要等待的 goroutine 在真正完成之前, 应调用该方法来人为表示 goroutine 完成了, 重要地是: 该方法会对等待计数器减 1。

最后, `Wait()`则意味着在等待计数器减为 0 之前, `Wait()`会一直阻塞当前的 goroutine

```Go
func TestTaskControdl(t *testing.T) {
 taskNum := 5
   
 wg := sync.WaitGroup{}
 wg.Add(taskNum)
 
   
 for i:=0; i< taskNum; i++{
 go func(i int){
 fmt.Println("info", i)
 wg.Done()
 }(i)
   }
   
 wg.Wait()
   }
-----------------------------------
info 4
info 3
info 1
info 0
info 2
```

#### Context

Context 是用来让多级 Goroutine 实现通信的一种工具, 并发安全。

多级嵌套: 父任务停止, 子任务停止、 控制停止顺序(如 ABCDEFG, 可以让顺序为 EFG, BC...)

`context.Context` 该接口定义了四个需要实现的方法

源码

```Go
type Context interface {

 Deadline() (deadline time.Time, ok bool) //返回context被取消的时间(完成工作的截止日期),没有设置则返回ok为false

 Done() <-chan struct{} //确认上下文是否完成,当当前context关闭后则会反会对应0值

 Err() error

 Value(key any) any //通过key获取value
}
```

创建空的上下文

```Go
func Background() Context { //上下文的默认起点(用于最高等级)
 return backgroundCtx{}
}

// TODO returns a non-nil, empty [Context]. Code should use context.TODO when
// it's unclear which Context to use or it is not yet available (because the
// surrounding function has not yet been extended to accept a Context
// parameter).
func TODO() Context { //创建临时上下文
 return todoCtx{}
}
```

> type backgroundCtx struct{ emptyCtx }
> 
> type todoCtx struct{ emptyCtx }
> 
> type emptyCtx struct{}

通过`context.WithTimeout()` 设置上下文的超时时间, 在到达超时之后自动结束。

而`WithDeadline()`则将设置上下文需要完成的截止时间。

```Go
ctx, cancel := context.WithTimeout(context.Background(), 10 * time.Millisecond),
```

`WithValue()`函数: 使Context携带key-value

```Go
func TestContext(t *testing.T){
 a := context.Background()
 b := context.WithValue(a, "k1", "val1")
 c := context.WithValue(b, "key1", "val1")
 d := context.WithValue(c, "key2", "val2")
 e := context.WithValue(d, "key3", "val3")
 f := context.WithValue(e, "key3", "val4") //并不是只会保留最后一个,而是类似一个栈的结构
 fmt.Printf(" %s\n", f.Value("key3"))
  // 输出: val4
}
```

## 依赖管理

Go在运行时的顺序如下:

![[Pasted image 20250715130815.png]]

其中init()函数会在main()之前被执行,可以对包内的内容进行初始化

对于函数名,大写字母开头的可以被外部访问(public),小写字母开头的只能在内部访问(private)

import中引入的依赖可以起别名,方法为import 别名 fmt,其中别名可以为下划线,代表为匿名类,即使不使用其中的方法也不会报错因为这样没有依赖名无法调用其中的方法,适用与需要单独执行其中的init()的情况,还可以为. ,代表将该依赖中的方法直接引入到当前代码(类似C的宏定义),这样在调用函数时就不用依赖名.方法()调用了,可以直接方法()使用(不推荐,如果两个依赖中存在重名方法就会冲突)

Go的依赖管理有三种方案,分别是GOPATH,Go Vendor和Go Module(最常用)

#### GOPATH

GOPATH是go的一个环境变量,代表go的工作区,该目录下主要保存如下三种文件

![[Pasted image 20250715130825.png]]

GOPATH管理依赖时,项目代码直接依赖 src下的代码,使用go get 下载最新版本的包到 src 目录下,即所有依赖代码都会被放在src中,因此无法实现多版本控制

#### Go Vendor

![[Pasted image 20250715130834.png]]

Go vendor相较gopath只是多了一个文件夹,它会为每个项目引入一份依赖的副本,这样解决了多项目引用同一份依赖的问题,但是还是无法控制依赖的版本

#### Go module
![[Pasted image 20250715130841.png]]

通过 go.mod 文件管理依赖包版本

通过 go get/go mod 指令工具管理依赖包

**依赖管理三要素:**

1. 配置文件，描述依赖 go.mod
    
2. 中心仓库管理依赖库 Proxy
    
3. 本地工具 go get/mod
    

![[Pasted image 20250715130850.png]]

依赖名中还包含一些标识,如indirect表示该依赖为间接依赖,即依赖的依赖,还有incompatible,代表当前依赖的依赖名中没有/vn标识,这个标识是用来区别不同版本的,不同的v可能会存在不兼容的问题

当当前项目间接依赖了一个依赖的不同小版本,在编译时会自动选择依赖了的小版本中最新的兼容当前项目的版本

![[Pasted image 20250715130858.png]]

![[Pasted image 20250715130922.png]]

#### 特殊符号

(1) 别名v2:

此处的v2相当于是上述导入包的一个别名，我们可以直接使用 v2.WxLogin来调用包中的接口或方法。

  

(2) 下划线_:

此处的下划线在最初的时候，我也十分好奇究竟是什么作用，而且发现导入的包未被使用。

最终查阅得到：

  

在导入路径前加入下划线表示只执行该库的 init 函数而不对其它导出对象进行真正地导入。因为 Go 语言的数据库驱动都会在 init 函数中注册自己，所以我们只需要进行上述操作即可；否则的话，Go 语言的编译器会提示导入了包却没有使用的错误。

  

(3) 点.:

此处的.，含义就是这个包导入之后在你调用这个包的函数时，你可以省略前缀的包名，也就是前面你调用的fmt.Println(“hello world”) 可以省略的写成Println(“hello world”)

## 测试

![[Pasted image 20250715130930.png]]

#### 单元测试

1. 规则
    

![[Pasted image 20250715130937.png]]

其中TestMain会在运行时测试所有单测

2. Mock
    

常用的Mock工具monkey : https://github.com/bouk/monkey

![[Pasted image 20250715130950.png]]

![[Pasted image 20250715131013.png]]

调用其中的patch对某一函数或方法进行打桩,这样在调用时就不会再对本地的一些数据(如数据库)的依赖了,之后再调用unpatch进行卸载

#### 基准测试

![[Pasted image 20250715131021.png]]

基准测试以Benchmark开头,其中.ResetTimer可以用于重置当前测试花费的时间(用于排除测试花费的时间中的一些额外操作),.RunParallel可以让测试并行进行(但是当测试代码中存在不允许并行的方法如rand()时就会影响测试效率,这时可以将rand()替换为fastrand(https://github.com/bytedance/gopkg)来获取随机数)

## 性能优化

#### 性能测试

GO提供了基准性能测试工具benchmark,可以在对应的方法名前加上Benchmark,调用

`go test -bench=. -benchmem`来测试

![[Pasted image 20250715131048.png]]

#### Slice

Slice预先指定大小可以减少扩容造成的性能消耗,并且在截取切片的一部分时(即其他部分不再被使用),不应该直接通过下标获取,而是使用copy将对应的区间复制到新的切片上再使用,因为前者会存在Slice上仍存在元素被引用而无法释放整个Slice导致浪费空间

#### String

GO中也有类似java的StringBuilder和StringBuffer,可以通过strings.Builder来获取,其中有WriteString方法可以向尾部添加字符串,.String方法可以将Builder转为Stirng,Builder的性能略优于buffer,他们的底层都是维护了一个byte[],支持预先分配大小,使用.Grow()即可

#### 空结构体

GO中的空结构体即使实例化也不占据空间,可以当做一个占位符,比如使用map时将其中一个类型指定为空结构体,就可以实现类似set的结构

#### atomic包

atomic的作用类似于锁,但是他只能维护变量,不能维护一段代码,但是当只需要维护一个变量时就可以用atomic,因为他是通过硬件实现的,而锁是通过操作系统实现的,所以atomic性能更好

## Pprof

pprof 是用于可视化和分析性能分析数据的工具

![[Pasted image 20250715131100.png]]

`Go Tool Pprof“``http://localhost:6060/debug/pprof/profile?seconds=10``”`可以获取10秒内的数据

![[Pasted image 20250715131106.png]]

- Flat == Cum，函数中没有调用其他函数
    
- Flat == 0,函数中只有其他函数的调用
    

![[Pasted image 20250715131114.png]]

![[Pasted image 20250715131122.png]]
![[Pasted image 20250715131134.png]]

`go tool pprof -http=:8082 "``http://localhost:6060/debug/pprof/heap``"`

可以在浏览器可视化