## 什么是 Node.js
1. Node.js 是一个独立的 JavaScript 运行环境，能独立执行 JS 代码，因为这个特点，它可以用来编写服务器后端的应用程序
2. Node.js 作用除了编写后端应用程序，也可以对前端代码进行压缩，转译，整合等等，提高前端开发和运行效率
3. Node.js 基于Chrome V8 引擎封装，独立执行 JS 代码，但是语法和浏览器环境的 V8 有所不同，没有 document 和 window 但是都支持 ECMAScript 标准的代码语法
![[Pasted image 20251127212612.png]]
Node.js 执行目标 JS 文件，需要使用 node xxx.js 命令来执行（我们可以借助 VSCode 集成终端使用，好处：可以快速切换到目标 JS 文件所在终端目录，利用相对路径找到要执行的目标 JS 文件
   ![image-20230330112925228](images/image-20230330112925228.png)
## fs模块-读写文件

1. 模块：类似插件，封装了方法和属性供我们使用

2. fs 模块：封装了与本机文件系统进行交互的，方法和属性

3. fs 模块使用语法如下：
- 加载 fs 模块，得到 fs 对象
```js
const fs = require('fs')
```
- 写入文件内容语法：
```js
fs.writeFile('文件路径', '写入内容', (err) => {
  // 写入后的回调函数
})
```
>第三个参数是一个写入结束后执行的回调函数,即使没有错误也会执行
- 读取文件内容的语法：
```js
fs.readFile('文件路径', (err, data) => {
  // 读取后的回调函数
  // data 是文件内容的 Buffer 数据流,可以调用toString()转为字符串
})
```
E.G. 向 test.txt 文件写入内容并读取打印
```js
/**
 * 目标：使用 fs 模块，读写文件内容
 * 语法：
 * 1. 引入 fs 模块
 * 2. 调用 writeFile 写入内容
 * 3. 调用 readFile  读取内容
 */
// 1. 引入 fs 模块
const fs = require('fs')
// 2. 调用 writeFile 写入内容
// 注意：建议写入字符串内容，会覆盖目标文件所有内容
fs.writeFile('./text.txt', '欢迎使用 fs 模块读写文件内容', (err) => {
  if (err) console.log(err)
  else console.log('写入成功')
})
// 3. 调用 readFile  读取内容
fs.readFile('./text.txt', (err, data) => {
  if (err) console.log(err)
  else console.log(data.toString()) // 把 Buffer 数据流转成字符串类型
})
```
## path模块-路径处理

**为什么在 Node.js 待执行的 JS 代码中要用绝对路径：**

Node.js 执行 JS 代码时，代码中的路径都是以终端所在文件夹出发查找相对路径，而不是以我们认为的从代码本身出发，会遇到问题，所以在 Node.js 要执行的代码中，访问其他文件，建议使用绝对路径

新建 03 文件夹编写待执行的 JS 代码，访问外层相对路径下的文件，然后在最外层终端路径来执行目标文件，造成问题
![image-20230330113929178](images/image-20230330113929178.png)
![image-20230330113942679](images/image-20230330113942679.png)
问题原因：就是从代码文件夹出发，使用`../text.txt`解析路径，找不到目标文件，报错了！
### join()
解决方案：使用模块内置变量 `__dirname`配合 path.join() 来得到绝对路径使用
>path.join()可以接收任意个字符串,他会把字符串以当前系统下的文件分隔符连接起来,如果有'..'等特殊符号也会正常执行(如返回上一级目录)
>`__dirname`是当前代码所在的文件夹目录
```js
// 1. 加载 path 模块
const path = require('path')
// 2. 使用 path.join() 来拼接路径
const pathStr = path.join(__dirname, '..', 'text.txt')
console.log(pathStr)

fs.readFile(pathStr, (err, data) => {
  if (err) console.log(err)
  else console.log(data.toString())
})
```
>`__dirname`是CJS中的全局变量,不需要导入path也可以使用,但是在ESM中就没有这个变量了,需要额外获取
```js
// 在ES模块中，使用import.meta.url和path模块获取当前目录
const __dirname = path.dirname(new URL(import.meta.url).pathname);
```
### mkdir()
mkdir()可以创建文件夹,在writeFile时如果路径中有不存在的文件夹会报错,所以需要手动创建
>第一个参数为要创建的路径(写绝对路径),第二个参数为配置型,配置为{ recursive: true }时如果路径已存在不会报错,第三个为结束后执行的回调函数
```js
  fs.mkdir(distPath, { recursive: true }, (err, path) => {
    if (err) {
      console.log(err)
    } else {
      console.log(path)
    }
  })
```
## 案例-压缩前端html

1. 前端工程化：前端代码压缩，整合，转译，测试，自动部署等等工具的集成统称，为了提高前端开发项目的效率

2. 需求：把准备好的 html 文件里的回车符（\r）和换行符（\n）去掉进行压缩，写入到新 html 中

3. 步骤：

   1. 读取源 html 文件内容
   2. 正则替换字符串
   3. 写入到新的 html 文件中，并运行查看是否能正常打开网页

4. 代码如下：
```js
/**
 * 目标一：压缩 html 里代码
 * 需求：把 public/index.html 里的，回车/换行符去掉，写入到 dist/index.html 中
 *  1.1 读取 public/index.html 内容
 *  1.2 使用正则替换内容字符串里的，回车符\r 换行符\n
 *  1.3 确认后，写入到 dist/index.html 内
 */

const fs = require('fs')
const path = require('path')
// 1.1 读取 public/index.html 内容
fs.readFile(path.join(__dirname, 'test.html'), (err, data) => {
  const htmlStr = data.toString()
  // 1.2 使用正则替换内容字符串里的，回车符\r 换行符\n
  const resultStr = htmlStr.replace(/[\r\n]/g, '')
  // 1.3 确认后，写入到 dist/index.html 内
  // 确保dist目录存在，如果不存在则创建
  const distPath = path.join(__dirname, 'dist')
  fs.mkdir(distPath, { recursive: true }, (err, path) => {
    if (err) {
      console.log(err)
    } else {
      console.log(path)
    }
  })
  fs.writeFile(path.join(distPath, 'test.html'), resultStr, (err) => {
    if (err) console.log(err)
    else console.log('压缩成功')
  })
})
```
## http模块-创建Web服务
>需求：引入 http 模块，使用相关语法，创建 Web 服务程序，响应返回给请求方一句提示 ‘hello，world’

步骤：
1. 引入 http 模块，创建 Web 服务对象
2. 监听 request 请求事件，对本次请求，做一些响应处理
3. 启动 Web 服务监听对应端口号
4. 运行本服务在终端进程中，用浏览器发起请求
```js
/**
 * 目标：基于 http 模块创建 Web 服务程序
 *  1.1 加载 http 模块，创建 Web 服务对象
 *  1.2 监听 request 请求事件，设置响应头和响应体
 *  1.3 配置端口号并启动 Web 服务
 *  1.4 浏览器请求（http://localhost:3000）测试
 */
// 1.1 加载 http 模块，创建 Web 服务对象
const http = require('http')
const server = http.createServer()
// 1.2 监听 request 请求事件，设置响应头和响应体
server.on('request', (req, res) => {
  // 设置响应头-内容类型-普通文本以及中文编码格式
  res.setHeader('Content-Type', 'text/plain;charset=utf-8')
  // 设置响应体内容，结束本次请求与响应
  res.end('欢迎使用 Node.js 和 http 模块创建的 Web 服务')
})
// 1.3 配置端口号并启动 Web 服务
server.listen(3000, () => {
  console.log('Web 服务启动成功了')
})
```
## 案例-浏览时钟

### 目标

体验 Web 服务除了接口数据以外，还能返回网页资源等



### 讲解

1. 需求：基于 Web 服务，开发提供网页资源的功能，了解下后端的代码工作过程

   ![image-20230330175243783](images/image-20230330175243783.png)

2. 步骤：

   1. 基于 http 模块，创建 Web 服务
   2. 使用 req.url 获取请求资源路径为 /index.html 的时候，读取 index.html 文件内容字符串返回给请求方
   3. 其他路径，暂时返回不存在的提示
   4. 运行 Web 服务，用浏览器发起请求

3. 代码如下：

   ```js
   /**
    * 目标：编写 web 服务，监听请求的是 /index.html 路径的时候，返回 dist/index.html 时钟案例页面内容
    * 步骤：
    *  1. 基于 http 模块，创建 Web 服务
    *  2. 使用 req.url 获取请求资源路径，并读取 index.html 里字符串内容返回给请求方
    *  3. 其他路径，暂时返回不存在提示
    *  4. 运行 Web 服务，用浏览器发起请求
    */
   const fs = require('fs')
   const path = require('path')
   // 1. 基于 http 模块，创建 Web 服务
   const http = require('http')
   const server = http.createServer()
   server.on('request', (req, res) => {
     // 2. 使用 req.url 获取请求资源路径，并读取 index.html 里字符串内容返回给请求方
     if (req.url === '/index.html') {
       fs.readFile(path.join(__dirname, 'dist/index.html'), (err, data) => {
         res.setHeader('Content-Type', 'text/html;charset=utf-8')
         res.end(data.toString())
       })
     } else {
       // 3. 其他路径，暂时返回不存在提示
       res.setHeader('Content-Type', 'text/html;charset=utf-8')
       res.end('你要访问的资源路径不存在')
     }
   })
   server.listen(8080, () => {
     console.log('Web 服务启动了')
   })
   ```

   

### 小结

1. Web 服务程序都有什么功能？

   <details>
   <summary>答案</summary>
   <ul>
   <li>提供数据和网页资源等等功能，其他它的功能远不止于此</li>
   </ul>
   </details>



# 02_Node.js模块化

## 知识点自测

1. 以下代码运行的结果是多少？

   ```js
   const arr = [10, 20, 30]
   const result = arr.map(val => val + 1).reduce((sum, val) => sum += val, 0)
   console.log(result)
   ```

   A：60

   B：63

   <details>
   <summary>答案</summary>
   <ul>
   <li>B选项正确</li>
   </ul>
   </details>



2. 以下代码运行的结果是多少？

   ```js
   const obj = { username: 'itheima', password: '777777' }
   const { uname } = obj
   console.log(uname)
   ```

   A：itheima

   B：undefined

   <details>
   <summary>答案</summary>
   <ul>
   <li>A选项正确</li>
   </ul>
   </details>





## 01.模块化简介

### 目标

了解模块化概念和好处，以及 CommonJS 标准语法导出和导入



### 讲解

1. 在 Node.js 中每个文件都被当做是一个独立的模块，模块内定义的变量和函数都是独立作用域的，因为 Node.js 在执行模块代码时，将使用如下所示的函数封装器对其进行封装

   ![image-20230331150152299](images/image-20230331150152299.png)

​	

2. 而且项目是由多个模块组成的，每个模块之间都是独立的，而且提高模块代码复用性，按需加载，独立作用域

   ![image-20230331150407659](images/image-20230331150407659.png)

3. 但是因为模块内的属性和函数都是私有的，如果对外使用，需要使用标准语法导出和导入才可以，而这个标准叫 CommonJS 标准，接下来我们在一个需求中，体验下模块化导出和导入语法的使用

4. 需求：定义 utils.js 模块，封装基地址和求数组总和的函数，导入到 index.js 使用查看效果

   ![image-20230331150506876](images/image-20230331150506876.png)

5. 导出语法：

   ```js
   module.exports = {
     对外属性名: 模块内私有变量
   }
   ```

6. 导入语法：

   ```js
   const 变量名 = require('模块名或路径')
   // Node.js 环境内置模块直接写模块名（例如：fs，path，http）
   // 自定义模块：写模块文件路径（例如：./utils.js)
   ```

   > 变量名的值接收的就是目标模块导出的对象

7. 代码实现

   * utils.js：导出

     ```js
     /**
      * 目标：基于 CommonJS 标准语法，封装属性和方法并导出
      */
     const baseURL = 'http://hmajax.itheima.net'
     const getArraySum = arr => arr.reduce((sum, item) => sum += item, 0)
     
     // 导出
     module.exports = {
       url: baseURL,
       arraySum: getArraySum
     }
     ```

   * index.js：导入使用

     ```js
     /**
      * 目标：基于 CommonJS 标准语法，导入工具属性和方法使用
      */
     // 导入
     const obj = require('./utils.js')
     console.log(obj)
     const result = obj.arraySum([5, 1, 2, 3])
     console.log(result)
     ```

     

### 小结

1. Node.js 中什么是模块化?

   <details>
   <summary>答案</summary>
   <ul>
   <li>每个文件都是独立的模块</li>
   </ul>
   </details>

2. 模块之间如何联系呢?

   <details>
   <summary>答案</summary>
   <ul>
   <li>使用特定语法，导出和导入使用</li>
   </ul>
   </details>

3. CommonJS 标准规定如何导出和导入模块?

   <details>
   <summary>答案</summary>
   <ul>
   <li>导出：module.exports = {},导入：require('模块名或路径')</li>
   </ul>
   </details>

4. 模块名/路径如何选择?

   <details>
   <summary>答案</summary>
   <ul>
   <li>内置模块：写名字。例如：fs，path，http等。自定义模块：写模块文件路径，例如：./utils.js</li>
   </ul>
   </details>







## 02.ECMAScript标准-默认导出和导入

### 目标

掌握 ECMAScript 标准语法中，默认导出和导入的使用



### 讲解

1. CommonJS 规范是 Node.js 环境中默认的，后来官方推出 ECMAScript 标准语法，我们接下来在一个需求中，体验下这个标准中默认导出和导入的语法要如何使用

1. 需求：封装并导出基地址和求数组元素和的函数，导入到 index.js 使用查看效果

1. 导出语法：

   ```js
   export default {
     对外属性名: 模块内私有变量
   }
   ```

1. 导入语法：

   ```js
   import 变量名 from '模块名或路径'
   ```

   > 变量名的值接收的就是目标模块导出的对象


5. 注意：Node.js 默认只支持 CommonJS 标准语法，如果想要在当前项目环境下使用 ECMAScript 标准语法，请新建 package.json 文件设置 type: 'module' 来进行设置

   ```json
   { “type”: "module" }
   ```



6. 代码实现：

   * utils.js：导出

     ```js
     /**
      * 目标：基于 ECMAScript 标准语法，封装属性和方法并"默认"导出
      */
     const baseURL = 'http://hmajax.itheima.net'
     const getArraySum = arr => arr.reduce((sum, item) => sum += item, 0)
     
     // 默认导出
     export default {
       url: baseURL,
       arraySum: getArraySum
     }
     ```

   * index.js：导入

     ```js
     /**
      * 目标：基于 ECMAScript 标准语法，"默认"导入，工具属性和方法使用
      */
     // 默认导入
     import obj from './utils.js'
     console.log(obj)
     const result = obj.arraySum([10, 20, 30])
     console.log(result)
     ```



### 小结

1. ECMAScript 标准规定如何默认导出和导入模块?

   <details>
   <summary>答案</summary>
   <ul>
   <li>导出：export default {} 导入：import 变量名 from '模块名或路径'</li>
   </ul>
   </details>

2. 如何让 Node.js 切换模块标准为 ECMAScript？

   <details>
   <summary>答案</summary>
   <ul>
   <li>运行模块所在文件夹，新建 package.json 并设置 {“type”：“module”}</li>
   </ul>
   </details>



## 03.ECMAScript标准-命名导出和导入

### 目标

掌握 ECMAScript 标准语法中，命名导出和导入的使用



### 讲解

1. ECMAScript 标准的语法有很多，常用的就是默认和命名导出和导入，这节课我们来学习下命名导出和导入的使用

2. 需求：封装并导出基地址和数组求和函数，导入到 index.js 使用查看效果

3. 命名导出语法：

   ```js
   export 修饰定义语句
   ```

4. 命名导入语法：

   ```js
   import { 同名变量 } from '模块名或路径'
   ```

   > 注意：同名变量指的是模块内导出的变量名

5. 代码示例：

   * utils.js 导出

     ```js
     /**
      * 目标：基于 ECMAScript 标准语法，封装属性和方法并"命名"导出
      */
     export const baseURL = 'http://hmajax.itheima.net'
     export const getArraySum = arr => arr.reduce((sum, item) => sum += item, 0)
     
     ```

   * index.js 导入

     ```js
     /**
      * 目标：基于 ECMAScript 标准语法，"命名"导入，工具属性和方法使用
      */
     // 命名导入
     import {baseURL, getArraySum} from './utils.js'
     console.log(obj)
     console.log(baseURL)
     console.log(getArraySum)
     const result = getArraySum([10, 21, 33])
     console.log(result)
     ```

6. 与默认导出如何选择：

   * 按需加载，使用命名导出和导入
   * 全部加载，使用默认导出和导入




### 小结

1. Node.js 支持哪 2 种模块化标准?

   <details>
   <summary>答案</summary>
   <ul>
   <li>CommonJS 标准语法（默认）</li>
   <li>ECMAScript 标准语法</li>
   </ul>
   </details>

2. ECMAScript 标准，命名导出和导入的语法?

   <details>
   <summary>答案</summary>
   <ul>
   <li>导出：export 修饰定义的语句，import { 同名变量 } from '模块名或路径'</li>
   </ul>
   </details>

3. ECMAScript 标准，默认导出和导入的语法？

   <details>
   <summary>答案</summary>
   <ul>
   <li>导出：export default {} 导入：import 变量名 from '模块名或路径'</li>
   </ul>
   </details>



## 04.包的概念

### 目标

了解 Node.js 环境中包的概念



### 讲解

1. 包：将模块，代码，其他资料整合成一个文件夹，这个文件夹就叫包

2. 包分类：

   * 项目包：主要用于编写项目和业务逻辑
   * 软件包：封装工具和方法进行使用

3. 包要求：根目录中，必须有 package.json 文件（记录包的清单信息）

   ![image-20230331154702306](images/image-20230331154702306.png)

   ![image-20230331154639881](images/image-20230331154639881.png)

4. 包使用：在引入一个包文件夹到代码中，默认引入的是包文件节下的 index.js 模块文件里导出的对象，如果没有 index.js 文件，则会引入 package.json 里 main 属性指定的文件模块导出的对象

5. 需求：封装数组求和函数的模块，封装判断用户名和密码长度函数的模块，形成一个软件包，并导入到 index.js 中使用查看效果

6. 代码示例：

   * utils/lib 相关代码在素材里准备好了，只需要自己在 utils/index.js 统一出口进行导出

     ```js
     /**
      * 本文件是，utils 工具包的唯一出口
      * 作用：把所有工具模块方法集中起来，统一向外暴露
      */
     const { getArraySum } = require('./lib/arr.js')
     const { checkUser, checkPwd } = require('./lib/str.js')
     
     // 统一导出所有函数
     module.exports = {
       getArraySum,
       checkUser,
       checkPwd
     }
     
     ```

   * index.js 导入软件包文件夹使用（注意：这次导入的是包文件夹，不是模块文件）

     ```js
     /**
      * 目标：导入 utils 软件包，使用里面封装的工具函数
      */
     const obj = require('./utils')
     console.log(obj)
     const result = obj.getArraySum([10, 20, 30])
     console.log(result)
     ```

     



### 小结

1. 什么是包?

   <details>
   <summary>答案</summary>
   <ul>
   <li>将模块，代码，其他资料聚合成的文件夹</li>
   </ul>
   </details>

2. 包分为哪 2 类呢?

   <details>
   <summary>答案</summary>
   <ul>
   <li>项目包：编写项目代码的文件夹，软件包：封装工具和方法供开发者使用</li>
   </ul>
   </details>

3. package.json 文件的作用?

   <details>
   <summary>答案</summary>
   <ul>
   <li>记录软件包的名字，作者，入口文件等信息</li>
   </ul>
   </details>

4. 导入一个包文件夹的时候，导入的是哪个文件?

   <details>
   <summary>答案</summary>
   <ul>
   <li>默认 index.js 文件，或者 main 属性指定的文件</li>
   </ul>
   </details>



## 05.npm软件包管理器

### 目标

掌握使用 npm 管理软件包



### 讲解

1. npm 简介[链接]([http://dev.nodejs.cn/learn/an-introduction-to-the-npm-package-manager#npm-%E7%AE%80%E4%BB%8B](http://dev.nodejs.cn/learn/an-introduction-to-the-npm-package-manager))： 软件包管理器，用于下载和管理 Node.js 环境中的软件包

   ![image-20230331155406652](images/image-20230331155406652.png)

2. npm 使用步骤：

   1. 初始化清单文件： npm init -y （得到 package.json 文件，有则跳过此命令）

      > 注意 -y 就是所有选项用默认值，所在文件夹不要有中文/特殊符号，建议英文和数字组成，因为 npm 包名限制建议用英文和数字或者下划线中划线

   2. 下载软件包：npm i 软件包名称

   3. 使用软件包

3. 需求：使用 npm 下载 dayjs 软件包到本地项目文件夹中，引入到 index.js 中格式化日期打印，运行观察效果

4. 具体使用流程图：

   ![image-20230331155537983](images/image-20230331155537983.png)



### 小结

1. npm 软件包管理器作用？

   <details>
   <summary>答案</summary>
   <ul>
   <li>下载软件包以及管理版本</li>
   </ul>
   </details>

2. 初始化项目清单文件 package.json 命令?

   <details>
   <summary>答案</summary>
   <ul>
   <li>npm init -y</li>
   </ul>
   </details>

3. 下载软件包的命令？

   <details>
   <summary>答案</summary>
   <ul>
   <li>npm i 软件包名字</li>
   </ul>
   </details>

4. 下载的包会存放在哪里?

   <details>
   <summary>答案</summary>
   <ul>
   <li>当前项目下的 node_modules 中，并记录在 package.json 中</li>
   </ul>
   </details>



## 06.npm安装所有依赖

### 目标

掌握 npm 安装所有依赖功能



### 讲解

1. 我们拿到了一个别人编写的项目，但是没有 node_modules，项目能否正确运行？

   > 不能，因为缺少了项目需要的依赖软件包，比如要使用 dayjs 和 lodash 但是你项目里没有这个对应的源码，项目会报错的

   ![image-20230331162320775](images/image-20230331162320775.png)

1. 为何没有给我 node_modules？

   > 因为每个人在自己的本机使用 npm 下载，要比磁盘间传递要快（npm 有缓存在本机）

1. 如何得到需要的所有依赖软件包呢？

   > 直接在项目目录下，运行终端命令：npm i 即可安装 package.json 里记录的所有包和对应版本到本项目中的 node_modules

   ![image-20230331162341684](images/image-20230331162341684.png)



4. 需求：请在准备好的素材项目中，安装项目所有需要的依赖软件包，并运行 index.js 代码看是否正常！



### 小结

1. 当前项目中只有 package.json 没有 node_modules 怎么办?

   <details>
   <summary>答案</summary>
   <ul>
   <li>当前项目目录下，打开终端，执行 npm i 安装所有依赖软件包</li>
   </ul>
   </details>

2. 为什么 node_modules 不进行传递?

   <details>
   <summary>答案</summary>
   <ul>
   <li>因为用 npm 下载有缓存在本机，比磁盘之间传递要快</li>
   </ul>
   </details>



## 07.npm全局软件包-nodemon

### 目标

掌握本地软件包和全局软件包区别，体验 nodemon 的使用



### 讲解

1. 软件包区别：
   * 本地软件包：当前项目内使用，<span style="color: red;">封装属性和方法</span>，存在于 node_modules
   * 全局软件包：本机所有项目使用，<span style="color: red;">封装命令和工具</span>，存在于系统设置的位置


2. nodemon 作用：替代 node 命令，检测代码更改，自动重启程序
3. 使用：
   1. 安装：npm i nodemon -g （-g 代表安装到全局环境中）
   2. 运行：nodemon 待执行的目标 js 文件
4. 需求：使用 nodemon 命令来启动素材里准备好的项目，然后修改代码保存后，观察终端重启应用程序



### 小结

1. 本地软件包和全局软件包区别?

   <details>
   <summary>答案</summary>
   <ul>
   <li>本地软件包，作用在当前项目，封装属性和方法</li>
   <li>全局软件包，本地所有项目使用，封装命令和工具</li>
   </ul>
   </details>

2. nodemon 作用?

   <details>
   <summary>答案</summary>
   <ul>
   <li>替代 node 命令，检测代码更改，自动重启程序</li>
   </ul>
   </details>

3. nodemon 怎么用?

   <details>
   <summary>答案</summary>
   <ul>
   <li>先确保安装 npm i nodemon -g</li>
   <li>使用 nodemon 执行目标 js 文件</li>
   </ul>
   </details>



## 08.Node.js概念和常用命令总结

### 目标

把上面学的模块化语法，包的概念，常用命令进行总结



### 讲解

1. Node.js 模块化：把每个文件当做一个模块，独立作用域，按需加载，使用特定标准语法导出和导入使用

   > CommonJS 标准：一般应用在 Node.js 项目环境中
   >
   > ECMAScript 标准：一般应用在前端工程化项目中

   ![image-20230331170210113](images/image-20230331170210113.png)

   ![image-20230331170220045](images/image-20230331170220045.png)



2. Node.js 包：把模块文件，代码文件，其他资料聚合成一个文件夹就是包

   > 项目包：编写项目需求和业务逻辑的文件夹
   >
   > 软件包：封装工具和方法进行使用的文件夹（一般使用 npm 管理）
   >
   > * 本地软件包：作用在当前项目，封装的属性/方法，供项目调用编写业务需求
   > * 全局软件包：作用在所有项目，一般封装的命令/工具，支撑项目运行

   ![image-20230331170539931](images/image-20230331170539931.png)



3. Node.js 常用命令：

   ![image-20230331171411780](images/image-20230331171411780.png)



### 小结

1. 安装本地软件包的命令是什么？

   <details>
   <summary>答案</summary>
   <ul>
   <li>npm i 软件包名</li>
   </ul>
   </details>
