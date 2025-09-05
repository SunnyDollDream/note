## 基础语法

#### 数据类型

![[Pasted image 20250715133308.png]]

**字符串:**

其中字符串的定义除了使用"",''和""" """也代表字符串,三个双引号中的内容还可以换行(无需换行符),输出的格式也是换行的

如果字符串中的内容要出现引号,有以下规则:

- 单引号定义法，可以内含双引号
    
- 双引号定义法，可以内含单引号
    

也可以使用转移字符（\）来将引号解除效用，变成普通字符串

字符串支持+拼接,但是不能像java那样直接把其他类型的数据变为字符串,如果有需求可以使用格式化的方式`message = "学IT就来%s" % name`,这样就可以把name直接拼接到%s的位置(类似C,有%s,%d,%f,但是不必像c一样和类型一一对应,因为实质上是把要放入的数据转化为占位符的类型再放入),多个变量占位变量要用括号括起来并按照占位的顺序填入

此外,占位符也支持和c类似的%5.2f(五格宽,四舍五入保留两位小数)这种限制精度的格式,当宽度比原本的长度还短时就不会生效

Python还支持`f"内容{变量}"`的快速拼接方式,如`print(f"我是{name}，我成立于:{set_up_year}，我今天的股票价格是:{stock_price}")`,这种写法不做精度控制,也不理会类型,适用于快速格式化字符串

P.S.这里的变量也都可以是表达式

**类型转换:**

使用int(x),float(x),str(x)可以转换x的类型,并作为返回值返回,浮点数转整数会直接扔掉小数部分

#### 注释

单行注释使用#(一般再使用一个空格与文字内容隔开),多行使用""" """(多行注释一般用于解释:

整个Python代码文件,类,方法)

**变量**

Python中变量没有类型,而数据有类型,所以声明变量的时候无需声明类型(弱类型),可以使用type(<变量名>)的方法查看变量存储的数据的类型

**命名:**

标识符命名中，只允许出现英文,中文,数字和下划线这四类元素,其余任何内容都不被允许,并且数字不能位于开头,大小写敏感,并且也不能使用Python的关键字

Python的命名习惯多个单词组合变量名时使用下划线做分隔,英文字母全小写

#### 运算符

"//":取证除,返回商的整数部分 9//2 输出结果 4 , 9.0//2.0 输出结果 4.0

"**":指数,a**b,10**20为10的20次方

Python也支持 +=这样的运算符

#### 键盘输入

Python键盘输入使用变量 = input()的方式,甚至还可以在括号中写入字符串,在执行到这一句语句时还会额外输出这行内容(不会换行),之后再等待输入

但是这样输入的内容都会是字符串

#### 流程控制

Python的流程控制语句没有大括号,属于一个代码块的语句只需要空4个格即可,Python通过缩进判断代码块的归属关系。

**if:**

```Python
if 条件1 : #这里有一个冒号,并且条件只能是布尔值,没有括号
    ...
    ...
elif 条件2 :
    ...
    ...
else :
    ...
    ...
```

**While:**

```Python
while 条件 :
    ...
    ...
```

**for:**

```Python
for 临时变量 in 待处理数据集 : 
        ...
        ...
```

Python的for循环比较类似for-each循环,是一种”轮询”机制，是对一批内容进行”逐个处理”,不能定义循环条件.语法中的待处理数据集，严格来说，应该称之为可迭代类型,可迭代类型指，其内容可以一个个依次取出的一种类型，包括：字符串,列表,元组等

P.S.这里的i相当于定义在了循环外,在循环外是可以调用的,但是不推荐这样做

range([int st],int end,[int step]):

获取一个从0(或st)开始，到end结束的数字序列（不含end本身）,如range(5)取得的数据是：[0, 1, 2, 3, 4],step为数字之间的步长,不写默认为1

```Python
for i in range(5): 
       print(i)
```

break和continue和其他语言类似

#### 函数

len(x):返回x的长度

**定义:**

```Python
def 函数名(传入参数) : #(形参也没有类型)
    函数体
    return 返回值
```

P.S.

① 参数如不需要，可以省略,可以在形参表直接写 x = value来指定默认值(所有位置参数必须出现在默认参数前，包括函数定义和调用)

② 返回值如不需要，可以省略

③ 函数必须先定义后使用

函数没有返回值时,其实也会返回一个值,Python中有一个特殊的字面量：None，其类型是：<class 'NoneType'>无返回值的函数，实际上就是返回了：None这个字面量

None表示：空的、无实际意义的意思,函数返回的None，就表示，这个函数没有返回什么有意义的内容。也就是返回了空的意思。

None可以用于判断条件,等价于false

**函数返回多个值:**

如果函数需要返回多个值,可以按照如下语法返回

```Python
def test return():
    return 1，2
x，y= test return
print(x) # 结果1
print(y) # 结果2
```

按照返回值的顺序，写对应顺序的多个变量接收即可,变量之间用逗号隔开,支持不同类型的数据return

**参数:**

除了调用函数时根据函数定义的参数位置来传递参数,Python还可以在函数调用时通过“键=值”形式传递参数(这种方式叫做关键字参数),

P.S.函数调用时，如果有位置参数时，位置参数必须在关键字参数的前面，但关键字参数之间不存在先后顺序

不定长参数：不定长参数也叫可变参数. 用于不确定调用的时候会传递多少个参数(不传参也可以)的场景,*使用元组接收参数,**使用字典接收参数

```Python
def user info(*args):
    print(args)

#('TOM')
user info('TOM')
#('TOM'，18)
user info('TOM'，18)
```

传进的所有参数都会被args变量收集，它会根据传进参数的位置合并为一个元组(tuple)，args是元组类型，这就是位置传递

```Python
def user info(**kwargs):
    print(kwargs)
#{'name':'TOM'，'age':18，'id':110}
user info(name='TOM'，age=18，id=110)
```

参数是“键=值”形式的形式的情况下, 所有的“键=值”都会被kwargs接受, 同时会根据“键=值”组成字典.

**函数作为参数传递:**

函数本身也可以作为参数传入另一个函数内。

```Python
def test_func(compute):
    result = compute(1，2)
    print(result)
def compute(x，y):
    return x + y
test_func(compute) # 结果:3
```

**lambda表达式:**

函数的定义中,def关键字，可以定义带有名称的函数,lambda关键字，可以定义匿名函数（无名称）,有名称的函数，可以基于名称重复使用,无名称的匿名函数，只可临时使用一次。

匿名函数定义语法：

lambda 是关键字，表示定义匿名函数

传入参数表示匿名函数的形式参数，如：x, y 表示接收2个形式参数

函数体，就是函数的执行逻辑，要注意：只能写一行，无法写多行代码

```Python
def test_func(compute):
    result=compute(1，2)
    print(result)
test_func(lambda x，y:x+y) # 结果:3
```

**函数的说明文档:**

```Python
def func(x, y):
"""
函数说明
:param x:形参x的说明
:param y:形参y的说明
:return:返回值的说明
"""
    函数体
    return 返回值
```

这样在鼠标悬停在调用函数名上就会显示注解内容,(应该写在函数体之前)

**变量作用域:**

Python在局部变量与外部或全局变量重名时会覆盖而不是改写,如果想要修改外部或全局变量,可以使用global修饰符(类似于static)声明当前变量为全局变量

#### 数据容器

数据容器是一种可以容纳多份数据的数据类型，容纳的每一份数据称之为1个元素,每一个元素，可以是任意类型的数据，如字符串、数字、布尔等,也可以是容器类型(不需要指定泛型)。

![[Pasted image 20250715133329.png]]

容器的通用方法:

- len(容器):返回容器元素数量
    
- max(容器):统计容器的最大元素
    
- min(容器):统计容器的最小元素
    
- 类型转换:各种容器之间可以互相转换,如tuple(容器)
    
- sorted(容器, [reverse=True]):将给定容器进行排序,注意，排序后都会得到列表（list）对象。
    

![[Pasted image 20250715133334.png]]

**list:(以[]做标识符,类比ArrayList):**

```Python
#字面量
[元素1，元素2，元素3，元素4，...]
# 定义变量
变量名称 =[元素1，元素2，元素3，元素4，...]
# 定义空列表
变量名称 =[]
变量名称 = 1ist()
```

可以使用[index]下标访问指定元素(从0开始),下标可以是负数,表示倒着访问列表(从-1开始,表示倒数第一个元素),嵌套列表可以类似多维数组用[][]访问

存储上限为上限为2**63-1;

常用方法:

![[Pasted image 20250715133347.png]]

**tuple(元组):**

```Python
# 定义元组字面量
(元素，元素，元素)
# 定义元组变量
变量名称 = (元素，元素，元素)
# 定义空元组
变量名称 = () # 方式1
变量名称 = tup1e() # 方式2
```

P.S.创建元组时只有一个元素时也要加一个逗号,否则不是元组

tuple和list很相似,但是tulpe中的元素无法修改(如果其中有元素是其他可修改的集合类型,那么可以修改其中的元素)

常用方法:

![[Pasted image 20250715133357.png]]

**String**

字符串是字符的容器，一个字符串可以存放任意数量的字符,使用类似char的元组,内容不能修改,只能得到新的字符串(类似java)

常用方法:

![[Pasted image 20250715133403.png]]

P.S. .strip()传入字符串并不是只会删除一模一样的字符串,而是删除字符串中包含的所有字符,如("ab")实际就会删除头尾的所有a和b;

与java不同,String支持for-each遍历

**序列切片:**

序列是指：内容连续、有序，可使用下标索引的一类数据容器,列表、元组、字符串，均可以可以视为序列。序列支持切片，即从一个序列中，取出一个子序列

语法：`序列[起始下标:结束下标:步长]`

表示从序列中，从指定位置开始，依次取出元素，到指定位置结束，得到一个新序列：

起始下标表示从何处开始，可以留空，留空视作从头开始,结束下标（不含）表示何处结束，可以留空，留空视作截取到结尾,步长表示，依次取元素的间隔(步长N表示，每次跳过N-1个元素取,步长为负数表示，反向取（注意，起始下标和结束下标也要反向标记）)

P.S.此操作不会影响序列本身，而是会得到一个新的序列（列表、元组、字符串）

**集合:**

```Python
# 定义集合字面量
{元素，元素，元素}
# 定义集合变量
变量名称 = {元素，元素，元素}
# 定义空集合
变量名称 = set()
```

集合的特点：不支持元素的重复（自带去重功能）、并且内容无序,所以集合不支持下标索引访问(但是支持foreach循环),

但是集合和列表一样，是允许修改的

常用方法:

![[Pasted image 20250715133417.png]]

P.S.集合1和集合2的差集是指集合1有而集合2没有的

**字典:**

字典的定义，同样使用{}，不过存储的元素是一个个的键值对，如下语法：

```Python
# 定义字典字面量
{key: value, key:value,key: value}
# 定义字典变量
my_dict ={fkey:value,key:value,key: value}
# 定义空字典
my_dict = {} # 空字典定义方式1
my_dict = dict() # 空字典定义方式2
```

P.S.Key和Value可以是任意类型的数据（key不可为字典,Value可以为字典,也就是字典支持嵌套,使用字典[][]访问）,Key不可重复，重复会对原有数据覆盖

字典同集合一样，不可以使用下标索引,但是字典可以通过Key值来取得对应的Value(字典[key])

常用方法:

![[Pasted image 20250715133425.png]]

字典不能用while循环遍历,可以使用for循环,在循环内用key访问各个元素

语法：for key in 字典.keys()

#### 异常

**异常捕获:**

```Python
try:
    可能发生错误的代码
except [NameError/(NameError, ZeroDivisionError) as e]: #不写错误类型只能捕获常规错误
    如果出现异常执行的代码                                #多种错误要加括号作为元组
else: #else表示的是如果没有异常要执行的代码。
    print('我是else，是没有异常的时候执行的代码')
finally:    # finally表示的是无论是否异常都要执行的代码
    f.close()
```

错误可以直接print输出错误信息

如果要捕获所有异常可以写错误类型为Exception

**异常的传递:**

异常是具有传递性的,当函数func01中发生异常, 并且没有捕获处理这个异常的时候, 异常会传递到函数func02, 当func02也没有捕获处理这个异常的时候,main函数会捕获这个异常, 这就是异常的传递性,当所有函数都没有捕获异常的时候, 程序就会报错

#### 模块

Python 模块(Module)，是一个 Python 文件，以 .py 结尾. 模块能定义函数，类和变量，模块里也能包含可执行的代码.

**模块的作用:**

python中有很多各种不同的模块, 每一个模块都可以帮助我们快速的实现一些功能, 比如实现和时间相关的功能就可以使用time模块,我们可以认为一个模块就是一个工具包, 每一个工具包中都有各种不同的工具供我们使用进而实现各种不同的功能.(类比java中的工具类)

**模块的导入:**

模块在使用前需要先导入 导入的语法如下:

`[from 模块名]import[模块 |类|变量 | 函数 |*][as 别名]`

常用的组合形式如：

- import 模块名(导入整个模块,模块名可以写多个,用逗号分隔,调用方法时用模块名.方法())
    
- from 模块名 import 类、变量、方法等(只导入某个方法,调用时就不需要加模块名了)
    
- from 模块名 import *(同上,导入模块中的所有方法,无需写模块名调用方法)
    
- import 模块名 as 别名
    
- from 模块名 import 功能名 as 别名
    

P.S.当导入多个模块的时候，且模块内有同名功能. 当调用这个同名功能的时候，调用到的是后面导入的模块的功能

**自定义模块:**

每个Python文件都可以作为一个模块，模块的名字就是文件的名字

如果一个模块文件中有`__all__`变量，当使用`from xxx import *`导入时，只能导入这个列表中的元素,即可以给其赋值一个list,里边用字符串的方式写好相应的方法名,这样可以起到类似访问修饰符的作用

#### 包

从物理上看，包就是一个文件夹，在该文件夹下包含了一个 __init__.py 文件，该文件夹可用于包含多个模块文件.从逻辑上看，包的本质依然是模块

**包的作用:**

当我们的模块文件越来越多时,包可以帮助我们管理这些模块, 包的作用就是包含多个模块，但包的本质依然是模块

**导包:**

- import 包名.模块名,调用时用包名.模块名.目标
    
- from 包名 import *(此时必须在`__init__.py`文件中添加`__all__ = []`，控制允许导入的模块列表)
    

P.S.__all__针对的是 ’ from ... import * ‘ 这种方式,对 ‘ import xxx ’ 这种方式无效

**导入三方包:**

第三方包的安装非常简单，我们只需要使用Python内置的pip程序即可,在命令提示符程序里面输入：`pip install 包名称`即可通过网络快速安装第三方包

(默认获取三方包的网址在国外,可以使用`pip install -i` `https://pypi.tuna.tsinghua.edu.cn/simple` `包名称`在国内下载)

#### JSON

JSON是一种轻量级的数据交互格式。可以按照JSON指定的格式去组织和封装数据,JSON本质上是一个带有特定格式的字符串

**Python数据和Json数据的相互转化:**

```Python
# 导入json模块 
import json 

# 准备符合格式json格式要求的python数据 
data = [{"name": "老王", "age": 16}, {"name": "张三", "age": 20}]
 
# 通过 json.dumps(data) 方法把python数据转化为了 json数据 
data = json.dumps(data) 

# 通过 json.loads(data) 方法把json数据转化为了 python数据 
data = json.loads(data)
```

## 进阶

#### 闭包

在函数内部使用global修改全局变量存在一定风险,一般要在内部函数修改外部函数变量时不应该使用global修饰,而是使用nonlocal

在函数嵌套的前提下，内部函数使用了外部函数的变量，并且外部函数返回了内部函数，我们把这个使用外部函数变量的内部函数称为闭包。

```Python
def outer(logo):
    def inner(msg): # 闭包
        print(f"<{logo}>{msg}<{logo}>")
    return inner
```

优点：

- 无需定义全局变量即可实现通过函数，持续的访问、修改某个值
    
- 闭包使用的变量的所用于在函数内，难以被错误的调用修改
    

缺点：

- 由于内部函数持续引用外部函数的值，所以会导致这一部分内存空间不被释放，一直占用内存
    

#### 装饰器

装饰器其实也是一种闭包， 其功能就是在不破坏目标函数原有的代码和功能的前提下，为目标函数增加新功能。(类比AOP)

```Python
def outer(func):    # 通知
    def inner():
        print("我要睡觉了")
        func()
        print("我起床了")
    return inner
def sleep():    # 切面
    import random
    import time
    print("睡眠中..")
    time.sleep(random.randint(1,5))
fn = outer(sleep)    # 环绕在传入函数执行通知
fn()
# 注解写法
def outer(func):
    def inner():
        print("我要睡觉了")
        func()
        print("我起床了")
    return inner
@outer # 注解,与通知方法名相同
def sleep():
    import random
    import time
    print("睡眠中..")
    time.sleep(random.randint(1,5))
sleep()
```

#### 设计模式

**单例模式:**

单例模式（Singleton Pattern）是一种常用的软件设计模式，该模式的主要目的是确保某一个类只有一个实例存在。在整个系统中，某个类只能出现一个实例时，单例对象就能派上用场。

定义: 保证一个类只有一个实例,并提供一个访问它的全局访问点

适用场景:当一个类只能有一个实例，而客户可以从一个众所周知的访问点访问它时。

单例的实现模式：

```Python
class StrTools:
    pass
str_tool = StrTools()
# 在一个文件中定义如上代码
from test import str_tool
s1 = str_tool
s2 = str_tool
print(s1)
print(s2)
# 在另一个文件中导入对象,这样s1和s2就都是同一个对象了
```

**工厂模式:**

当需要大量创建一个类的实例的时候， 可以使用工厂模式。即，从原生的使用类的构造去创建对象的形式迁移到，基于工厂提供的方法去创建对象的形式

```Python
class Person:
    pass
class Worker(Person):
    pass
class Student(Person):
    pass
class Teacher(Person):
    pass
class Factory:
    def get_person(self, p_type):
        if p_type -'w':
            return Worker()
        elif p_type ='s':
            return Student()
        else:
            return Teacher()
factory = Factory()
worker = factory.get_person('w')
stu = factory.get_person('s')
teacher = factory.get_person('t')
```

#### 多线程

Python的多线程可以通过threading模块来实现。

```Python
import threading
thread_obj = threading.Thread([group [, target [, name [, args [, kwargs]]]]])
# group:暂时无用，未来功能的预留参数
# target:执行的目标任务名
# args:以元组的方式给执行任务传参
# kwargs:以字典方式给执行任务传参
# name:线程名，一般不用设置
# 启动线程，让线程开始工作
thread obj.start()
# 需要传参的话可以通过:args参数通过元组(按参数顺序)的方式传参或使用kwargs参数用字典的形式传参
def sing():
    while True:
        print("我在唱歌，啦啦啦...")
        time.sleep(1)
def dance(msg):
    while True:
        print(msg)
        time.sleep(1)
"""多线程运行模式"""
sing_thread = threading.Thread(target=sing)
dance_thread = threading.Thread(target=dance, args=("我在跳舞，哈哈哈",))
sing_thread.start()
dance thread.start()
"""多线程运行模式"""
sing thread = threading.Thread(target=sing)
dance_thread = threading.Thread(target=dance, kwargs={"msg":"我在跳舞，哈哈哈"})
sing_thread.start()
dance_thread.start()
```

#### 正则表达式

Python正则表达式，使用re模块，并基于re模块中三个基础方法来做正则匹配。分别是：match、search、findall 三个基础方法

re.match(匹配规则， 被匹配字符串)

从被匹配字符串开头进行匹配， 匹配成功返回匹配对象（包含匹配的信息），匹配不成功返回空。

search(匹配规则， 被匹配字符串)

搜索整个字符串，找出匹配的。从前向后，找到第一个后，就停止，不会继续向后,整个字符串都找不到，返回None

findall(匹配规则， 被匹配字符串)

匹配整个字符串，找出全部匹配项,找不到返回空list: []

如需使用正则表达式,在匹配规则的""前要加个r,字符串的r标记，表示当前字符串是原始字符串，即内部的转义字符无效而是普通字符,如找出特殊字符：`re.findall(r‘\W’, s)`

## 面向对象

#### 类和对象

**声明:**

```Python
class 类名称:    # class是关键字，表示要定义类了
    类的属性    # 类的属性，即定义在类中的变量(成员变量)
    
    类的行为    # 类的行为，即定义在类中的函数(成员方法)

# 创建类对象的语法:
对象 = 类名称()
```

**成员方法:**

```Python
def 方法名(self，形参1,形参N):
    方法体
def say_hi(self):
    print(f"Hi大家好，我是{self.name}")
```

在方法定义的参数列表中，有一个self关键字,self关键字是成员方法定义的时候，必须填写的,它用来表示类对象自身的意思,传参的时候可以忽略它,当我们使用类对象调用方法时，self会自动被python传入,在方法内部，想要访问类的成员变量，必须使用self

如果想声明一个成员变量或方法为私有,则要在变量名前加上两个下划线

**构造方法:**

Python类可以使用：__init__()方法，称之为构造方法。

```Python
def __init__(self, name, age, tel):
    self.name = name
    self.age = age
    self.tel = tel
    print("student类创建了一个对象")
```

创建对象时直接在 类名()中传入参数即可,无需显示调用__init__,会自动调用

构造方法也是成员方法，不要忘记在参数列表中提供：self

Python可以直接在构造方法内定义成员变量，需要使用self.成员变量名,因为直接写是定义局部变量

**魔术方法:**

Python内置的类方法我们称之为：魔术方法

![[Pasted image 20250715133444.png]]

__str__()(类比toString()):

```Python
def __str__(self):
    return f"student类对象，name={self.name},age={self.age}
```

__lt__ __gt__(重载小于大于):

```Python
def __lt__(self, other):
    return self.age < other.age
```

__le__ __ge__(≤,≥):

```Python
def __le__(self, other):
    return self.age <= other.age
```

__eq__(=):

```Python

def __eq__(self, other):
    return self.age == other.age
```

#### 继承

继承分为：单继承和多继承,使用如下语法，可以完成类的继承。

```Python
class 类名(父类名): #可以写多个实现多继承
    类内容体
```

继承表示：将从父类那里继承（复制）来成员变量和成员方法（不含私有）

多个父类中，如果有同名的成员，那么默认以继承顺序（从左到右）为优先级。即先继承的保留，后继承的被覆盖

**重载:**

父类函数可以在子类中进行重载,在子类中重新定义同名的属性或方法即可

一旦复写父类成员，那么类对象调用成员的时候，就会调用复写后的新成员,如果需要使用被复写的父类的成员，需要特殊的调用方式(只能在子类内调用父类的同名成员,子类的类对象直接调用会调用子类复写的成员):

```Python
方式1：
调用父类成员
     使用成员变量：父类名.成员变量
     使用成员方法：父类名.成员方法(self)

方式2：
使用super()调用父类成员
     使用成员变量：super().成员变量
     使用成员方法：super().成员方法()
```

#### 类型注解

Python可以使用类型注解的方式告诉IDE某个参数是什么类型,并不会真正的对类型做验证和判断。也就是，类型注解仅仅是提示性的，不是决定性的,一般无法直接看出变量类型之时才会添加变量的类型注解

语法:变量: 类型

```Python
var_1:int =10 # 基础类型
stu:student=student() # 类
# 容器()也可以只写list不指定里边有什么
my_list:list[int]=[1，2，3]
my_tuple:tuple[str,int,bool]=("itheima",666,True)
my_set:set[int]={1，2，3}
my_dict: dict[str, int]= {"itheima": 666}
```

元组类型设置类型详细注解，需要将每一个元素都标记出来

字典类型设置类型详细注解，需要2个类型，第一个是key第二个是value

除了使用 变量: 类型， 这种语法做注解外，也可以在注释中进行类型注解。

语法:# type: 类型

```Python
var_1 = random.randint(1，10)    # type: int
var_2= json.loads(data)    # type: dict[str, int]
var_3 = func()    # type: student
```

同时，函数（方法）的返回值也是可以添加类型注解的。

```Python
def 函数方法名(形参:类型,.....，形参:类型)->返回值类型:
    pass
```

对于元组之外的容器,如果其中存储了多种类型的数据,可以使用union[类型, ......, 类型],其可以定义联合类型注解,Union联合类型注解，在变量注解、函数（方法）形参和返回值注解中，均可使用。

```Python
from typing import union
my_list:list[union[str, int]]=[1,2,"itheima", "itcast"]
my_dict: dict[str, union[str, int]]= {"name":"周杰轮"，"age": 31}
```

#### 抽象方法和接口

抽象类：含有抽象方法的类称之为抽象类

抽象方法：方法体是空实现的（pass）称之为抽象方法

## 整合SQL

在Python中，使用第三方库：pymysql 来完成对MySQL数据库的操作。

`pip install pymysql`

#### **创建连接**

```Python
from pymysql import connection
# 获取到MySQL数据库的链接对象
conn = connection(
    host='localhost', # 主机名(或IP地址)
    port=3306,        # 端口，默认3306
    user='root',      # 账户名
    password='123456' # 密码
)
# 打印MySQL数据库软件信息
print(conn.get_server_info()
#关闭到数据库的链接
conn.close()
```

#### 执行语句

```Python
# 获取游标对象
cursor = conn.cursor()
conn.select _db('test') #先选择数据库
# 使用游标对象，执行非查询的sql语句
cursor.execute("CREATE TABLE test_pymysql(id INT, info VARCHAR(255))")
#使用游标对象，执行查询sq1语句
cursor.execute('SELECT * FROM student')
# 获取查询结果
results:tuple = cursor.fetchall()
for r in results:
    print(r)
```

游标对象使用fetchall()方法，得到的是全部的查询结果，是一个元组,这个元组内部嵌套了元组，嵌套的元组就是一行查询结果

pymysql默认是不会自动提交事务的,可以在建立连接时加上一个autocommit = True开启自动提交,否则修改数据后需要手动调用`链接对象.commit()`提交事务