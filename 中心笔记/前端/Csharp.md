## 基础语法
### 程序结构
```C#
using System;
namespace HelloWorldApplication
{
   class HelloWorld
   {
      static void Main(string[] args)
      {
         /* 我的第一个 C# 程序*/
         Console.WriteLine("Hello World");
         Console.ReadKey();
      }
   }
}
```
**顶级语句（Top-Level Statements）**

在 C# 9.0 版本中，引入了顶级语句（Top-Level Statements）的概念，这是一种新的编程范式，允许开发者在文件的顶层直接编写语句，而不需要将它们封装在方法或类中。

**特点：**

- **无需类或方法**：顶级语句允许你直接在文件的顶层编写代码，无需定义类或方法。
- **文件作为入口点**：==包含顶级语句的文件被视为程序的入口点==，类似于 C# 之前的 `Main` 方法。
- **自动 `Main` 方法**：编译器会自动生成一个 `Main` 方法，并将顶级语句作为 `Main` 方法的主体。
- **支持局部函数**：尽管不需要定义类，但顶级语句的文件中仍然可以定义局部函数。
- **更好的可读性**：对于简单的脚本或工具，顶级语句提供了更好的可读性和简洁性。
- **适用于小型项目**：顶级语句非常适合小型项目或脚本，可以快速编写和运行代码。
- **与现有代码兼容**：顶级语句可以与现有的 C# 代码库一起使用，不会影响现有代码。

使用顶级语句，上边的代码可以简化为：
```C#
using System;

Console.WriteLine("Hello, World!");
```
>**文件限制**：顶级语句只能在一个源文件中使用。如果在一个项目中有多个使用顶级语句的文件，会导致编译错误。
>**程序入口**：如果使用顶级语句，则该文件会隐式地包含 Main 方法，并且该文件将成为程序的入口点。
>**作用域限制**：顶级语句中的代码共享一个全局作用域，这意味着可以在顶级语句中定义的变量和方法可以在整个文件中访问。
### 变量名命名规则

- 标识符必须以字母、下划线或 ==@== 开头，后面可以跟一系列的字母、数字（ 0 - 9 ）、下划线（ _ ）、==@==。
- 标识符中的第一个字符不能是数字。
- 标识符必须不包含任何嵌入的空格或符号，比如 ? - +! # % ^ & * ( ) [ ] { } . ; : " ' / \。
- 标识符不能是 C# 关键字。除非它们有一个 @ 前缀。 例如，@if 是有效的标识符，但 if 不是，因为 if 是关键字。
- 标识符必须区分大小写。大写字母和小写字母被认为是不同的字母。
- 不能与C#的类库名称相同。
### 数据类型
| 类型          | 描述                          | 范围                                                     | 默认值      |
| ----------- | --------------------------- | ------------------------------------------------------ | -------- |
| bool        | 布尔值                         | True 或 False                                           | False    |
| byte        | 8 位无符号整数                    | 0 到 255                                                | 0        |
| char        | 16 位 Unicode 字符             | U +0000 到 U +ffff                                      | '\0'     |
| ==decimal== | ==128 位精确的十进制值，28-29 有效位数== | ==(-7.9 x 1028 到 7.9 x 1028) / 100 到 28==              | ==0.0M== |
| double      | 64 位双精度浮点型                  | (+/-)5.0 x 10-324 到 (+/-)1.7 x 10308                   | 0.0D     |
| float       | 32 位单精度浮点型                  | -3.4 x 1038 到 + 3.4 x 1038                             | 0.0F     |
| int         | 32 位有符号整数类型                 | -2,147,483,648 到 2,147,483,647                         | 0        |
| long        | 64 位有符号整数类型                 | -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807 | 0L       |
| ==sbyte==       | ==8 位有符号整数类型==                  | ==-128 到 127==                                             | ==0==        |
| short       | 16 位有符号整数类型                 | -32,768 到 32,767                                       | 0        |
| uint        | 32 位无符号整数类型                 | 0 到 4,294,967,295                                      | 0        |
| ulong       | 64 位无符号整数类型                 | 0 到 18,446,744,073,709,551,615                         | 0        |
| ushort      | 16 位无符号整数类型                 | 0 到 65,535                                             | 0        |
>如需得到一个类型或一个变量在特定平台上的准确尺寸，可以使用 **sizeof** 方法。表达式 _sizeof(type)_ 产生以字节为单位存储对象或类型的存储尺寸。

**内置的** 引用类型有：**object**、**dynamic** 和 **string**。
用户自定义引用类型有：class、interface 或 delegate。
#### 动态（Dynamic）类型
您可以存储任何类型的值在动态数据类型变量中。这些变量的类型检查是在运行时发生的。
声明动态类型的语法：
```C#
dynamic <variable_name> = value;
```
例如：
```C#
dynamic d = 20;
```
动态类型与对象类型相似，但是对象类型变量的类型检查是在编译时发生的，而动态类型变量的类型检查是在运行时发生的。
#### 指针类型（Pointer types）
指针类型变量存储另一种类型的内存地址。C# 中的指针与 C 或 C++ 中的指针有相同的功能。
声明指针类型的语法：
```C#
type* identifier;
```
例如：
```C#
char* cptr;
int* iptr;
```
#### 自定义类型转换
C# 还允许你定义自定义类型转换操作，通过在类型中定义 implicit 或 explicit 关键字。
```C#
using System;

public class Fahrenheit  
{  
    public double Degrees { get; set; }  
  
    public Fahrenheit(double degrees)  
    {  
        Degrees = degrees;  
    }  
  
    // 隐式转换从Fahrenheit到double  
    public static implicit operator double(Fahrenheit f)  
    {  
        return f.Degrees;  
    }  
  
    // 显式转换从double到Fahrenheit  
    public static explicit operator Fahrenheit(double d)  
    {  
        return new Fahrenheit(d);  
    }  
}  
  
public class Program  
{  
    public static void Main()  
    {  
        Fahrenheit f = new Fahrenheit(98.6);  
        Console.WriteLine("Fahrenheit object: " + f.Degrees + " degrees");  
  
        double temp = f; // 隐式转换  
        Console.WriteLine("After implicit conversion to double: " + temp + " degrees");  
  
        Fahrenheit newF = (Fahrenheit)temp; // 显式转换  
        Console.WriteLine("After explicit conversion back to Fahrenheit: " + newF.Degrees + " degrees");  
    }  
}
```
### 单问号和双问号
? 单问号用于对 int、double、bool 等无法直接赋值为 null 的数据类型进行 null 的赋值，意思是这个数据类型是 Nullable 类型的。
```C#
int? i = 3;
```
等同于：
```C#
Nullable<int> i = new Nullable<int>(3);

int i; //默认值0
int? ii; //默认值null
```
?? 双问号用于判断一个变量在为 null 的时候返回一个指定的值。
#### 可空类型
C# 提供了一个特殊的数据类型，**nullable** 类型（可空类型），可空类型可以表示其基础值类型正常范围内的值，再加上一个 null 值。
E.G. Nullable< bool > 变量可以被赋值为 true 或 false 或 null。
声明一个 **nullable** 类型（可空类型）的语法如下：
```C#
< data_type> ? <variable_name> = null;
```

```C#
using System;
namespace CalculatorApplication
{
   class NullablesAtShow
   {
      static void Main(string[] args)
      {
         int? num1 = null;
         int? num2 = 45;
         double? num3 = new double?();
         double? num4 = 3.14157;
         
         bool? boolval = new bool?();

         // 显示值
         
         Console.WriteLine("显示可空类型的值： {0}, {1}, {2}, {3}", 
                            num1, num2, num3, num4);
         Console.WriteLine("一个可空的布尔值： {0}", boolval);
         Console.ReadLine();

      }
   }
}
// 显示可空类型的值： , 45,  , 3.14157
// 一个可空的布尔值：
```
#### Null 合并运算符（ ?? ）
>类似js的 || ,不是三目运算符

Null 合并运算符用于定义可空类型和引用类型的默认值。Null 合并运算符为类型转换定义了一个预设值，以防可空类型的值为 Null。Null 合并运算符把操作数类型隐式转换为另一个可空（或不可空）的值类型的操作数的类型。

如果第一个操作数的值为 null，则运算符返回第二个操作数的值，否则返回第一个操作数的值。
```C#
using System;
namespace CalculatorApplication
{
   class NullablesAtShow
   {
         
      static void Main(string[] args)
      {
         
         double? num1 = null;
         double? num2 = 3.14157;
         double num3;
         num3 = num1 ?? 5.34;      // num1 如果为空值则返回 5.34
         Console.WriteLine("num3 的值： {0}", num3);
         num3 = num2 ?? 5.34;
         Console.WriteLine("num3 的值： {0}", num3);
         Console.ReadLine();

      }
   }
}
// num3 的值： 5.34
// num3 的值： 3.14157
```
### 数组
在 C# 中声明一个数组，您可以使用下面的语法：
```C#
datatype[] arrayName;
```
其中，
- datatype 用于指定被存储在数组中的元素的类型。
- `[ ]` 指定数组的秩（维度）。秩指定数组的大小。
- arrayName 指定数组的名称。
#### 初始化数组
声明一个数组不会在内存中初始化数组。当初始化数组变量时，您可以赋值给数组。
数组是一个引用类型，所以您需要使用 new 关键字来创建数组的实例。
例如：
```C#
double[] balance = new double[10];
// 或
double[] balance = { 2340.0, 4523.69, 3421.0};

int [] marks = new int[5]  { 99,  98, 92, 97, 95};
```
>当您创建一个数组时，C# 编译器会根据数组类型隐式初始化每个数组元素为一个默认值。例如，int 数组的所有元素都会被初始化为 0。
#### 多维数组
C# 支持多维数组。多维数组又称为矩形数组。
您可以声明一个 string 变量的二维数组，如下：
```C#
string [,] names;
// 初始化
int [,] a = new int [3,4] {
 {0, 1, 2, 3} ,   /*  初始化索引号为 0 的行 */
 {4, 5, 6, 7} ,   /*  初始化索引号为 1 的行 */
 {8, 9, 10, 11}   /*  初始化索引号为 2 的行 */
};
//访问
int val = a[2,3];
```
或者，您可以声明一个 int 变量的三维数组，如下：
```C#
int [ , , ] m;
```
#### 交错数组

### @字符串
C# string 字符串的前面可以加 @（称作"逐字字符串"）将转义字符（\）当作普通字符对待，比如：
```C#
string str = @"C:\Windows";
```
等价于：
```C#
string str = "C:\\Windows";
```
@ 字符串中可以任意换行，换行符及缩进空格都计算在字符串长度之内。
```C#
string str = @"<script type=""text/javascript"">
    <!--
    -->
</script>";
```
### 其他运算符
![[Pasted image 20250922082908.png]]
>typeof获取的是一个Type对象,相当于是java的元对象,里边有这个类的限定名等属性
### foreach循环
C# 的 foreach 循环可以用来遍历集合类型，例如数组、列表、字典等。它是一个简化版的 for 循环，使得代码更加简洁易读。

以下是 foreach 循环的语法：
```C#
foreach (var item in collection)
{
    // 循环
}
```
collection 是要遍历的集合，item 是当前遍历到的元素。
### 访问修饰符
一个 访问修饰符 定义了一个类成员的范围和可见性。C# 支持的访问修饰符如下所示：
>类成员的默认访问修饰符为private

public：所有对象都可以访问；
private：对象本身在对象内部可以访问；
protected：只有该类对象及其子类对象可以访问
internal：同一个程序集的对象可以访问；
protected internal：访问限于当前程序集或派生自包含类的类型。
![[Pasted image 20250922084059.png]]
Internal 访问修饰符允许一个类将其成员变量和成员函数暴露给==当前程序中的其他函数和对象==。换句话说，带有 internal 访问修饰符的任何成员可以被定义在该成员所定义的应用程序内的任何类或方法访问。
```C#
using System;

namespace RectangleApplication
{
    class Rectangle
    {
        //成员变量
        internal double length;
        internal double width;
        
        double GetArea()
        {
            return length * width;
        }
       public void Display()
        {
            Console.WriteLine("长度： {0}", length);
            Console.WriteLine("宽度： {0}", width);
            Console.WriteLine("面积： {0}", GetArea());
        }
    }//end class Rectangle    
    class ExecuteRectangle
    {
        static void Main(string[] args)
        {
            Rectangle r = new Rectangle();
            r.length = 4.5;
            r.width = 3.5;
            r.Display();
            Console.ReadLine();
        }
    }
}
```
### 方法
当定义一个方法时，从根本上说是在声明它的结构的元素。在 C# 中，定义方法的语法如下：
```C#
<Access Specifier> <Return Type> <Method Name>(Parameter List)
{
   Method Body
}
```
下面是方法的各个元素：

- Access Specifier：访问修饰符，这个决定了变量或方法对于另一个类的可见性。
- Return type：返回类型，一个方法可以返回一个值。返回类型是方法返回的值的数据类型。如果方法不返回任何值，则返回类型为 void。
- Method name：方法名称，是一个唯一的标识符，且是大小写敏感的。它不能与类中声明的其他标识符相同。
- Parameter list：参数列表，使用圆括号括起来，该参数是用来传递和接收方法的数据。参数列表是指方法的参数类型、顺序和数量。参数是可选的，也就是说，一个方法可能不包含参数。
- Method body：方法主体，包含了完成任务所需的指令集。
#### 参数传递
当调用带有参数的方法时，您需要向方法传递参数。在 C# 中，有三种向方法传递参数的方式：
![[Pasted image 20250922090520.png]]
1. 按值传递
这是参数传递的默认方式。在这种方式下，当调用一个方法时，会为每个值参数创建一个新的存储位置。

实际参数的值会复制给形参，实参和形参使用的是两个不同内存中的值。所以，当形参的值发生改变时，不会影响实参的值，从而保证了实参数据的安全。
2. 按引用传递
引用参数是一个对变量的**内存位置的引用**。当按引用传递参数时，与值参数不同的是，它不会为这些参数创建一个新的存储位置。引用参数表示与提供给方法的实际参数具有相同的内存位置。

在 C# 中，使用 **ref** 关键字声明引用参数。
```C#
using System;
namespace CalculatorApplication
{
   class NumberManipulator
   {
      public void swap(ref int x, ref int y)
      {
         int temp;

         temp = x; /* 保存 x 的值 */
         x = y;    /* 把 y 赋值给 x */
         y = temp; /* 把 temp 赋值给 y */
       }
   
      static void Main(string[] args)
      {
         NumberManipulator n = new NumberManipulator();
         /* 局部变量定义 */
         int a = 100;
         int b = 200;

         Console.WriteLine("在交换之前，a 的值： {0}", a);
         Console.WriteLine("在交换之前，b 的值： {0}", b);

         /* 调用函数来交换值 */
         n.swap(ref a, ref b);

         Console.WriteLine("在交换之后，a 的值： {0}", a);
         Console.WriteLine("在交换之后，b 的值： {0}", b);
 
         Console.ReadLine();

      }
   }
}
```
>ref在定义形参和传递实参的时候都要写
3. 按输出传递
return 语句可用于只从函数中返回一个值。但是，可以使用 **输出参数** 来从函数中返回两个值。输出参数会把方法输出的数据赋给自己，其他方面与引用参数相似。
```C#
using System;

namespace CalculatorApplication
{
   class NumberManipulator
   {
      public void getValues(out int x, out int y )
      {
          Console.WriteLine("请输入第一个值： ");
          x = Convert.ToInt32(Console.ReadLine());
          Console.WriteLine("请输入第二个值： ");
          y = Convert.ToInt32(Console.ReadLine());
      }
   
      static void Main(string[] args)
      {
         NumberManipulator n = new NumberManipulator();
         /* 局部变量定义 */
         int a , b;
         
         /* 调用函数来获取值 */
         n.getValues(out a, out b);

         Console.WriteLine("在方法调用之后，a 的值： {0}", a);
         Console.WriteLine("在方法调用之后，b 的值： {0}", b);
         Console.ReadLine();
      }
   }
}
```
>与ref类似,但是out形参传入的实参变量可以未初始化
