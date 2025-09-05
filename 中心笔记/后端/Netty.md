![[Pasted image 20250715134730.png]]
![[Pasted image 20250715134737.png]]
![[Pasted image 20250715134742.png]]


## HTTP(超文本传输协议(Hypertext Transfer Protocol))

一个HTTP请求大致包含如下内容

![[Pasted image 20250715134756.png]]
![[Pasted image 20250715134801.png]]
请求:

  

1. 请求行:对当前HTTP版本的描述(左图第一行)
    
2. 请求头:协议的原数据
    

Content-Length:body的总字节数,用于接收端确定一共要接收多少字节的数据

3. 请求体:传输的内容
    

响应:

1. 状态行:第一行为HTTP版本,状态码和状态码对应的描述
    
2. 响应头:
    
3. 响应体:
    

![[Pasted image 20250715134809.png]]

#### HTTP的不足

![[Pasted image 20250715134816.png]]

**HTTP1:**

1. 队头阻塞:HTTP1基于TCP,后边的分片必须等待前边的分片的到来才能继续发送数据,否则处于阻塞状态
    
2. 传输效率低:无用信息过多,请求头包含大量信息
    
3. 不支持多路复用:即当前请求未结束前,这个连接上是无法发送其他请求的
    

HTTP2也是基于TCP,没有解决队头阻塞问题,握手开销仍然较大

## SOCKS5代理原理

![[Pasted image 20250715134823.png]]

## TLS

HTTP中的信息都是明文传输的,这存在一定的安全隐患,而HTTPS就是安全版的HTTP协议,其之所以能够保证安全性,是因为它在应用层使用了TLS协议(传输加密协议).

TLS协议是专门用来对数据进行加密和解密的,他会在本地信息进入网络传输前进行加密,到达服务器端服务层再进行解密,这样在传输过程中的信息就都是加密过的了

其他保密协议还有SSL,它是TLS的前身,现在提到的SSL基本和TLS等同

除了 HTTPS 外，TLS可以被应用于任何基于 TCP/IP 协议的上层协议或应用程序，如 SFTP协议，SMTP协议，数据库间通信加密等等。

TLS采用的是对称加密的方法,先根据固定算法生成密钥,再由这个密钥对数据进行加密和解密,具体流程如下:

1. 客户端先向服务端发送一个Hello请求,告知服务端自己所支持的TLS版本和加密算法,再由服务端选择最优的版本和算法并返回客户端一个响应
    
2. 接收到服务端的响应后,客户端会先随机生成一串字符串(预主密钥),再用确定好的算法对这串字符串进行加密,由此生成密钥(回话密钥)
    
3. 之后客户端会将预主密钥和加密方法发送给服务端,服务端会用同样的方式生成一个与客户端完全一致的秘钥,这样客户端和服务端就可以相互通讯了
    

但在这个过程中,预主密钥是未经加密的,这就又产生了新的危险,因此TLS还有另一种非对称加密(PKI)的方法来传输预主密钥

非对称加密有公钥和私钥两种秘钥,公钥用于加密,私钥用于解密,公钥是向所有人公开的,但私钥是只有拥有者才知道的,其工作流程大致如下:

1. 服务端会有私钥和公钥,一开始还是会进行Hello请求,只是服务端在返回响应时会多返回公钥给客户端,在对预主密钥进行加密时就会使用公钥进行加密,之后再发送给服务端
    
2. 服务端接收到预主密钥后会使用私钥进行解密,然后客户端和服务端就会根据这个预主密钥生成会话密钥
    

但即使这样还是存在请求在一开始就被非法拦截,被攻击者返回了虚假的公钥的风险,所以就有了证书

#### 证书

证书是一个网站的唯一身份信息

1. key:服务器上的私钥文件，用于对发送给客户端数据的加密，以及对从客户端接收到数据的解密。
    
2. csr:证书签名请求文件，用于提交给证书颁发机构(CA)对证书签名。
    
3. crt:由证书颁发机构(CA)签名后的证书，或者是开发者自签名的证书，包含证书持有人的信息，持有人的公钥，以及签署者的签名等信息。
    
4. pem:是基于Base64编码的证书格式，扩展名包括PEM、CRT和CER。
    

#### Hash加密(单向加密算法)

任意长度的输入在经过hash算法加密后都会变为一串固定长度的字符串,称为散列值,散列值有以下特性:

- 不可逆性:无法对一个散列值做逆向操作来获取其原始数据
    
- 唯一性:不同的数据其计算得出的散列值永远不同，而同一输入计算出的散列值永远一样
    

利用hash算法对公钥和证书内容进行加密后就得到了证书的指纹码,证书指纹会和加密使用的hash算法一起放入证书中,这样在浏览器得到证书后就可以使用同样的方法得到指纹并与证书指纹进行比对,这样就可以证明证书没有被篡改过和证书的身份

#### 签名

要想检查证书的真实性(即是否真的是由对应的CA机构颁发),就需要用到签名,签名也使用了PKI加密,但是是使用私钥加密,公钥解密,而公钥是包含在根证书中在系统中预先安装好的,这样就避免了公钥被篡改的风险

TLS协议主要解决如下三个网络安全问题。

1)保密(message privacy)，保密通过加密encryption实现，所有信息都加密传输，第三方无法嗅探,

2)完整性(message integrity)，通过MAC校验机制，一旦被篡改，通信双方会立刻发现;

3)认证(mutual authentication)，双方认证,双方都可以配备证书，防止身份被冒充;

## NIO三大组件

1. channel:读写数据的双向通道(File-,Datagram-,Socket-Channel)
    
2. Buffer:缓冲区读写数据(MappedByte-,DirectByte-,HeapByte-Buffer)
    
3. Selector:配合一个线程来管理多个Channel,获取这些Channel上发生的事件,调用Selector的select()会阻塞直到Channel发生了读写就绪事件,这些事件发生,select()就会返回这些事件交给thread来处理
    

netty的依赖:netty-all(io.netty)

#### 基本使用

FileChannel可以通过FileInputStream("文件名.后缀").getChannel()获得

ByteBuffer可以通过ByteBuffer.allocate(字节数)获取

使用Channel的.read(Buffer)从Channel读取写到Buffer中(可以为buffer数组,会把前一个buffer写满之后写下一个),其有一个返回值为实际读到的字节数,如果读到了文件的末尾则为-1,可以利用这个返回值使用有限的缓冲区去读入比较大的数据,Channel的write()可以反过来从Buffer读写到Channel(形参可以是一个buffer数组,会把多个buffer的数据写入这个Channel中)(写入并不是立刻写到磁盘中,而是暂存在缓存中,可以调用.force(true)强制写入)

Buffer的.filp()方法可以将Buffer转为读模式(使用.get()获取Byte类型的数据),.clear()或.compact()可以转为写模式,.hasRemaining()可以检查是否还有未读出的内容

对于需要手动关闭的资源,可以放在try的()中,这种用法叫做try with resources,他会在try结束后自动调用括号中对象的.close()关闭

#### ByteBuffer

ByteBuffer 有以下重要属性

- capacity:容量
    
- position:当前的操作位置(写的时候为写入位置,读的时候为读取位置)
    
- limit:写入/读取限制
    

常用方法

.clear()会把Buffer中的数据全部清空,相当于重置为一开始的写模式,而.compact()则是只清除已经读取的数据,保留未读取的数据切换到写模式(其实只是将未读取的数据从0开始在存一遍,所以其实他们原来的位置上如果没有被覆写的话还是保有原来的数据的,只是因为此时转为了写模式,起始位置就在转存的数据的末尾,所以再写入时会把多余的数据覆写掉)

.put可以直接放入byte数据(byte数组也可以)(写数字代表ASCII码,要强转为byte类型)

.allocate()申请的内存为堆内存,读写效率较低且会受到GC的影响导致内存不时的迁移,还有.allocateDirect()申请的为系统内存,不会受到GC影响,读写效率高(相较堆内存少一次拷贝),但是因为需要调用系统函数,所以分配的时候效率低,但是使用时需要注意内存回收,防止内存泄漏

.get()会在读取数据的时候让position后移一位,其形参可以放一个byte数组,这样就可以一次读多个,想要重复读取数据时可以使用rewind()来将position重置为0,也可以直接使用get(int index)来获取指定位置的内容,这样不会让position后移

.mark()可以给当前position打上标记,再使用.reset()就可以回到标记的地方.

**字符串和Buffer的互相转化:**

1. 可以直接使用String的getbytes获取byte数组,调用Buffer的.put放入
    
2. 使用charsets或者StandardCharSets的.encode("str")直接获取一个Buffer对象,.decode(Buffer)可以把ByteBuffer转为Charbuffer,charbuffer就可以直接.toString()转为String了
    
3. ByteBuffer的.warp(byte[])可以把byte数组转为Buffer
    

#### 文件编程

不能直接打开fileChannel，必须通过 FilelnputStream、fileOutputStream或者RandomAccessfile来获取FileChannel，它们都有getChannel方法,fileChannel只能工作在阻塞模式下

- 通过 FilelnputStream 获取的 channel 只能读
    
- 通过 FileOutputStream 获取的 channel 只能写
    
- 通过 RandomAccessFile 是否能读写根据构造 RandomAccessFile 时的读写模式决定("rw"模式既能写也能读)
    

**常用方法:**

.transferTo(long position(写入的起始位置),long count(写入的数据量),Channel):可以使用这个方法完成数据的传输(两个Channel分别属于不同文件,传入方不存在时会自动创建)(最大传入2g的文件),返回值为读入的字节数

**Path和Paths:**

Paths是一个工具类,使用.get("文件路径")可以获取Path对象,传入多个参数会自动拼接/连接

对于一些存在..等特殊匹配符的路径可以使用path对象的.normalize()来简化路径

**Files工具类:**

常用方法:

.exists(Path):查看文件(夹)是否存在

.createDirectorys(Path):创建文件夹

.copy(Path source,Path target):拷贝文件,如果文件已存在，会抛异常 FileAlreadyExistsException,如果希望用 source 覆盖掉 target，需要用 StandardcopyOption 来控制

Files.copy(source, target, StandardcopyOptiOn.REPLACE_EXISTING);

Files.move(source,target,standardcopyOption.ATOMIC_MOVE):移动文件

StandardcopyOption.ATOMIC_MOVE 保证文件移动的原子性

.delete(Path)删除文件或空的文件夹

.walkFileTree():(可以用于删除和拷贝多级目录)

![[Pasted image 20250715134835.png]]

SimpleFileVistor是一个接口,其内部有方法.pre/psotVistDirectory:指定进入文件夹前/后的操作,visitFile()指定遍历文件时的操作,visitFIleFail()指定读取文件失败时的操作

## NIO

SeverrSocketChannel是一个等待与服务器连接的管道,可以通过ServerSocketChannel.open()获得,获得后还需要使用.bind(new InetSocketAddress(端口号))将该对象绑定到一个监听端口上,之后死循环使用.accept()方法接受服务器连接,连接成功后会返回一个SocketChannel对象,这个就可以当做正常Channel对象使用了

客户端要连接就直接新建一个SocketChannel对象(使用SocketChannel.open()),调用.connect(new InetSocketAddress(主机名,端口号))即可

通过调用ServerSocketChannel或者SocketChannel的.configureBlocking()可以改变其阻塞(true)还是非阻塞模式(false),此时他的accept()或read()等方法就变成非阻塞了,没有建立连接就返回null

#### Selector

也是使用Selector.open()获取对象,再调用ServerSocketChannel对象的.register(Selector)方法注册到Selector即可,这个方法会返回一个SelectionKey对象,通过这个对象就可以在事件发生后知道这个事件和其发生的Channel

事件有四种类型,分别为

- accept:accept()接收到连接请求时触发
    
- connect:客户端建立连接后触发
    
- read:SocketChannel中有数据时触发,可读事件
    
- wirte:可写事件
    

调用SelectionKey对象的interestOps(SelectionKey.<类型>)即可设置关注的事件类型,此时在程序中调用Selector的.select()方法,他会在没有事件发生的时候阻塞线程减少消耗,发生时才继续运行

![[Pasted image 20250715134842.png]]

调用.selectedKeys()就可以获得发生的所有事件(SelectionKey),调用.channel()就可以获得对应消息发生的channel(为SelectChannel,需要强转),此时就可以调用.accept()方法连接了