TypeScript是 JavaScript 的超集（JS 有的 TS 都有），在 JS 基础之上，为 JS 添加了类型支持，可以在任何运行 JavaScript 的地方运行。

从编程语言的动静来区分，TypeScript 属于静态类型(编译期做类型检查)的编程语言，JS 属于动态类型(执行期做类型检查)的编程语言。
## 安装
`Node.js/浏览器`只认识JS 代码，不认识 TS 代码。需要先将 TS 代码转化为 JS 代码，然后才能运行。
>安装编译TS的工具包
```shell
pnpm i -g typescript
```
typescript 包：用来编译 TS 代码的包，提供了` tsc `命令，实现了 TS -> JS 的转化。
验证是否安装成功：tsc –v（查看 typescript 的版本）。

![[Pasted image 20250920215747.png]]
>安装运行TS的工具
```shell
pnpm i -g ts-node
```
ts-node 包提供了 ts-node 命令,可以直接使用`ts-node <ts文件>`运行ts文件,其内部还是把ts转为js再运行的,但是不会生成额外的js文件
>在单纯的文件夹中运行可能会有问题，可以添加tsconfig.json配置文件(运行tsc --init即可自动生成)
```json
{
  "compilerOptions": {
    "target": "ES2016",
    "module": "CommonJS",
    "strict": false,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```
## 类型注解
```ts
let age: number = 18
```
>代码中的` : number `就是类型注解。

作用：为变量添加类型约束。比如，上述代码中，约定变量 age 的类型为 number（数值类型）。约定了什么类型，就只能给变量赋值该类型的值，否则，就会报错。
## 常用类型
可以将 TS 中的常用基础类型细分为两类：1 JS 已有类型 2 TS 新增类型。
1. JS 已有类型
原始类型：number/string/boolean/null/undefined/symbol。
对象类型：object（包括，数组、对象、函数等对象）。
2. TS 新增类型
联合类型、自定义类型（类型别名）、接口、元组、字面量类型、枚举、void、any,never 等。
### 原始类型
>这些类型，完全按照 JS 中类型的名称来书写。
```ts
let age: number = 18

let myName: string = 'test'

let isLoading: boolean = false

let a: null = null

let b: undefined = undefined

let s: symbol = Symbol()
```
### 数组类型
>对象类型，在 TS 中更加细化，每个具体的对象都有自己的类型语法
```ts
let number: number[] = [1, 2, 3]

let strings: Array<string> = ['a', 'b', 'c'] //不推荐
```
ts的数组可以有多个指定的元素类型
```ts
let arr: (number | string | boolean)[] = [1, 'a', true]
```
| （竖线）在 TS 中叫做==联合类型==（由两个或多个其他类型组成的类型，表示可以是这些类型中的任意一种）。
#### 元组
>元组类型是另一种类型的数组，它确切地知道包含多少个元素，以及特定索引对应的类型。
```ts
let position: [number, number, number] = [1, 2, 3]
```
### 类型别名
>类型别名（自定义类型）：为任意类型起别名。
```ts
type CustomArray = (number | string)[]

let arr1: CustomArray = [1, 'a']
```
类型别名（比如，此处的 CustomArray），可以是任意合法的==变量名称==。
### 函数类型
函数的类型实际上指的是：函数参数和返回值的类型。
为函数指定类型的两种方式：
1. 单独指定参数、返回值的类型 
```ts
function add(num1: number, num2: number): number { //函数声明
  return num1 + num2
}
const add2 = (num1: number, num2: number): number => { //函数表达式
  return num1 + num2
}
```
2. 同时指定参数、返回值的类型。
```ts
const add3: (num1: number, num2: number) => number = (num1, num2) => {
  return num1 + num2
}
```
>这种形式只适用于函数表达式。且函数类型中的参数名与实际箭头函数的参数名是按照位置对应的,实际叫什么没有影响

如果函数没有返回值,那么他的返回值类型为void
```ts
const f = (arg0: string): void => {
  console.log(arg0)
}
```
#### 可选参数
>在可传可不传的参数名称后面添加 ?（问号）。
```ts
const mySlice = (start?: number, end?: number): void => {
  console.log(start, end)
}
```
可选参数在使用时可传可不传
可选参数只能出现在参数列表的最后，也就是说可选参数后面不能再出现必选参数。
### 对象类型
>JS 中的对象是由属性和方法构成的，而 TS 中对象的类型就是在描述对象的结构（有什么类型的属性和方法）。
```ts
let person: { name: string; age: number; sayHi(): void } = {
  name: 'jack',
  age: 19,
  sayHi() {},
}
```
1. 直接使用 {} 来描述对象结构。属性采用属性名: 类型的形式；方法采用方法名(): 返回值类型的形式。
2. 如果方法有参数，就在方法名后面的小括号中指定参数类型（比如：greet(name: string): void）。
3. 在一行代码中指定对象的多个属性类型时，使用 ;（分号）来分隔。
	- 如果一行代码只指定一个属性类型（通过换行来分隔多个属性类型），可以去掉 ;（分号）。
	- 方法的类型也可以使用箭头函数形式（比如：{ sayHi: () => void }）。
4. 对象的属性或方法，也可以是可选的，也可以用可选属性。否则的话在赋值时就必须有这个属性
#### 接口
>当一个对象类型被多次使用时，一般会使用接口（interface）来描述对象的类型，达到复用的目的。
```ts
interface IPerson {
  name: string
  age: number
  sayHi: () => void
}

let person2: IPerson = {
  name: 'test',
  age: 18,
  sayHi: (): void => {},
}
```
1. 使用 interface 关键字来声明接口。
2. 接口名称（比如，此处的 IPerson），可以是任意合法的变量名称。
3. 声明接口后，直接使用接口名称作为变量的类型。
4. 因为每一行只有一个属性类型，因此，属性类型后没有 ;（分号）。

#### interface（接口）和 type（类型别名）的对比：
```ts
type IPerson2 = {
  name: string
  age: number
  sayHi: () => void
}

let person3: IPerson2 = {
  name: 'test',
  age: 18,
  sayHi: (): void => {},
}
```
**相同点**：
都可以给对象指定类型。
**不同点**：
接口，只能为对象指定类型。
类型别名，不仅可以为对象指定类型，实际上可以为任意类型指定别名。
#### 继承
>如果两个接口之间有相同的属性或方法，可以将公共的属性或方法抽离出来，通过继承来实现复用。
```ts
interface Point2D {
  x: number
  y: number
}

interface Point3D {
  x: number
  y: number
  z: number
}

interface Point3D2 extends Point2D {
  z: number
}
```
1. 使用 extends（继承）关键字实现了接口 Point3D 继承 Point2D。
2. 继承后，Point3D 就有了 Point2D 的所有属性和方法（此时，Point3D 同时有 x、y、z 三个属性）。
#### 访问器
访问器是类中用于控制对属性访问的特殊方法，包括：
 1. Getter 访问器（获取器）
	- 用于**读取**属性值
	- 以 `get` 关键字开头
	- 不接受参数，必须返回一个值
```
 class Person {
  private _name: string = 'huihui';
  
  // Getter 访问器
  get name(): string {
    console.log('正在读取 name 属性');
    return this._name;
  }
}

const person = new Person();
console.log(person.name); // 触发 getter，输出："正在读取 name 属性"，然后 "huihui"
```
>不一定就是对源对象属性的访问,可以像计算属性一样使用,get <变量名>,这样也可以直接用实例化对象.出来
```ts
class Rectangle {
  constructor(private _width: number, private _height: number) {}
  
  // 计算属性：面积
  get area(): number {
    return this._width * this._height;
  }
  
  // 验证逻辑
  set width(value: number) {
    if (value > 0) {
      this._width = value;
    }
  }
}

const rect = new Rectangle(10, 20);
console.log(rect.area); // 200，调用 getter
rect.width = 30;        // 调用 setter
```
 2. Setter 访问器（设置器）
- 用于**设置**属性值
- 以 `set` 关键字开头
- 接受一个参数，不返回值
```ts
class Person {
  private _name: string = 'huihui';
  private _age: number = 0;
  
  // Getter 访问器
  get name(): string {
    return this._name;
  }
  
  // Setter 访问器
  set name(newName: string) {
    if (newName.length > 0) {
      this._name = newName;
    }
  }
  
  set age(newAge: number) {
    if (newAge >= 0) {
      this._age = newAge;
    }
  }
}

const person = new Person();
person.name = '张三'; // 触发 setter
person.age = -5;     // 不会设置，因为年龄不能为负
```
### 类型推断
在 TS 中，某些没有明确指出类型的地方，TS 的类型推论机制会帮助提供类型。
换句话说：由于类型推论的存在，这些地方，类型注解可以省略不写！
发生类型推论的 2 种常见场景：
1. 声明变量并初始化时 
![[Pasted image 20250921184321.png]]
2. 决定函数返回值时。
![[Pasted image 20250921184347.png]]
### 类型断言

![[Pasted image 20250921192713.png]]

![[Pasted image 20250921192737.png]]
getElementById 方法返回值的类型是 HTMLElement，该类型只包含所有标签公共的属性或方法，不包含 a 标签特有的 href 等属性。因此，这个类型太宽泛（不具体），无法操作 href 等 a 标签特有的属性或方法。这种情况下就需要使用类型断言指定更加具体的类型。

![[Pasted image 20250921192819.png]]
1. 使用 as 关键字实现类型断言。
2. 关键字 as 后面的类型是一个更加具体的类型（HTMLAnchorElement 是 HTMLElement 的子类型）。
3. 通过类型断言，aLink 的类型变得更加具体，这样就可以访问 a 标签特有的属性或方法了
另一种语法，使用 <> 语法，这种语法形式不常用知道即可：

![[Pasted image 20250921193111.png]]
### 字面量类型
对于如下代码
```ts
let str1 = 'Hello TS'

const str2 = 'Hello TS'
```
通过 TS 类型推论机制，可以得他们的类型：
1. 变量 str1 的类型为：string。
2. 变量 str2 的类型为：=='Hello TS'==。
---
1. str1 是一个变量（let），它的值可以是任意字符串，所以类型为：string。
2. str2 是一个常量（const），它的值不能变化只能是 'Hello TS'，所以，它的类型为：'Hello TS'。
>此处的 'Hello TS'，就是一个==字面量类型==。也就是说某个特定的字符串也可以作为 TS 中的类型。除字符串外，任意的 JS 字面量（比如，对象、数字等）都可以作为类型使用。

使用模式：字面量类型配合==联合类型==一起使用。
使用场景：用来表示一组明确的可选值列表。
比如，在贪吃蛇游戏中，游戏的方向的可选值只能是上、下、左、右中的任意一个。

![[Pasted image 20250921194132.png]]
解释：参数 direction 的值只能是 up/down/left/right 中的任意一个。
优势：相比于 string 类型，使用字面量类型更加精确、严谨。
### 枚举
>定义一组命名常量。它描述一个值，该值可以是这些命名常量中的一个。
```ts
enum Direction {
  Up,
  Down,
  Left,
  Right,
}
// 形参 direction 的类型为枚举 Direction，那么，实参的值就应该是枚举 Direction 成员的任意一个。
function changeDirection(direction: Direction) {
  console.log(direction)
}
```
枚举的功能类似于字面量类型+联合类型组合的功能，也可以表示一组明确的可选值。
1. 使用 enum 关键字定义枚举。
2. 约定枚举名称、枚举中的值以大写字母开头。
3. 枚举中的多个值之间通过 ,（逗号）分隔。
4. 定义好枚举后，直接使用枚举名称作为类型注解。
5. 直接通过点（.）语法访问枚举的成员。
枚举成员是有值的，默认为：从 0 开始自增的数值。我们把，枚举成员的值为数字的枚举，称为：==数字枚举==。
当然，也可以给枚举中的成员初始化值。
```ts
enum Direction {
  Up = 10, // 10
  Down, // 11
  Left = 90, // 90
  Right, // 91
}
```
==字符串枚举==：枚举成员的值是字符串。
```ts
enum Direction {
  Up = 'UP',
  Down = 'DOWN',
  Left = 'LEFT',
  Right = 'RIGHT',
}
```
>字符串枚举没有自增长行为，因此，字符串枚举的每个成员必须有初始值
### any 类型(不推荐)
>当值的类型为 any 时，可以对该值进行任意操作，并且不会有代码提示。
```ts
let obj: any = { x: 0 }

obj.bar = 100

console.log(obj) // { x: 0, bar: 100 }
```
隐式具有 any 类型的情况：
1. 声明变量不提供类型也不提供默认值 
2. 函数参数不加类型。
### never
`never`是其他类型 （包括`null`和 `undefined`）的子类型，可以赋值给任何类型，代表从不会出现的值
但是没有类型是 never 的子类型，这意味着声明 `never` 的变量只能被 `never` 类型所赋值。
`never` 类型一般用来指定那些总是会抛出异常、无限循环
```js
let a:never;
a = 123; // 错误的写法

a = (() => { // 正确的写法
  throw new Error('错误');
})()

// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
```
**类型保护中的穷尽检查**
```ts
// 定义联合类型
type Shape = 
    | { kind: "circle"; radius: number }
    | { kind: "square"; side: number }
    | { kind: "triangle"; base: number; height: number };

// 计算面积函数
function getArea(shape: Shape): number {
    switch (shape.kind) {
        case "circle":
            return Math.PI * shape.radius ** 2;
        case "square":
            return shape.side ** 2;
        case "triangle":
            return 0.5 * shape.base * shape.height;
        default:
            // 这里的 shape 应该是 never 类型
            // 如果 Shape 有新的类型没处理，这里会报错！
            const _exhaustiveCheck: never = shape;
            throw new Error(`未知的形状: ${_exhaustiveCheck}`);
    }
}

// ✅ 现在如果我们添加新的形状类型：
type Shape = 
    | { kind: "circle"; radius: number }
    | { kind: "square"; side: number }
    | { kind: "triangle"; base: number; height: number }
    | { kind: "hexagon"; side: number }; // 新增类型

// ❌ TypeScript 会立即报错！
// 错误：类型 '{ kind: "hexagon"; side: number; }' 不能赋值给类型 'never'
```
这样比直接抛出错误的好处是静态检查,无需等到运行时才报错
### typeof
>TS 也提供了 typeof 操作符：可以在类型上下文中引用变量或属性的类型（类型查询）。
>可以根据已有变量的值，获取该值的类型，来简化类型书写。
```ts
let p = {x:1,y:2}
function formatPoint(point:{x:number,y:number}){
  console.log(point) // { x: 1, y: 2 }
}
let p = { x: 1, y: 2 }
function formatPoint(point: typeof p) {
  console.log(point) // { x: 1, y: 2 }
}
```
1. 使用 typeof 操作符来获取变量 p 的类型，结果与第一种（对象字面量形式的类型）相同。
2. typeof 出现在类型注解的位置（参数名称的冒号后面）所处的环境就在类型上下文（区别于 JS 代码）。
3. 注意：typeof 只能用来查询变量或属性的类型，无法查询其他形式的类型（比如，函数调用的类型）。
## 高级类型
### class类
```ts
class Person{}

const p = new Person()
```
1. 根据 TS 中的类型推论，可以知道 Person 类的实例对象 p 的类型是 Person。
2. TS 中的 class，不仅提供了 class 的语法功能，也作为一种类型存在。

![[Pasted image 20250921204343.png]]
>在严格模式下类的属性必须初始化或在构造函数中初始化,非严格模式则可以不初始化，接口则不能直接初始化
#### 构造函数
```ts
class Person {
  age: number
  gender = '男'
  constructor(age:number){
    this.age = age
  }
}

const p = new Person(18)
```
1. 成员初始化（比如，age: number）后，才可以通过 this.age 来访问实例成员。
2. 需要为构造函数指定类型注解，==否则会被隐式推断为 any==；构造函数不需要返回值类型。
#### 实例方法
```ts
class Person {
  age: number
  gender = '男'
  constructor(age: number) {
    this.age = age
  }
  toString(): string {
    return JSON.stringify(this)
  }
}

const p = new Person(18)
console.log(p.toString()) //{"gender":"男","age":18}
```
>接口中是能声明方法的类型,而不能实现方法
#### 继承&实现
类继承的两种方式：
1. extends（继承父类） 
2. implements（实现接口）。
>JS 中只有 extends，而 implements 是 TS 提供的。
```ts
class Animal {
  eat(){
    console.log('eat');
    
  }
}

interface IPerson {
  toString: () => string
}

class Person extends Animal implements IPerson{
  age: number
  gender = '男'
  constructor(age: number) {
    super();
    this.age = age
  }
  toString(): string {
    return JSON.stringify(this)
  }
}

const p = new Person(18)
p.eat()
```
>继承的关键字必须在实现的关键字之前
>实现一个接口同时也要给声明接口中的所有属性并赋值,继承需要在构造函数中调用super()来调用父类的构造函数
#### 可见性修饰符
>类成员可见性：可以使用 TS 来控制 class 的方法或属性对于 class 外的代码是否可见。

可见性修饰符包括：
1. public（公有的） :公有成员可以被任何地方访问，默认可见性,不写就是public。
2. protected（受保护的）:仅对其声明所在类和子类中（非实例对象）可见,在子类的方法内部可以通过 this 来访问父类中受保护的成员，但是，对实例不可见
3. private（私有的）:表示私有的，只在当前类中可见，对实例对象以及子类也是不可见的,私有的属性或方法只在当前类中可见，对子类和实例对象也都是不可见的
#### readonly
除了可见性修饰符之外，还有一个常见修饰符就是：readonly（只读修饰符）。
>readonly：表示只读，用来==防止在构造函数之外对属性进行赋值==。
```ts
class Person {
  readonly age: number = 18
  constructor(age: number) {
    this.age = age
  }
}
```
1. 使用 readonly 关键字修饰该属性是只读的，注意==只能修饰属性不能修饰方法==。
2. 注意：属性 age 后面的类型注解（比如，此处的 number）如果不加，则 age 的类型为 18 （字面量类型）。
3. 接口或者 {} 表示的对象类型，也可以使用 readonly。
### 类型兼容性
两种类型系统：
1. Structural Type System（结构化类型系统） 
2. Nominal Type System（标明类型系统）。
TS 采用的是结构化类型系统，也叫做 duck typing（鸭子类型），类型检查关注的是值所具有的形状。也就是说，在结构类型系统中，如果两个对象具有相同的形状，则认为它们属于同一类型。
```ts
class A {
  x: number
  y: number
}

class B {
  x: number
  y: number
}

const a: B = new A()
const b: A = new B()

function f(val: A) {
  console.log(val) // B {}
}

f(b)
```
1.  TS 是结构化类型系统，在赋值时只检查 目标类型 和 值的类型 的结构是否相同（相同，都具有 x 和 y 两个属性，属性类型也相同）。
2. 但是，如果在 Nominal Type System 中（比如，C#、Java 等），它们是不同的类，类型无法兼容。
在结构化类型系统中，如果两个对象具有相同的形状，则认为它们属于同一类型，这种说法并不准确。
更准确的说法：对于对象类型来说，y 的成员至少与 x 相同，则 x 兼容 y（成员多的可以赋值给少的，~~向上造型~~）
```ts
class A {
  x: number
  y: number
}

class B {
  x: number
  y: number
  z: number
}

const a: B = new B() // 这里再赋值为A就会报错说少属性z
const b: A = new B()

function f(val: A) {
  console.log(val) // B {}
}

f(b)
```
>除了 class 之外，TS 中的其他类型也存在相互兼容的情况，包括：接口兼容性，函数兼容性 等。

接口之间的兼容性，类似于 class。并且，class 和 interface 之间也可以兼容。
```ts
interface Point2D {
  x: number
  y: number
}

interface Point {
  x: number
  y: number
}

let p1: Point
let p2: Point2D = p1

interface Point3D {
  x: number
  y: number
  z: number
}
let p3: Point3D
p2 = p3

class Point3D2 {
  x: number
  y: number
  z: number
}

p2 = new Point3D2()
```
函数之间兼容性比较复杂，需要考虑：1 参数个数 2 参数类型 3 返回值类型。
1. 参数个数，参数多的兼容参数少的（或者说，==参数少的可以赋值给多的==）。
```ts
type F1 = (arg0: number) => void
type F2 = (arg0: number, arg1: number) => void

let f1: F1
let f2: F2 = f1
```
>最常见的就是各种框架和函数作为参数传入的回调函数,如Array的forEach(()=>{ ... }),原回调函数类型为`(value: string, index: number, array: string[]) => void`,但是我们用的时候并不需要传入一个带这么多参数的箭头函数,这也是类型兼容的一种体现,而且因为回调函数是有类型的,所以会自动根据参数顺序推断出类型
2. 参数类型，相同位置的参数类型要相同（原始类型）或兼容（对象类型）。
![[Pasted image 20250921215418.png]]
![[Pasted image 20250921215707.png]]
>此处与前面讲到的接口兼容性正好==相反==,可以看做将对象拆开，把每个属性看做一个个参数，则，参数少的（f2）可以赋值给参数多的（f3）。
3. 返回值类型，只关注返回值类型本身即可：
![[Pasted image 20250921215954.png]]
	1. 如果返回值类型是原始类型，此时两个类型要相同，比如，左侧类型 F5 和 F6。
	2. 如果返回值类型是对象类型，此时成员多的可以赋值给成员少的，比如，右侧类型 F7 和 F8。
### 交叉类型
>交叉类型（&）：功能类似于接口继承（extends），用于组合多个类型为一个类型（常用于对象类型）。
```ts
interface Person {
  name: string
}
interface Contact {
  phone: string
}
type PersonDetail = Person & Contact
let obj: PersonDetail = {
  name: 'test',
  phone: '110',
}
```
使用交叉类型后，新的类型 PersonDetail 就同时具备了 Person 和 Contact 的所有属性类型。
交叉类型（&）和接口继承（extends）的对比：
- 相同点：都可以实现对象类型的组合。
- 不同点：两种方式实现类型组合时，对于同名属性之间，处理类型冲突的方式不同,继承时会将子类的方法作为父类的重写或实现,所以函数类型需要兼容,而交叉类型会把同名属性的类型更改为两个类的联合类型(string | number)
### 泛型
>泛型是可以在保证类型安全前提下，让函数等与多种类型一起工作，从而实现复用，常用于：函数、接口、class 中。
```ts
function fun<Type>(value: Type): Type {
  return value
}
console.log(typeof fun<number>(10)); // number

console.log(typeof fun<string>('10')) // string
```
1. 语法：在函数名称的后面添加 <>（尖括号），尖括号中添加类型变量，比如此处的 Type。
2. 类型变量 Type，是一种特殊类型的变量，它处理类型而不是值。
3. 该类型变量相当于一个类型容器，能够捕获用户提供的类型（具体是什么类型由用户调用该函数时指定）。
4. 因为 Type 是类型，因此可以将其作为函数参数和返回值的类型，表示参数和返回值具有相同的类型。
5. 类型变量 Type，可以是任意合法的变量名称。
6. 调用：在函数名称的后面添加 <>（尖括号），尖括号中指定具体的类型，比如，此处的 number。
7. 当传入类型 number 后，这个类型就会被函数声明时指定的类型变量 Type 捕获到。
8. 此时，Type 的类型就是 number，所以，函数 id 参数和返回值的类型也都是 number。
9. 在调用泛型函数时，可以省略 <类型> 来简化泛型函数的调用。
10. 此时，TS 内部会采用一种叫做==类型参数推断==的机制，来根据传入的实参自动推断出类型变量 Type 的类型。
11. 比如，传入实参 10，TS 会自动推断出变量 num 的类型 number，并作为 Type 的类型。
#### 泛型约束
>泛型约束：默认情况下，泛型函数的类型变量 Type 可以代表多个类型，这导致无法访问任何属性。此时，就需要为泛型添加约束来收缩类型（缩窄类型取值范围）。

添加泛型约束收缩类型，主要有以下两种方式：
1. 指定更加具体的类型 
```ts
function fun<Type>(value: Type[]): Type[] {
  console.log(value.length)
  return value
}
```
>比如，将类型修改为 `Type[]`（Type 类型的数组），因为只要是数组就一定存在 length 属性，因此就可以访问数组的属性length了。
2. 添加约束。
```ts
interface ILength {
  length: number
}

function fun<Type extends ILength>(value: Type): Type {
  console.log(value.length)
  return value
}
```
1. 创建描述约束的接口 ILength，该接口要求提供 length 属性。
2. 通过 extends 关键字使用该接口，为泛型（类型变量）添加约束。
3. 该约束表示：传入的类型必须具有 length 属性。
>传入的实参（比如，数组）只要有 length 属性即可，这也符合前面讲到的接口的类型兼容性。

泛型的类型变量可以有多个，并且类型变量之间还可以约束（比如，第二个类型变量受第一个类型变量约束）。
```ts
function fun<Type, Key extends keyof Type>(value: Type, key: Key) {
  return value[key]
}

let person = { name: 'jack', age: 18 }

console.log(fun(person, 'name')) // jack
```
1. 添加了第二个类型变量 Key，两个类型变量之间使用（,）逗号分隔。
2. ==keyof== 关键字接收一个对象类型，生成其键名称（可能是字符串或数字）的联合类型。
3. 本示例中 keyof Type 实际上获取的是 person 对象==所有键的联合类型==(是键名的字面量类型的联合类型,不是键的类型的联合类型)，也就是：'name' | 'age'。
4. 类型变量 Key 受 Type 约束，可以理解为：Key 只能是 Type 所有键中的任意一个，或者说只能访问对象中存在的属性。
#### 泛型接口
>接口也可以配合泛型来使用，以增加其灵活性，增强其复用性。
```ts
interface IdFunc<Type> {
  id: (value: Type) => Type
  ids: () => Type[]
}

let obj: IdFunc<number> = {
  id(value) {
    return value
  },
  ids() {
    return [1, 3, 5]
  },
}
```
1. 在接口名称的后面添加 <类型变量>，那么，这个接口就变成了泛型接口。
2. 接口的类型变量，对接口中所有其他成员可见，也就是接口中所有成员都可以使用类型变量。
3. 使用泛型接口时，需要显式指定具体的类型（比如，此处的 `IdFunc<nunber>`）。
4. 此时，id 方法的参数和返回值类型都是 number；ids 方法的返回值类型是 `number[]`。
#### 泛型类
>类似于泛型接口，在 class 名称后面添加 <类型变量>，这个类就变成了泛型类。
```ts
class GenericNumber<NumType> {
  defaultValue: NumType
  add: (x: NumType, y: NumType) => NumType
}

const myNum = new GenericNumber<number>()
myNum.defaultValue = 10
```
类似于泛型接口，在创建 class 实例时，在类名后面通过 <类型> 来指定明确的类型
### 索引签名类型
>当无法确定对象中有哪些属性（或者说对象中可以出现任意多个属性），此时，就用到索引签名类型了。
```ts
interface AnyObject {
  [key: string]: number
}

let obj: AnyObject = {
  a:1,
  b:2
}
```
1. 使用 `[key: string]` 来约束该接口中允许出现的属性名称。表示==只要是 string 类型的属性名称==，都可以出现在对象中。这个key的类型更像是map的key类型,也可以是number,这样定义出来的类使用起来就像是一个数组,可以使用`[1]`访问值
2. 这样，对象 obj 中就可以出现任意多个属性（比如，a、b 等）。
3. key 只是一个占位符，可以换成任意合法的变量名称。
4. 隐藏的前置知识：JS 中对象（{}）的键是 string 类型的。
### 映射类型
>映射类型：基于旧类型创建新类型（对象类型），减少重复、提升开发效率。
```ts
type PropKeys = 'x' | 'y' | 'z'
type Type1 = { [Key in PropKeys]: number }

// 等效于

type Type1 = {
  x: number
  y: number
  z: number
}
```
1. 映射类型是基于索引签名类型的，所以，该语法类似于索引签名类型，也使用了 `[]`。
2. Key in PropKeys 表示 Key 可以是 PropKeys 联合类型中的任意一个，类似于 forin(let k in obj)。
3. 使用映射类型创建的新对象类型 Type2 和类型 Type1 结构完全相同。
4. 注意：映射类型只能在类型别名中使用，不能在接口中使用。
### 索引查询（访问）类型
>对于一个对象,可以使用<类名>`[属性名]`的方式来获取这个属性的类型,可以通过这个类型来声明变量
```ts
type Props = { a: number; b: string; c: boolean }

let test: Props['a'] // number

let test2: Props['a' | 'b'] // string | number

let test3: Props[keyof Props] // string | number | boolean
```
`[]` 中的属性必须存在于被查询类型中，否则就会报错。
## 工具类
### 处理联合类型
#### Extract<Type,Keys> (提取/包括)
>一般用于处理联合类型
```ts
// 处理联合类型
type Test1 = '1' | '2' | '3'

const obj: Extract<Test1, '1' | '2'> = '1'; // 1,2 OK 赋值3就会error
```
**源码**
```ts
// Extract实现源码 原理很简单
type Extract<T, U> = T extends U ? T : never;
```
#### Exclude<Type,Keys> (排除/不包括)
>和 Extract 正好相反，也是用于处理联合类型
```ts
// 处理联合类型
type Test1 = '1' | '2' | '3'

const obj: Exclude<Test1, '1' | '2'> = '3'; // 3 OK 赋值1,2就会error
```
**源码**
```ts
// Exclude源码
type Exclude<T, U> = T extends U ? never : T;
```
#### `NonNullable<Type>` (类型中排除 `null` 和 `undefined`)
```ts
type MaybeString = string | null | undefined;  
  
const value: MaybeString = getSomeStringValue(); // 假设这个函数可能返回一个字符串、null 或 undefined  
  
// 使用 NonNullable 确保 value 不会是 null 或 undefined  
const nonNullableValue: NonNullable<MaybeString> = value!; // 使用 ! 断言操作符，或者通过其他方式确保值不为 null 或 undefined  
  
// 现在，nonNullableValue 的类型是 string，因为我们已经排除了 null 和 undefined  
console.log(nonNullableValue.length); // 这是安全的，因为我们知道 nonNullableValue 一定是字符串

// 其实就是某些场景绝对为了排除null,undefined的类型才用的
```
**源码**
```ts
// 源码
/**
 * Exclude null and undefined from T
 */
type NonNullable<T> = T & {}; // 源码的这一句写的很有意思，泛型参数T和{}的交集就默认排除了`null` 和 `undefined`
```
### 处理一般类型
#### Pick<Type, Keys> (采集)
>从 Type 中选择一组属性来构造新类型。
```ts
interface Props {
  id: string
  title: string
  children: number[]
}

type PickyProps = Pick<Props, 'id' | 'title'>

type PickyProps = {
  id: string
  title: string
}
```
**源码**
```ts
// Pick 的源码
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};
```
1. Pick 工具类型有两个类型变量：1 表示选择谁的属性 2 表示选择哪几个属性。
2. 其中第二个类型变量，如果只选择一个则只传入该属性名即可。
3. 第二个类型变量传入的属性只能是第一个类型变量中存在的属性。
4. 构造出来的新类型 PickProps，只有 id 和 title 两个属性类型。
#### Omit<Type,Keys> (省略/剔除)
>顾名思义 可以剔除 已定义对象中 自己不需要的一部分形成新的定义类型。
```ts
interface UserObj {
    readonly name: string; // readonly 只读属性 只能初始化定义 不能二次赋值
    age: number;
    id: number;
    sex: 0 | 1;
    address: string;
    weight: number;
}

// 剔除省略自己不需要的
type Person = Omit<UserObj, "age" | "sex"  | "address" | "weight">;

// 此时Person 等同于 Person1

interface Person1 {
    readonly name: string;
    id: number;
}
```
**源码**
```ts
// Omit 的源码
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```
### 修改属性元类型
#### `Partial<Type>`
>用来构造（创建）一个类型，将 Type 的所有属性设置为可选。
```ts
interface Props {
  id: string
  children: number[]
}

type PartialProps = Partial<Props>

// 等效于
type PartialProps = {
  id?: string
  children?: number[]
}
```
**源码**
```ts
type Partial<T> = {
    [P in keyof T]?: T[P];
};
```
构造出来的新类型 PartialProps 结构和 Props 相同，但所有属性都变为可选的。
#### `Required<Type>` (必选的)
>Required 和 Partial刚好相反,可把定义好的对象（包含 必选+可选项）类型全部转化为 **必选项**
```ts
// 已有定义类型Person
interface Person {
    name: string;
    age: number;
    id?: number;
    sex?: 0 | 1;
}

// 使用方法
const newObj: Required<Person> = {
    name: '张三',
    age: 1,
    id: 1,
    sex: 1
};

// Required<Person>等同于 NewPerson
interface NewPerson {
    name: string;
    age: number;
    id: number;
    sex: 0 | 1;
}
```
**源码**
```ts
/**
 * Make all properties in T required
 */
type Required<T> = {
    [P in keyof T]-?: T[P]; // -?" 意思是移除可选属性
};
```
#### `Readonly<Type>` (转化只读)
>用来构造一个类型，将 Type 的所有属性都设置为 readonly（只读）。
```ts
interface Props {
  id: string
  children: number[]
}

type ReadonlyProps = Readonly<Props>

// 等效于

type ReadonlyProps = {
  readonly id: string
  readonly children: number[]
}
```
**源码**
```ts
/**
 * Make all properties in T readonly
 */
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};
```
构造出来的新类型 ReadonlyProps 结构和 Props 相同，但所有属性都变为只读的。
### 推导新类型
#### Record<Keys,Type>
>构造一个对象类型，属性键为 Keys，属性类型为 Type。
```ts
type RecordObj = Record<'a' | 'b' | 'c', string[]>

// 等效于

type RecordObj = {
  a: string[]
  b: string[]
  c: string[]
}
```
1. Record 工具类型有两个类型变量：1 表示对象有哪些属性 2 表示对象属性的类型。
2. 构建的新对象类型 RecordObj 表示：这个对象有三个属性分别为a/b/c，属性值的类型都是 `string[]`。
#### `ReturnType<Type>` (函数的类型推导返回)
>即获取函数返回值的对应类型
```ts
// 正确用法
const myFun = () => ({
    name: 'zhangsan',
    age: 233,
    sex: '1',
    tel: 123456789,
    fun: () => {
        return 233;
    },
    arr: [1,2,3,4,5,6]
});


type Test2 = ReturnType<typeof myFun>; // OK 鼠标放到Test2上可以发现 已推导出了myFun函数的返回类型。

// 错误用法
const someValue = 42;  
type InvalidReturnType = ReturnType<typeof someValue>; // error错误！someValue 不是一个函数。
```
**源码**
```ts
// ReturnType源码
/**
 * Obtain the return type of a function type
 */
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```
## 类型声明文件
>所有的三方库都是编译为js再发布的,但是使用时却有类型提示和保护,这是因为类型声明文件==为js提供了类型信息==

TS 中有两种文件类型：1 .ts 文件 2 .d.ts 文件。
.ts 文件：
1. 既包含类型信息又可执行代码。
2. 可以被编译为 .js 文件，然后，执行代码。
3. 用途：编写程序代码的地方。
---
.d.ts 文件：
1. 只包含类型信息的类型声明文件,不能出现可执行代码。
2. 不会生成 .js 文件，仅用于提供类型信息。
3. 用途：为 ==JS== 提供类型信息。
>总结：.ts 是 implementation（代码实现文件）；.d.ts 是 declaration（类型声明文件）。

如果要为 JS 库提供类型信息，要使用 .d.ts 文件。
### 使用
类型声明文件的使用包括以下两种方式：
1. 使用已有的类型声明文件
2. 创建自己的类型声明文件
已有的类型声明文件包括内置的类型声明文件以及第三方库的类型声明文件,这些都会自动导入并提供类型提示
#### DefinitelyTyped
>[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) 是一个 github 仓库，用来提供高质量 TypeScript 类型声明。

有的三方库的类型声明文件需要通过DefinitelyTyped来获取
通过 `npm/yarn` 来下载该仓库提供的 TS 类型声明包，这些包的名称格式为：`@types/*`。
安装lodash的类型声明文件
```shell
npm i --save-dev @types/lodash
```
>在实际项目开发时，如果你使用的第三方库没有自带的声明文件，VSCode 会给出明确的提示。
### 创建自己的类型声明文件
#### 项目内共享类型
>如果多个 .ts 文件中都用到同一个类型，此时可以创建 .d.ts 文件提供该类型，实现类型共享

操作步骤：
1. 创建 index.d.ts 类型声明文件。
2. 创建需要共享的类型，并使用 export 导出（TS 中的类型也可以使用 import/export 实现模块化功能）。
3. 在需要使用共享类型的 .ts 文件中，通过 import 导入即可（.d.ts 后缀导入时，直接省略）。
```ts
type Props = { x: number; y: number }

export { Props }
```
#### 为已有 JS 文件提供类型声明
>类型声明文件的编写与模块化方式相关，不同的模块化方式有不同的写法,而js模块化方法有很多,这里以ESModule(import/export)为例

在导入 .js 文件时，TS 会自动加载与 .js 同名的 .d.ts 文件，以提供类型声明。
declare 关键字：用于类型声明，为其他地方（比如，.js 文件）已存在的变量声明类型，而不是创建一个新的变量。
1. 对于 type、interface 等这些明确就是 TS 类型的（只能在 TS 中使用的），可以省略 declare 关键字。
2. 对于 let、function 等具有双重含义（在 JS、TS 中都能用），应该使用 declare 关键字，明确指定此处用于类型声明
util.js
```js
let count = 10
let songName = '痴心绝对'
let position = {
  x: 0,
  y: 0
}

function add(x, y) {
  return x + y
}

function changeDirection(direction) {
  console.log(direction)
}

const formatPoint = point => {
  console.log('当前坐标：', point)
}

export { count, songName, position, add, changeDirection, formatPoint }
```
util.d.ts
```ts
declare let count: number
declare let songName: string

interface Point {
  x: number
  y: number
}

declare let position: Point

declare function add(x: number, y: number): number

declare function changeDirection(direction: 'up' | 'down' | 'left' | 'right'): void

declare function formatPoint(point: Point): void

export { count, songName, position, add, changeDirection, formatPoint }
// 这里的模块方案要和js一致
```
>注意两个文件文件名要相同,对与函数只要函数名一致就可以,用的是表达式形式还是function的形式没有影响

## Decorator
Ts的装饰器与js的不完全相同,且需要手动开启(tsconfig.json)
```json
{
    "compilerOptions": {
        "target": "ES5",
        "experimentalDecorators": true
    }
}
```
### 区别
**TypeScript 装饰器（传统/实验性）**：
```ts
// 类装饰器
function classDecorator(constructor: Function) {}

// 方法装饰器  
function methodDecorator(target: any, propertyKey: string, descriptor: PropertyDescriptor) {}

// 参数装饰器
function paramDecorator(target: any, propertyKey: string, parameterIndex: number) {}
```
例子
```ts
function log(target: any, methodName: string, descriptor: PropertyDescriptor) {
  const originalMethod = descriptor.value;
  descriptor.value = function(...args: any[]) {
    console.log(`调用 ${methodName}，参数:`, args);
    return originalMethod.apply(this, args);
  };
}

class MyClass {
  @log
  greet(name: string) {
    return `Hello, ${name}!`;
  }
}
```
**JavaScript 装饰器（新标准）**：
```js
// 所有装饰器统一签名
function decorator(value, context) {
  // context 包含元数据信息
  const { kind, name, addInitializer } = context;
}
```
例子
```js
function log(value, { kind, name }) {
  if (kind === 'method') {
    return function(...args) {
      console.log(`调用 ${name}，参数:`, args);
      return value.call(this, ...args);
    };
  }
}

class MyClass {
  @log
  greet(name) {
    return `Hello, ${name}!`;
  }
}
```