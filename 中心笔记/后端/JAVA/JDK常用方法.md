#### 对基本类型的容器集合进行排序

Collections.sort()

#### .trim()去空格是字符串对象就有的方法

#### List的remove()

这个方法有两种形参,一个是int,一个是Object,当集合中存放整数时就会产生歧义,会按照int去删除对应下标,所以在使用时如果想要删除对应值,则应在数前声明(Integer)

#### vector的toarray要在形参放入一个声明好的数组

#### MessageFormat.format()

这个方法可以使用类似C语言printf的格式使用.format("一{0},二{1},3{2}",a,b,c)的格式将元素拼接到字符串内.

#### JAVA四舍五入 Math.round()

Math.round 函数接收一个float或double类型的参数，用于对数字进行四舍五入，即返回一个离传入参数最近的整数（如果传入参数是float返回int类型结果，如果传入参数是double返回long类型结果）。

#### Arrays.binarysort()

这个二分查找在没有找到元素时会返回应该插入的位置的下标的负数在-1的值,如7,9,10,8应该插入在下标为一的位置,所以返回值会为-2.

#### HashMap获取key的值不存在则设置初始值

.getOrDefault(key,defaultValue)方法可以获取当前key的值,不存在则添加当前key并且设置初始值.

#### [字符串按字典序比较](https://blog.csdn.net/qq_44925904/article/details/102848571) .compareTo(String str)

用字典顺序比较两个字符串。str和str2进行相比，当str比str2大时，输出一个正整数；当str比str2小的时候输出一个负整数；str和str2相等时输出0。字符串里面很多内容都可以相比，同一内容的如全是小写字母间相比可以容易理解它们间的相减值，但若大写、小写、数值等不同类型两两相比较则输出一个难以知道它们相差的整数或者一行不知名的输出字符串。

#### StringBuilder的增删改查

增:append(String str)在末尾添加,insert(Integer index,String str)在指定下标插入

删:deleteCharAt(Integer index)删除指定下标位置元素,delete(int start,int end)区间子串删除

改:replace(int start,int end,String str)区间替换,setCharAt(int index)替换指定下标元素

查:tostring()

#### 字符转进制数

Integer有方法把Ascii(字符自动转化)转其他进制

//toBinaryString(int i)返回变量的二进制表示的字符串

//toHexString(int i) 八进制

//toOctalString(int i) 十六进制

String result = Integer.toBinaryString(strChar[i]);

进制字符串可以通过Integer的praseInt(String str,int n)转为十进制,其中n为字符串的进制

#### ArrayList获取其中某个元素的下标 .indexOf()

#### char转String

char ch = 'a';

//第一种方法

String str = ch + "";

//第二种方法

String str = Character.toString(ch);

//第三种方法

String str = String.valueOf(ch);