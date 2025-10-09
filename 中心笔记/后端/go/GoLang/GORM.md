ORM(Object Relational Mapping)

**优点:**

提高开发效率

**缺点:**

牺牲执行性能

牺牲灵活性

弱化SQL能力

## CURD(go原生)

## 增删改.Exec

除了查询之外的语句都可以使用.Exec()方法执行

```Go
func main() {
  // 连接数据库
  db, err := sql.Open("mysql", "root:root@tcp(127.0.0.1:3306)/gorm_new_db")
  if err != nil {
    log.Fatalf("数据库连接失败 %s", err)
  }
  defer db.Close()
  _, err2 := db.Exec("CREATE TABLE users1(id INT NOT NULL , name VARCHAR(20), PRIMARY KEY(ID));")
  if err2 != nil {
    log.Fatal(err2)
  }

  fmt.Println("successfully create table")

}
```

#### 查.Query

```Go
  res, err := db.Query("select id, name from users")
  for res.Next() {
    var id int
    var name string
    // 这里scan的字段要对上
    err = res.Scan(&id, &name)
    fmt.Println(id, name, err)
  }
```

也可以使用QueryRow函数，查一行

`err = db.QueryRow("select id, name from users").Scan(&id, &name)`

  

## 使用

#### **安装**

```Go
go get gorm.io/driver/mysql //安装mysql的驱动
go get gorm.io/gorm //安装gorm
```

#### 连接数据库

连接对应的数据库就引入对应的驱动

```Go
import "gorm.io/driver/mysql"
```

连接:

```Go
package main

import (
    "fmt"
    "gorm.io/driver/mysql"
    "gorm.io/gorm"
)

func main() {
    username := "root"       //账号
    password := "123456vbnm" //密码
    host := "127.0.0.1"      //数据库地址，可以是Ip或者域名
    port := 3306             //数据库端口
    Dbname := "test"         //数据库名
    timeout := "10s"         //连接超时，10秒

    // root:root@tcp(127.0.0.1:3306)/gorm?
    dsn := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?charset=utf8mb4&parseTime=True&loc=Local&timeout=%s", username, password, host, port, Dbname, timeout)
    //连接MYSQL, 获得DB类型实例，用于后面的数据库读写操作。
    db, err := gorm.Open(mysql.Open(dsn))
    if err != nil {
       panic("连接数据库失败, error=" + err.Error())
    }
    // 连接成功
    fmt.Println(db)
}
```

#### 常用操作

1. **建表**
    

模型是标准的 struct，由 Go 的基本数据类型、实现了 `Scanner` 和 `Valuer` 接口的自定义类型及其指针或别名组成(字段名要写为大写开头,否则gorm读不到),一般表的结构体会使用Model结尾来表示他是表的结构体

```Go
type Student struct {
  ID    uint // 默认使用ID作为主键
  Name  string
  Email *string // 使用指针是为了存空值
}
```

使用DB.AutoMigrate(&<模版>{}...(可以为多个))即可创建表,`AutoMigrate`的逻辑是只新增，不删除，不修改（大小会修改）,比如修改模版中的字段名后,他不会把原来表中的对应字段删除或者修改,而是新增一列

但是这样生成的表中的字段属性的容量会很大,我们可以使用一些tag进行修改

```Go
Name  string  `gorm:"type:varchar(12)"`
Name  string  `gorm:"size:2"`
```

> `type` 定义字段类型
> 
> `size` 定义字段大小
> 
> `column` 自定义列名
> 
> `primaryKey` 将列定义为主键
> 
> `unique` 将列定义为唯一键
> 
> `default` 定义列的默认值
> 
> `not null` 不可为空
> 
> `embedded` 嵌套字段
> 
> `embeddedPrefix` 嵌套字段前缀
> 
> `comment` 注释
> 
> 多个标签之前用 `;` 连接

2. 增删改查
    

增:

`.Create(&student)` 增(接收的是一个指针，而不是值,并且这个方法会自动为当前对象补全在数据库中生成的值,如id会自动拼接回对象中)(也可以传一个数组,会一次性都加进去)

插入操作钩子写法

```Go
func (u *UserModel) BeforeCreate(tx *gorm.DB) error {
  fmt.Println("创建的钩子函数")
  u.Name = "枫枫"
  return nil
}
```

查:

```Go
DB.Take(&student)  //可以在结构体中预置主键值,这样也可以查出来
// SELECT * FROM `students` LIMIT 1
DB.Take(&student, 2) //主键查询,可以是字符串,也可以是数字
DB.Take(&student, "name = ?", "机器人27号") //其他字段查询使用？作为占位符，将查询的内容放入?,原理就是将参数全部转义(所有内容作为一个整体),这样可以有效防止sql注入
DB.First(&student) 
// SELECT * FROM `students` ORDER BY `students`.`id` LIMIT 1
DB.Last(&student)  //take,last和first都只会查主键
// SELECT * FROM `students` ORDER BY `students`.`id` DESC LIMIT 1
DB.Find(&studentList) //传入的是一个切片的指针,这样就可以查询多条记录
DB.Find(&studentList, []int{1, 3, 5, 7})
DB.Find(&studentList, 1, 3, 5, 7) //按主键查询(这两种写法都可以)
DB.Find(&studentList, "name in ?", []string{"枫枫", "zhangsan"}) //按字段值查询
count := DB.Find(&studentList).RowsAffected //获取查询的记录数
err := DB.Find(&studentList).Error //是否查询失败(查询为空，查询条件错误，sql语法错误)
```

.where()相当于sql中的where语句

.Take()更倾向于查询一条数据

.Find()相当于将查询结果注入传入的指针中,倾向于查多条

`DB.Where("name <> ?", "枫枫").Find(&users)`

where()直接传入一个结构体指针也可以查询,0值不会被查询

`DB.Where(&Student{Name: "李元芳", Age: 0}).Find(&users)`

也可以传入一个map,这样不会过滤0值

`DB.Where(map[string]any{"name": "李元芳", "age": 0}).Find(&users)`

.Not()相当于对Where()中的条件进行一个否定

`DB.Not("age > 23").Find(&users)`

.Or()相当于在Where里直接写or

`DB.Or("gender = ?", false).Or(" email like ?", "%``@qq.com``").Find(&users)`

.Select()相当于指定sql语句中的查询字段部分

`DB.Select("name", "age").Find(&users)` (没有被查询的就会被赋0值)

.Scan()可以把查询到的结果复制到另一个结构体中,也可以代替Find()(区别是Scan是根据column列名进行扫描的),即只需要特定字段时使用Scan

`DB.Select("name", "age").Find(&students).Scan(&users)`

.Model()和.Table()可以指定查询那个表

.Order()可以对查询结果进行排列

`DB.Order("age desc").Find(&users)`

.Distinct()可以对指定的字段进行去重

`DB.Table("students").Select("age").Distinct("age").Scan(&ageList)`

**分页查询:**

```Go
var users []Student
// 一页两条，第1页
DB.Limit(2).Offset(0).Find(&users)
fmt.Println(users)
// 第2页
DB.Limit(2).Offset(2).Find(&users)
fmt.Println(users)
// 第3页
DB.Limit(2).Offset(4).Find(&users)
fmt.Println(users)
```

.Group()可以进行分组查询

`DB.Table("students").Select("count(id)").Group("gender").Scan(&ageList)`

.Raw()内可以执行原sql语句(要加单引号)

DB.Raw('`SELECT count(id), gender, group_concat(name) FROM students GROUP BY gender`').Scan(&agge)

**子查询:**

`DB.Model(Student{}).Where("age > (?)", DB.Model(Student{}).Select("avg(age)")).Find(&users)`

gorm还支持对查询语句中的参数进行命名,再使用sql.Named或map对这些变量进行赋值

```Go
DB.Where("name = @name and age = @age", sql.Named("name", "枫枫"), sql.Named("age", 23)).Find(&users)
DB.Where("name = @name and age = @age", map[string]any{"name": "枫枫", "age": 23}).Find(&users)
```

Gorm也支持Mybatis类似的函数式sql,但是最后的Find赋值还是要写

```Go
func Age23(db *gorm.DB) *gorm.DB { //格式
  return db.Where("age > ?", 23)
}

func main(){
  var users []Student
  DB.Scopes(Age23).Find(&users)
  fmt.Println(users)
}
```

改:

有很多方法，Save、Update、UpdateColumn、Updates

不同的方法有不同的区别，如下：

1. Save，有主键记录就是更新，并且可以更新零值，否则就是创建
    
2. Update，可以更新零值，必须要有条件
    
3. UpdateColumn，可以更新零值，不会走更新的Hook
    
4. Updates，如果是结构体，则更新非零值，map可以更新零值
    

```Go
DB.Save(&student) //需要预置主键,否则会变为新增
// UPDATE `students` SET `name`='枫枫',`age`=23,`gender`=true,`email`='xxx@qq.com' WHERE `id` = 1
DB.Select("age").Save(&student) //只修改某一个字段,同样需要传入主键
DB.Find(&studentList, "age = ?", 21).Update("email", "is21@qq.com")  //传入的是一个切片的指针
DB.Model(&Student{}).Where("age = ?", 21).Update("email", "is21@qq.com") //批量更新,两种方法都一样
```

> 更新是默认不会更新属性值为0的字段,如果需要修改可以使用.Select指定
> 
> ```Go
> email := "xxx@qq.com"
> DB.Model(&Student{}).Where("age = ?", 21).Updates(Student{
>   Email:  &email,
>   Gender: false,  // 这个不会更新
> })
> // UPDATE `students` SET `email`='xxx@qq.com' WHERE age = 21
> ```
> 
> ```Go
> email := "xxx1@qq.com"
> DB.Model(&Student{}).Where("age = ?", 21).Select("gender", "email").Updates(Student{
>   Email:  &email,
>   Gender: false,
> })
> // UPDATE `students` SET `gender`=false,`email`='xxx1@qq.com' WHERE age = 21
> ```
> 
> 或者使用map来修改值为0的属性
> 
> ```Go
> DB.Model(&Student{}).Where("age = ?", 21).Updates(map[string]any{
>   "email":  &email,
>   "gender": false,
> })
> ```

.Select() 可以选定字段

.Omit() 可以忽略字段

.Expr() 通常用于获取原字段的数据,例如年龄加一

```Go
DB.Model(&UserModel{}).
  Where("id = ?", 1).
  UpdateColumn("age", gorm.Expr("age + 1"))
```

修改操作钩子写法

```Go
func (u *UserModel) BeforeUpdate(tx *gorm.DB) (err error) {
  fmt.Println("更新的钩子函数")
  return nil
}
```

删:

```Go
// student 的 ID 是 `10`
db.Delete(&student) //单个删除
// DELETE from students where id = 10;

db.Delete(&Student{}, []int{1,2,3}) //同时删除多个
// 查询到的切片列表
db.Delete(&studentList)
```

删除钩子

```Go
func (u *UserModel) BeforeDelete(tx *gorm.DB) (err error) {
    fmt.Println("删除钩子")
    return
}
```

#### 逻辑删除(软删除)

如果你的模型包含了 `gorm.DeletedAt`字段（该字段也被包含在`gorm.Model`中），那么该模型将会自动获得软删除的能力

当调用`Delete`时，GORM并不会从数据库中删除该记录，而是将该记录的`DeleteAt`设置为当前时间，而后的一般查询方法将无法查找到此条记录。

查找被删除的记录

```TypeScript
DB.Unscoped().Find(&users)
```

永久删除

```TypeScript
db.Unscoped().Delete(&user)
```

## 约束
>不写注解默认取父表主键作为子表的关联字段,取`<类名>+<关联的字段名>`拼接作为子表的外键
>父表中foreignKey注解可以指定子表中的特定属性(如Number)为外键
>父表中references注解可以指定父表中作为主键的字段
1. 一对一
>父表中添加子表的对象属性,子表中添加外键字段
```go
// 声明
// User 有一张 CreditCard，UserID 是外键
type User struct {
  gorm.Model
  CreditCard CreditCard
}

type CreditCard struct {
  gorm.Model
  Number string
  UserID uint
}
// 重写外键
type User struct {
  gorm.Model
  CreditCard CreditCard `gorm:"foreignKey:UserName"` // 使用 UserName 作为外键
}

type CreditCard struct {
  gorm.Model
  Number   string
  UserName string
}
// 重写引用
type User struct {
  gorm.Model
  CreditCard CreditCard `gorm:"foreignKey:UserName"` // 使用 UserName 作为外键
}

type CreditCard struct {
  gorm.Model
  Number   string
  UserName string
}
```
2. 一对多
>父表添加子表对象数组,子表添加外键字段
```go
// 声明
// User 有多张 CreditCard，UserID 是外键  
type User struct {  
  gorm.Model  
  CreditCards []CreditCard  
}  
  
type CreditCard struct {  
  gorm.Model  
  Number string  
  UserID uint  
}
// 重写外键
type User struct {
  gorm.Model
  CreditCards []CreditCard `gorm:"foreignKey:UserRefer"`
}

type CreditCard struct {
  gorm.Model
  Number    string
  UserRefer uint
}
// 重写引用
type User struct {
  gorm.Model
  MemberNumber string
  CreditCards  []CreditCard `gorm:"foreignKey:UserNumber;references:MemberNumber"`
}

type CreditCard struct {
  gorm.Model
  Number     string
  UserNumber string
}
```
3. 多对多
>两张表中都添加对方的对象数组,中间表创建联合主键
>many2many是必须的
>不写其他注解默认关联两张表的主键
```go
// 声明
// User 拥有并属于多种 language，`user_languages` 是连接表
type User struct {
  gorm.Model
  Languages []Language `gorm:"many2many:user_languages;"`
}

type Language struct {
  gorm.Model
  Name string
}
// 反向引用
// User 拥有并属于多种 language，`user_languages` 是连接表
type User struct {
  gorm.Model
  Languages []*Language `gorm:"many2many:user_languages;"`
}

type Language struct {
  gorm.Model
  Name string
  Users []*User `gorm:"many2many:user_languages;"`
}
// 重写外键
type User struct {
    gorm.Model
    Profiles []Profile `gorm:"many2many:user_profiles;foreignKey:Refer;joinForeignKey:UserReferID;References:UserRefer;joinReferences:ProfileRefer"`
    Refer    uint      `gorm:"index:,unique"`
}

type Profile struct {
    gorm.Model
    Name      string
    UserRefer uint `gorm:"index:,unique"`
}

// 会创建连接表：user_profiles
//   foreign key: user_refer_id, reference: users.refer
//   foreign key: profile_refer, reference: profiles.user_refer

// 自定义连接表
type Person struct {
  ID        int
  Name      string
  Addresses []Address `gorm:"many2many:person_addresses;"`
}

type Address struct {
  ID   uint
  Name string
}

type PersonAddress struct {
  PersonID  int `gorm:"primaryKey"`
  AddressID int `gorm:"primaryKey"`
  CreatedAt time.Time
  DeletedAt gorm.DeletedAt
}

func (PersonAddress) BeforeCreate(db *gorm.DB) error {
  // ...
}

// 修改 Person 的 Addresses 字段的连接表为 PersonAddress
// PersonAddress 必须定义好所需的外键，否则会报错
err := db.SetupJoinTable(&Person{}, "Addresses", &PersonAddress{})
```
#### 一对多

如果一个结构体中有另一个结构体,在使用GORM时就会为这两个结构体对应的表添加外键关系

```Go
type User struct {
  ID       uint      `gorm:"size:4"`
  Name     string    `gorm:"size:8"`
  Articles []Article // 用户拥有的文章列表(表名+s)
}

type Article struct {
  ID     uint   `gorm:"size:4"`
  Title  string `gorm:"size:16"`  
  UserID uint   `gorm:"size:4"`// 属于   这里的类型要和引用的外键类型一致，包括大小(名字也是固定的表名+ID)
  User   User   // 属于
}
```

对应的表结构

![[Pasted image 20250715131206.png]]
![[Pasted image 20250715131213.png]]

GORM中也可以使用tag表示外键

```Go
type User struct {
  ID       uint      `gorm:"size:4"`
  Name     string    `gorm:"size:8"`
  Articles []Article `gorm:"foreignKey:UID;references:ID"` // 用户拥有的文章列表
}

type Article struct {
  ID    uint   `gorm:"size:4"`
  Title string `gorm:"size:16"`
  UID   uint   // 属于
  User  User   `gorm:"foreignKey:UID"` // 属于(这两个位置都是从表中的外键名,这样就不用按格式起名了)
}
```

这样配置后,就可以进行一对多的添加了

```Go
a1 := Article{Title: "python"}
a2 := Article{Title: "golang"}
user := User{Name: "枫枫", Articles: []Article{a1, a2}}
DB.Create(&user) //gorm自动创建了两篇文章，以及创建了一个用户，还将他们的关系给关联上了
```

或者在从表的对象中直接填入userid或者user结构体也可以

要给已经存在的记录添加约束关系,需要使用更新的句式

```Go
var user User
DB.Take(&user, 2)

var article Article
DB.Take(&article, 5)

user.Articles = []Article{article}
DB.Save(&user)
```

或者使用.Association().Append()

`DB.Model(&user(要绑定的主表记录)).Association("Articles").Append(&article(要绑定的从表记录))`

这里的Articles是结构体中声明的变量名,反过来添加同理

#### 预加载

有外键字段后从表在查询时如果要把主表外键对应上就需要预加载主表的内容(本质上是先去查了对应主表中的属性,查完从表后再拼接起来,进行了两次查询)

`DB.Preload("Articles").Take(&user, 1)` ("Articles"还支持嵌套,即可以"Articles.User.Array"可以反复拼接)

.Preload()中可以带上条件,如

`DB.Preload("Articles", "id = ?", 1).Take(&user, 1)`

或者使用函数

```Go
var user User
DB.Preload("Articles", func(db *gorm.DB) *gorm.DB {
  return db.Where("id in ?", []int{1, 2})
}).Take(&user, 1)
fmt.Println(user)
```

#### 删除外键关系

```Go
var user User
DB.Preload("Articles").Take(&user, 2)
DB.Model(&user).Association("Articles").Delete(&user.Articles)
```

这样就只会删除从表的外键,这样可以防止从表的外键指向不存在的记录

也可以.Select("Articles").Delete(&user)这样会把主表中的数据和从表的外键删掉

#### 重写外键引用

```Go
type User struct {
  ID       uint      `gorm:"size:4"`
  Name     string    `gorm:"size:8"`
  Articles []Article `gorm:"foreignKey:UserName;references:Name"` // 用户拥有的文章列表
}

type Article struct {
  ID       uint   `gorm:"size:4"`
  Title    string `gorm:"size:16"`
  UserName string
  User     User `gorm:"references:Name"` // 属于(这里的reference写的应该是主表的对应字段,foreign写的是从表中的字段)
}
```

这样写的意思就是会把原来从表中外键直接替换为主表中的对应字段(不常用)

#### 一对一

如果在一个结构体中声明另一个结构体,但是另一个结构体中没有声明该结构体的属性,只有对应的主键,此时他们就是一对一关系

```Go
type User struct {
  ID       uint
  Name     string
  Age      int
  Gender   bool
  UserInfo UserInfo // 通过UserInfo可以拿到用户详情信息
}

type UserInfo struct {
  UserID uint // 外键
  ID     uint
  Addr   string
  Like   string
}
```

这样在添加时就会把从表的部分放在从表中,想要一块查出来也需要预加载

```Go
DB.Create(&User{
  Name:   "枫枫",
  Age:    21,
  Gender: true,
  UserInfo: UserInfo{
    Addr: "湖南省",
    Like: "写代码",
  },
})
var user User
DB.Preload("UserInfo").Take(&user)
fmt.Println(user)
```

删除时要.Select("UserInfo").Delete(&user),这个user是预置了数据的对象

#### 多对多

声明多对多关系需要在两个结构体中互相声明一个对方的切片,再加上tag,结构如下

```Go
type Tag struct {
  ID       uint
  Name     string
  Articles []Article `gorm:"many2many:article_tags;"` // 用于反向引用
}

type Article struct {
  ID    uint
  Title string
  Tags  []Tag `gorm:"many2many:article_tags;"`
}
```

这样在添加时就会自动对应外键了,也支持预加载功能

建表时会自动创建中间表,但是默认的中间表只有双方的id,如果需要加入更多自定义信息,就需要自己写一个结构体去创建了

```Go
type ArticleTag struct {
  ArticleID uint `gorm:"primaryKey"`
  TagID     uint `gorm:"primaryKey"`
  CreatedAt time.Time
}
```

但是创建前还需要设置他们之间的关系(在添加和更新前都需要指定,查询则不需要)

```Go
// 设置Article的Tags表为ArticleTag
DB.SetupJoinTable(&Article{}, "Tags", &ArticleTag{})
// 如果tag要反向应用Article，那么也得加上
DB.SetupJoinTable(&Tag{}, "Articles", &ArticleTag{})
err := DB.AutoMigrate(&Article{}, &Tag{}, &ArticleTag{})
fmt.Println(err)
```

如果需要修改中间表的字段名,可以使用joinForeignKey和joinReferences这两个tag

```Go
type ArticleModel struct {
  ID    uint
  Title string
  Tags  []TagModel `gorm:"many2many:article_tags;joinForeignKey:ArticleID;JoinReferences:TagID"`
}

type TagModel struct {
  ID       uint
  Name     string
  Articles []ArticleModel `gorm:"many2many:article_tags;joinForeignKey:TagID;JoinReferences:ArticleID"`
}

type ArticleTagModel struct {
  ArticleID uint `gorm:"primaryKey"` // article_id
  TagID     uint `gorm:"primaryKey"` // tag_id
  CreatedAt time.Time
}
```

## 自定义数据类型

对于一些数据库中不存在的类型,gorm提供自定义数据类型,来规定这种在存入数据库时转化为什么格式,取出时再转化为什么格式

自定义的数据类型必须实现 Scanner 和 Valuer 接口

#### 存储json

```Go
type Info struct {
  Status string `json:"status"`
  Addr   string `json:"addr"`
  Age    int    `json:"age"`
}

// Scan 从数据库中读取出来
func (i *Info) Scan(value interface{}) error {
  bytes, ok := value.([]byte)
  if !ok {
    return errors.New(fmt.Sprint("Failed to unmarshal JSONB value:", value))
  }
  err := json.Unmarshal(bytes, i)
  return err
}

// Value 存入数据库
func (i Info) Value() (driver.Value, error) {
  return json.Marshal(i)
}

type AuthModel struct {
  ID   uint
  Name string
  Info Info `gorm:"type:string"` //此时需要单独指定类型
}

func main() {
  DB.AutoMigrate(&AuthModel{})
}
```

#### 存储枚举

对于用于存储状态的字段,一般会用数字代替以节省空间,但是这样可读性会降低,所以我们可以将int声明为中自定义类型格式(go的),然后重写类型转化时的逻辑,这样在其转码时就会自动进行对应

```Go
type Status int

func (s Status) MarshalJSON() ([]byte, error) {
  return json.Marshal(s.String())
}

func (s Status) String() string {
  var str string
  switch s {
  case Running:
    str = "Running"
  case Except:
    str = "Except"
  case OffLine:
    str = "Status"
  }
  return str
}

const (
  Running Status = 1
  OffLine Status = 2
  Except  Status = 3
)

type Host struct {
  ID     uint   `json:"id"`
  Status Status `gorm:"size:8" json:"status"`
  IP     string `json:"ip"`
}

func main() {
  //DB.AutoMigrate(&Host{})

  //DB.Create(&Host{
  //  IP:     "192.168.200.12",
  //  Status: Running,
  //})
  var host Host
  DB.Take(&host)
  fmt.Println(host)
  fmt.Printf("%#v,%T\n", host.Status, host.Status)
  data, _ := json.Marshal(host)
  fmt.Println(string(data))

}
```

## 事务

GORM是默认开启事务的,如果关闭了,可以用以下方式手动创建事务

```Go
var zhangsan, lisi User
DB.Take(&zhangsan, "name = ?", "张三")
DB.Take(&lisi, "name = ?", "李四")
// 张三给李四转账100元
DB.Transaction(func(tx *gorm.DB) error {

  // 先给张三-100
  zhangsan.Money -= 100
  err := tx.Model(&zhangsan).Update("money", zhangsan.Money).Error
  if err != nil {
    fmt.Println(err)
    return err
  }

  // 再给李四+100
  lisi.Money += 100
  err = tx.Model(&lisi).Update("money", lisi.Money).Error
  if err != nil {
    fmt.Println(err)
    return err
  }
  // 提交事务
  return nil
})
```

或者

```Go
var zhangsan, lisi User
DB.Take(&zhangsan, "name = ?", "张三")
DB.Take(&lisi, "name = ?", "李四")

// 张三给李四转账100元
tx := DB.Begin() //注意开启事务后就不是用DB操作而是使用tx操作了

// 先给张三-100
zhangsan.Money -= 100
err := tx.Model(&zhangsan).Update("money", zhangsan.Money).Error
if err != nil {
  tx.Rollback()
}

// 再给李四+100
lisi.Money += 100
err = tx.Model(&lisi).Update("money", lisi.Money).Error
if err != nil {
  tx.Rollback()
}
// 提交事务
tx.Commit() //手动提交事务
```

## 高级配置

#### 跳过默认事务

为了确保数据一致性，GORM 会在事务里执行写入操作（创建、更新、删除）。如果没有这方面的要求，您可以在初始化时禁用它，这样可以获得60%的性能提升

```Go
db, err := gorm.Open(mysql.Open("gorm.db"), &gorm.Config{
  SkipDefaultTransaction: true,
})
```

#### 命名策略

gorm采用的命名策略是，表名是蛇形复数，字段名是蛇形单数

例如

```Go
type Student struct {
  Name      string
  Age       int
  MyStudent string
}
```

gorm会为我们这样生成表结构

```Go
CREATE TABLE students (name longtext,age bigint,my_student longtext)
```

我们也可以修改这些策略

```Go
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
  NamingStrategy: schema.NamingStrategy{
    TablePrefix:   "f_",  // 表名前缀
    SingularTable: false, // 单数表名
    NoLowerCase:   false, // 关闭小写转换
  },
})
```

#### 显示日志

gorm的默认日志是只打印错误和慢SQL

我们可以自己设置

```Go
var mysqlLogger logger.Interface
// 要显示的日志等级
mysqlLogger = logger.Default.LogMode(logger.Info)
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
  Logger: mysqlLogger,
})
```

如果你想自定义日志的显示

那么可以使用如下代码

```Go
newLogger := logger.New(
  log.New(os.Stdout, "\r\n", log.LstdFlags), // （日志输出的目标，前缀和日志包含的内容）
  logger.Config{
    SlowThreshold:             time.Second, // 慢 SQL 阈值
    LogLevel:                  logger.Info, // 日志级别
    IgnoreRecordNotFoundError: true,        // 忽略ErrRecordNotFound（记录未找到）错误
    Colorful:                  true,        // 使用彩色打印
  },
)
db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{
  Logger: newLogger,
})
```

部分展示日志

```Go
var model Student
session := DB.Session(&gorm.Session{Logger: newLogger})
session.First(&model)
// SELECT * FROM students ORDER BY students.name LIMIT 1
```

如果只想某些语句显示日志

```Go
DB.Debug().First(&model)
```