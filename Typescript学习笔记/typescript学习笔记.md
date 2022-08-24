

>`=== 参考文献 ===`
>
>Typescript中文网站  https://www.tslang.cn/docs/handbook/
>
>聊聊TypeScript类型兼容，协变、逆变、双向协变以及不变性  https://segmentfault.com/a/1190000022724899
>
>【typescript】ts协变逆变双向协变高级用法 https://blog.csdn.net/yehuozhili/article/details/108567297
>
>深入学习js之浅谈设计模式（混入）https://blog.csdn.net/qq_36936155/article/details/77431947
>
>在 TS 中如何实现类型保护？类型谓词了解一下 https://cloud.tencent.com/developer/article/1600711

## tsc命令

```sh
tsc --help # 查看帮助文档
tsc --all  # 查看全部编译器选项
```

tsc命令的帮助文档：
```sh
Version 4.3.2
Syntax:   tsc [options] [file...]      

Examples: tsc hello.ts
          tsc --outFile file.js file.ts
          tsc @args.txt
          tsc --build tsconfig.json    

Options:
 -h, --help                                         # 打印命令有关的信息
 -w, --watch                                        # 通过监听模式编译ts文件
 --pretty                                           # 格式化输出日志信息
 --all                                              # 展示所有的编译器选项
 -v, --version                                      # 打印编译器的版本
 --init                                             # 初始化一个ts项目并创建一个tsconfig配置文件
 -p FILE OR DIRECTORY, --project FILE OR DIRECTORY  # 使用给定路径的配置文件或者给定路径下的tsconfig.json配置文件编译项目
 -b, --build                                        # 执行一个或多个项目及其依赖的构建，如果构建已经过期
 -t VERSION, --target VERSION                       # 指定ES的目标版本: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', 'ES2021', or 'ESNEXT'.
 -m KIND, --module KIND                             # 指定模块代码的生成风格: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'.
 --lib                                              # 指定在编译过程中引用的依赖文件
                                                      'es5' 'es6' 'es2015' 'es7' 'es2016' 'es2017' 'es2018' 'es2019' 'es2020' 'es2021' 'esnext' 'dom' 'dom.iterable' 'webworker' 'webworker.importscripts' 'webworker.iterable' 'scripthost' 'es2015.core' 'es2015.collection' 'es2015.generator' 'es2015.iterable' 'es2015.promise' 'es2015.proxy' 'es2015.reflect' 'es2015.symbol' 'es2015.symbol.wellknown' 'es2016.array.include' 'es2017.object' 'es2017.sharedmemory' 'es2017.string' 'es2017.intl' 'es2017.typedarrays' 'es2018.asyncgenerator' 'es2018.asynciterable' 'es2018.intl' 'es2018.promise' 'es2018.regexp' 'es2019.array' 'es2019.object' 'es2019.string' 'es2019.symbol' 'es2020.bigint' 'es2020.promise' 'es2020.sharedmemory' 'es2020.string' 'es2020.symbol.wellknown' 'es2020.intl' 'es2021.promise' 'es2021.string' 'es2021.weakref' 'esnext.array' 'esnext.symbol' 'esnext.asynciterable' 'esnext.intl' 'esnext.bigint' 'esnext.string' 'esnext.promise' 'esnext.weakref'
 --allowJs                                          # 允许javascript文件被编译
 --jsx KIND                                         # 指定JSX代码生成的风格: 'preserve', 'react-native', 'react', 'react-jsx' or 'react-jsxdev'.
 -d, --declaration                                  # 生成对应的.d.ts文件
 --declarationMap                                   # 为每一个.d.ts文件生成对应的sourcemap
 --sourceMap                                        # 生成对应的.map文件
 --outFile FILE                                     # 指定编译结果输出的文件     
 --outDir DIRECTORY                                 # 指定编译结果输出的目录
 --removeComments                                   # 在编译输出结果中移除注释
 --noEmit                                           # 只编译，不输出编译结果
 --strict                                           # 启用严格模式的所有校验
 --noImplicitAny                                    # 启用严格模式-禁用any类型推断
 --strictNullChecks                                 # 启用严格模式-空值检查
 --strictFunctionTypes                              # 启用严格模式-函数类型检查
 --strictBindCallApply                              # 启用严格模式-禁用bind、call和apply函数
 --strictPropertyInitialization                     # 启用严格模式-非可选的属性值未初始化将报错
 --noImplicitThis                                   # 启用严格模式-this被推断成any时报错
 --alwaysStrict                                     # 启用严格模式-编译后的js也使用严格模式
 --noUnusedLocals                                   # 有未使用的局部变量时报错
 --noUnusedParameters                               # 有未使用的参数时报错
 --noImplicitReturns                                # 对于有返回值的函数，并非所有分支都有return时报错
 --noFallthroughCasesInSwitch                       # 有贯通switch case语句时报错
 --types                                            # 引入类型声明文件
 --esModuleInterop                                  # 兼容commonJS导出依赖的方式（没有导出default）
 @<file>                                            # 从文件中插入控制台选项和文件
```

## 基础类型

基础类型：boolean、number、string、Array、Tuple、枚举

数组类型的两种写法：[]写法和Array<>写法

元组类型：表示一个已知元素数量和类型的数组，各元素的类型不必相同。

```typescript
let x: [string, number];
x = ['hello',123]; // OK
x = [123, 'hello']; // ERROR
```

枚举类型：

```ts
enum Color {Red, Green, Blue};
let c:Color = Color.Green;
```

默认情况下从0开始编号，也可以使用 `=` 手动指定元素的编号

any：跳过类型检查

## 变量声明

区别`let`、`const`、`var`，尽量使用`let`、`const`代替`var`

### 解构

什么是解构？

一般情况下左值只能是一个变量，但是解构实现了左值可以是多个变量

解构赋值：

```ts
// 数组的解构赋值
let input = [1,2];
let [a,b] = input;

// 已声明变量的解构赋值
let c=1,d=2;
let [e,f] = [c,d];

// 参数列表的解构赋值
function([param1,param2]:[number,number]){}

// 数组中使用 ... 语法赋值
let arr = [123,...input];

// 使用解构赋值时，多余的元素将会被忽略
let [first] = [1,2,3,4];
let [,second,,fourth] = [1,2,3,4];
```

对象解构：

```ts
// 解构对象
let obj = {a:1,b:2,c:3};
let {a,b,c} = obj;

// 使用未声明的对象解构
let {a1,b1,c1} = {a1:1,b1:2,c1:3};
({a1,b1,c1}) = {a1:'1',b1:'2',c1:'3'};

// 使用 ... 语法创建/重写剩余属性值
let x = {apple:1,bee:2,car:3};
let {apple,...x0} = {apple:'1',bee:'2',car:'3'}; // 在这个例子中创建了对象x0，并将剩余属性bee和car赋值给x0
```

属性值命名：

```ts
let {a:number1,b:number2} = {a:1,b:2};

// 这里的冒号用来指示别名，并不是指示类型
// 如果要指示类型，则应该这样来表达
let {a:number1,b:number2}:{a:number,b:number} = {a:1,b:1};
```

默认值：

```ts
let {a,b=1001} = {a:1};
```

函数声明中使用解构：

```ts
type C = {a:string,b?:number}
function f({a,b}:C):void {
    // ...
}
```

通常情况下更多的是指定默认值，解构默认值有些棘手。首先需要在默认值之前设置其格式

```ts
function f({a="",b=0}={}):void {
    // ...
}
```

其次，你需要知道在解构属性上给予一个默认或可选的属性用来替换主初始化列表。要知道C的定义有一个b可选属性：

```ts
function f({a,b=0}={a:""}):void {
    // ...
}

// 该函数的等同于下面的结构
let defaultObj = {a:""};
function f(obj=defaultObj):void{
    let {a,b=0} = obj;
    // ...
}
```

### 展开

使用 `...` 将一个数组展开为另一个数组，或将一个对象展开为另一个对象。
```ts
let first = [1,2];
let second = [3,4];
let bothPlus = [0,...first,...second,5];
```

还可以展开对象，操作是类似的。

展开操作时，后面的展开会将前面已经定义的属性值覆盖。

对象展开还有一些意想不到的限制。

- 首先，它仅包含对象自身可枚举属性，当你展开一个对象实例时，会丢失其方法。
- 其次，ts编辑器不允许展开泛型函数上的类型参数。

## 接口

ts的核心原则之一就是对值所具有的结构进行类型检查。在TS中，接口的作用就是为这些类型命名和为你的代码或者第三方代码订立契约。

### 定义一个接口
```ts
interface Login{
    username:string;
    password:string;
}

function dosomething(hello:Hello){
    console.log(`username = ${hello.username}`);
    console.log(`password = ${hello.password}`);
}
```

注意，类型检查器不会检查属性值的顺序。

可选属性：
```ts
interface Login{
    username:string;
    password:string;
    nickname?:string;
}
```

### 只读属性：

```ts
interface Point{
    readonly x:number;
    readonly y:number;
}

// 可以通过赋值一个对象字面量来构造一个Point。复制后，只读属性不能被改变了
let p1:Point = {x:111,y:222};

p1.x = 5;//ERROR
```

【区别】`readonly`和`与const`
最简单判断这两个关键字的方法是看它作为变量使用还是作为一个属性使用。

### 额外属性检查：

接口中已经定义了一些属性，但赋值时如果出现接口中没有定义的属性值，那么“额外属性检查”会认为这是一个错误。

避免这个问题的方法：

- 使用类型断言

```ts
interface Hello{
    username?:string;
    password?:string;
}

let a:Hello = {address:"123"} as Hello;
```

- 添加一个字符串索引签名

```ts
interface Hello{
    username?:string;
    password?:string;
    [propName:string]:any;
}
```

- 将对象赋值给另外一个变量

### 函数类型：

接口的调用签名：
```ts
interface Hello{
    (username:string,password:string):boolean;
}
```

这样定义后，能够使用像其他接口一样使用函数类型的接口。

```ts
let hello:Hello = function(username:string,password:string){
    // ...
}
```

对于函数类型的类型检查，不需要函数的参数名与接口粒定义的名称相匹配。

如果不想指定类型，ts类型系统将根据接口的类型推断出函数的类型

### 可索引的类型

接口可以定义那些“通过索引得到”的类型

```ts
interface StringArray{
    [index:number]:string;
}

let arr: = ["123","456"];
```

ts支持两种索引签名：字符串和数字。可以同时使用这两种签名，但是数字索引的返回值必须是字符串索引返回值类型的子类型。

可以将索引签名设为readonly，以防止对索引的修改。

### 类类型

```ts
interface HelloService{
    currentTime:Date;
}

class HelloServiceImpl implements HelloService{
    currentTime:Date;
    constructor(h:number,m:number){}
    setTime(time:Date){
        this.currentTime = time;
    }
}
```

接口描述了类的公有部分，并不会检查类是否具有某些私有部分

类具有静态部分和实例部分，接口只检查实例部分的类型

接口可以实现多继承

### 混合类型

接口能够描述JS中丰富的类型，有时希望对象同时具有上面提到的多种类型，这时可以采用混合类型

### 接口继承类

接口可以继承类，但是只继承类的结构，不继承类的实现。

## 类

类的定义：

```ts
class Hello{
    private username:string;
    private password:string;
    public constructor(username:string,password:string){
        this.username = username;
        this.password = password;
    }
    public setUsername(val:string):void{ this.username = val; }
    public setPassword(val:string):void{ this.password = val; }
    public getUsername():string{ return this.username; }
    public getPassword():string{ return this.password; }
}
```

继承：

使用extends继承类

子类构造函数中必须调用super函数执行父类构造函数

访问控制修饰符：

`public`: 默认使用`public`，对外可以访问

`private`: 不对外访问，不对子类访问

`protected`: 不对外访问，可对子类访问

readonly修饰符：

使用`readonly`关键字将属性设置为只读，只读属性只能在声明时或构造函数中初始化

也可以在方法的参数中使用

存取器：类似于C#中的存取器

```ts
class A{
    private _a:string;
    get a():string{
        // do something
        return this._a;
    }
    set a(val:string):void{
        // do something
        this._a = val;
    }
}

let a:A = new A();
a.a = "123";
console.log(a.a);
```

静态属性：

`static`

抽象类：

`abstract`

TS类被编译成JS时，类的实例属性会作为实例对象的属性，类的实例方法作为对象原型的方法，类的静态成员作为构造函数对象的成员。

## 函数

函数的完整定义：

```ts
let fun:(x:number,y:number)=>number = 
    function(x:number,y:number):number{return x+y;}
```

类型推断：赋值运算符左边一已经添加了标明了类型，则赋值运算符右边就不用再添加类型了。

使用`?`指定可选参数。

使用`...`指定可变参数

注意`function`和箭头函数中关于`this`的区别

函数重载：

```ts
// 重载函数
function hello(x:string):string;
function hello(x:number):number;

// 重载函数的实现在这里
function hello(x:any):any{
    if(typeof x === 'string'){
        return `string:${x}`;
    }
    else if(typeof x === 'number'){
        return x;
    }
}
```

## 泛型

泛型的语法风格与C++类似，泛型参数声明的位置

在参数列表紧接在函数、类的名称之后

泛型约束：`extends`

## 枚举

支持数字枚举和字符串枚举，支持混合异构枚举

枚举成员的值可以是常量或可计算的

字面量枚举成员：不带有初始值的常量枚举成员或者值被初始化为：

- 任何字符串字面值
- 任何数字字面值
- 应用了一元运算符`-`的数字字面值

当所有枚举成员都拥有了字面量枚举值时

- 枚举成员成为了类型
- 枚举类型本身成为每个枚举成员的联合。换言之，枚举成员彼此一定是互斥的，那么在对这些值进行带类型的逻辑运算时，就可以应用短路规则。

运行时的枚举：枚举是运行时真正的对象，会创建以属性名作为对象成员的对象，数字枚举还具有反向映射，从枚举值到枚举的名称

const枚举：只允许常量枚举，不允许计算枚举，常量枚举在编译过程中会被删除，并在使用的地方内联进去

外部枚举：外部枚举用来描述已经存在的枚举类型的形状。外部枚举和非外部枚举之间有一个重要的区别，在正常的枚举里，没有初始化方法的成员被当成常数成员。对于非常数的外部枚举而言，没有初始化方法时被当作需要经过计算的。

```ts
declare enum Enum{
    A = 1,
    B,
    C = 2
}
```

## 类型推断

最佳类型推断：推断类型时，会从候选类型中推断出通用类型，能够兼容所有的候选类型。最终得到的最佳类型有可能是候选类型之一，也有可能是候选类型的联合

上下文类型：类型推断也有可能按照相反的方向，由左值的类型推断得到右值的类型。上下文类型也会作为最佳类型推断的候选类型

## 类型兼容性

TS中的类型兼容性是基于结构子类型的。换言之，不强求形式上的类型兼容，只强调实质结构上的类型兼容。只要对象具有与类型相同的结构，即便没有声明，也是类型兼容的

### 函数兼容性

我兼容你 => 你有的我都有

你不兼容我 => 我有的，你没有

类型系统强制源函数的返回值类型必须是目标函数返回值类型的子类型

区分几个概念：

- 协变（Covariant）：`T`兼容`U`，`Comp<T>`兼容`Comp<U>`
- 逆变（Contravariant）：`T`兼容`U`，`Comp<U>`兼容`Comp<T>`
- 双向协变（Bivariant）：既满足协变又满足逆变
- 不变（Invariant）：既不满足协变，又不满足逆变

举例说明：

```
A兼容B
协变：Array<A>兼容Array<B>
逆变：(p:B)=>void兼容(p:A)=>void
	# 调用的时候，如果传参为A，(p:B)=>void和(p:A)=>void都不会报错
	# 调用的时候，如果传参为B，(p:B)=>void不会报错，但(p:A)=>void需要把B转成A，会丢掉一一些内容，所以会报错
	# 因此，函数参数是一种逆变
```

函数参数双向协变：

关闭`strictFunctionTypes`选项之后，允许函数参数从`B`转为`A`，那么这时候，就说函数参数具有双向协变。

可选参数与剩余参数：

- 比较函数兼容性时，可选参数与必须参数是可互换的。源类型上有额外的可选参数不是错误，目标类型的可选参数在源类型里没有对应参数也不是错误。

  比如，我要具有三个可选参数的函数，你给我没有可选参数的函数，没有问题；除了那三个可选参数，你又多加了一个可选参数，也没有问题。

- 当一个参数有剩余参数时，等价于有无穷多个可选参数

函数重载：

对于有重载的函数，源函数的每个重载都要在目标函数上找到对应的函数签名，保证目标函数可以在所有源函数可调用的地方调用

### 枚举和类

枚举类型与数字类型兼容，并且数字类型与枚举类型之间也是兼容的，不同枚举之间是不兼容的

比较两个类类型时，只有实例的成员会被比较，静态成员和构造函数不在比较之列

类的私有成员和保护成员会影响兼容性，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。同样地，这条规则也适用于保护成员实例的类型检查。这允许子类赋值给父类，但是不能赋值给其它有相同类型的类。

### 泛型

ts是结构性的类型系统，类型参数只影响使用其作为类型一部分的结果类型

```ts
interface Empty<T>{
    
}

let x:Empty<number>;
let y:Empty<string>;
x = y;//兼容的，虽然使用类型参数，但结果类型是一致的
```

如果把Empty改成这样就不是兼容的了

```ts
interface Empty<T>{
    data:T;
}
```

### 子类型与赋值

TS中有两种兼容性，子类型和赋值，赋值扩展了子类型兼容性，增加了允许和any来回赋值，enum和对应数字值之间的赋值等规则

实际上，类型兼容性是由赋值兼容性控制的

## 高级类型

### 交叉类型

将多个类型合并成一个类型，包含了所需所有类型的特性。

例如`Person & Serializable & Loggable`同时是`Person`、`Serializable`和`Loggable`。

大多数情况下，是在混入（mixins）或者其他不适合典型面向对象模型的地方看到交叉类型的使用。

> 什么是混入？
>
> 在继承或者实例化时，JS的对象机制并不会自动执行复制行为。简单地说，JS中只有对象，并不存在可以被实例化的类。一个对象并不会被赋值到其他对象，他们会被关联起来。由于在其他语言中类表现出来的都是复制行为，因此JS开发人员想出一种模拟类的行为，这个方法就是混入。
>
> 混入分为**显式混入**和**隐式混入**
>
> 1.显式混入
>
> 将目标对象中没有的且源对象中直接拥有的属性值复制到目标对象中去
>
> 1.1.显式伪多态
>
> 调用”源对象“的方法时，使用call硬绑定上下文至混入的”目标对象“中去，这样也能够实现多态。这种模式就是显式伪多态。
>
> 1.2.寄生复制
>
> 在B的构造函数中，创建A的一个实例，然后暂存A实例的方法引用，重写A实例的方法为新的方法，在新的方法中可以使用call调用暂存的方法，最后返回构造的对象
>
> 2.隐式混入
>
> 就是直接定义一个新对象，具有相同签名的方法，然后使用call调用源对象的方法，却绑定上下文至新对象

在执行混入时，混入操作的结果对象应当同时具有源对象类型T和混入对象的类型U，所以混入结果对象的类型就是`T&U`

### 联合类型

既可以是类型T，又可以是类型U，甚至是`T&U`，那么这种复合得到的类型就是联合类型`T|U`。

易得`T|T&U|U = T|U`

### 类型保护与区分类型

对于联合类型，可以访问每种类型都有的成员，但访问某种类型特有的成员，必须使用类型断言。

```ts
function fun(animal:Fish|Bird){
    if((<Fish>animal).swim){
        (<Fish>animal).swim();
    }
    else{
        (<Bird>animal).fly();
    }
}
```

类型断言可以使用`<>`或者`as`关键字表示/

用户自定义的类型保护：为了减少代码中类型断言的使用，可以使用用户自定义的类型保护机制。

```ts
interface Fish{
    swim:()=>void;
}
interface Bird{
    fly:()=>void;
}
const hello = function(obj:Fish|Bird):obj is Fish{
    return (obj as Fish).swim !== undefined;
}

// 定义一个对象，对象的类型是联合类型 Fish | Bird
let obj:Fish|Bird = {
    swim(){},
    fly(){}
};
// 调用类型判断函数，该函数使用了类型谓词
if(hello(obj)){
    obj.swim();// 此处已经断定为Fish
}
else{
    obj.fly();// 此处已经断定为Bird
}
```

利用TS的泛型，可以将上面的例子改造为：

```ts
interface Fish{
    swim:()=>void;
}
interface Bird{
    fly:()=>void;
}
const isOfType = function<T>(obj:any,param:keyof T):obj is T{
    return (obj as T)[param] !== undefined;
}

let obj = {
    swim(){}
} as Fish|Bird;
if(isOfType<Fish>(obj,'swim')){
    obj.swim();
}
else{
    obj.fly();
}
```

### typeof类型保护

TS能够自动将typeof obj === 'number'识别为类型保护

```ts
function hello(obj:any){
    if(typeof obj === 'number'){
        return obj.toFixed();
    }
    else if(typeof obj === 'string'){
        return obj.trim();
    }
    else{
        return "";
    }
}
```

这些**`typeof`类型保护**只有两种形式能被识别： `typeof v === "typename"`和 `typeof v !== "typename"`， `"typename"`必须是 `"number"`， `"string"`， `"boolean"`或 `"symbol"`。 但是TypeScript并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护

### instanceof类型保护

```ts
interface Animal{
    move():void;
};
class Dog implements Animal{
    move(){}
    wangwang(){}
};
class Cat implements Animal{
    move(){}
    miaomiao(){}
}
function hello(obj:Animal){
    if(obj instanceof Dog){
        obj.wangwang();//此处可以断定为Dog
    }
    else if(obj instanceof Cat){
        obj.miaomiao();//此处可以断定为Cat
    }
}
```

### 可以为null的类型

null和undefined类型，分别具有值null和undefined，可以赋值给其他类型，null和undefined也是其他类型的一个有效值。

`--strictNullChecks`标记可以解决此错误：当你声明一个变量时，它不会自动地包含 `null`或 `undefined`。 你可以使用联合类型明确的包含它们。

### 可选参数和可选属性

使用了 `--strictNullChecks`，可选参数会被自动地加上 `| undefined`

可选属性也会有同样的处理

### 类型保护和类型断言

使用判空语句完成类型断言

```ts
function hello(obj:string|null){
    if(obj == null){
        return 'default';
    }
    else{
        return obj.trim();
    }
}
```

使用`!`后缀能够使用断言去除null和undefined

### 类型别名

```ts
type 类型别名 = 类型、类型别名或其组成的表达式
```

接口与类型别名的区别：

- 接口创建新的名称，别名只是别名
- 别名不能被extends/implements也不能extends/implements其他类型

### 字符串字面量类型、数字字面量类型、枚举成员类型

```ts
type A = "1"|"2"|"3";
type B = 1|2|3;
enum C{
    option_a=1,
    option_b=2,
    option_c=3
};
function fun(a:A,b:B){}
```

### 可辨识联合

可辨识联合（标签联合、代数数据类型）：合并单例类型、联合类型、类型保护和类型别名创建而成的高级模式

```ts
interface Square{
    kind: 'square',
    size: number
};

interface Rectangle{
    kind: 'rectangle',
    width: number,
    height: number
};

interface Circle{
    kind: 'circle',
    radius: number
};

type Shape = Square|Rectangle|Circle;

function area(shape:Shape):number{
    switch(shape.kind){
        case 'square':
            return shape.size * shape.size;
        case 'rectangle':
            return shape.width * shape.height;
        case 'circle':
            return Math.PI * shape.radius*shape.radius;
        default:
            return 0;
    }
}

let circle:Circle = {
    kind:'circle',
    radius:10
};

console.log(`area = ${area(circle)}`);
```

完整性检查：

```ts
//增加一种新的类型 Triangle
type Shape = Square|Rectangle|Circle|Triangle;
function area(s:Shape):number{
    //...
    // area函数不变，并没有增加Triangle的代码
}
// 在这种情况下如果想要让编辑器检查可辨识联合的完整性，可以使用下面的方法
```

解决的方案有两种：

- 指定返回值类型并且启用`--strictNullChecks`。如果传参是一个Triangle，那么没有对应的return语句，返回值类型就是undefined，在启用空值校验的情况下，编译就会报错。
- 使用`never`类型，编译器使用这种类型进行完整性校验

```ts
// 返回值类型为never时，不允许函数正常返回
function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s); // 此处抛出异常
    }
}
```

### 多态的`this`类型

将方法的返回值类型定义为this类型，调用方法的对象具体是什么类型，this就是什么类型，从而实现“多态”的效果

```ts
class A{
    protected data:number = 0;
    // 定义一个方法，方法的返回值类型为this
    public inc(val:number):this{
        this.data+=val;
        return this;
    }
}

// 类B继承自类A
class B extends A{
    // 定义一个方法，方法的返回值类型为this
    public dec(val:number):this{
        this.data-=val;
        return this;
    }

    public get():number{
        return this.data;
    }
}

let inst:B = new B();
// inst的类型是B，调用了A的方法，返回值又调用了B的方法
// 这说明返回值类型this就是实例的类型
// 简言之，就是inst是A，那么this类型就是A，inst是B，那么this类型就是B
inst.inc(2).dec(3);
```

### 索引类型

索引类型查询操作符`keyof T`：类`Person`中有两个属性`a`和`b`，那么`keyof Person`的结果就是 `"a"|"b"`。（包括但不限于类）

索引访问运算符`T[K]`：类Person中具有属性a和b，person是Person类的实例，那么person["a"]的值具有的类型可以表示为Person["a"]，Person类任何一个属性值的类型都包含于Person[keyof Person]。（包括但不限于类）

字符串索引签名与索引类型：

```ts
interface Map<T>{
    [key:string]:T;//字符串索引签名
}
// 索引类型查询操作符和索引访问运算符的使用
let keys:keyof Map<number>;// string
let value:Map<number>[keyof Map<number>];//number
```

### 映射类型

类型映射是从类型到类型的映射，其中映射到的类型称为映射类型

映射的原象：

```ts
interface Item{
    code:number;
    name:string;
    value:object;
}
```

映射的象：

```ts
interface ResultItem{
    readonly code:number;
    readonly name:string;
    readonly value:object;
}
```

那么我们可以将`ResultItem`表示为`{readonly [P in keyof Item]:Item[P];}`

```ts
type rdItem = {
    readonly [P in keyof Item]:Item[P];
}
```

映射类型结合泛型使用：

```ts
type Nullable<T> = {[P in keyof T]:T[P]|null}
type Partial<T> = {[P in keyof T]?:T[P]}
// 映射只作用于T，是同态的
```

TS标准库中的映射类型：

```ts
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
type Partial<T> = {
    [P in keyof T]?: T[P];
}
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
}
type Record<K extends string, T> = {
    [P in K]: T;
}
```

`Readonly`， `Partial`和 `Pick`是同态的，但 `Record`不是。 因为 `Record`并不需要输入类型来拷贝属性，所以它不属于同态。非同态类型本质上会创建新的属性，因此它们不会从它处拷贝属性修饰符。

由映射类型可以完成类型推断，实现拆包

预定义的有条件的类型：

- `Exclude<T, U>` -- 从`T`中剔除可以赋值给`U`的类型。
- `Extract<T, U>` -- 提取`T`中可以赋值给`U`的类型。
- `NonNullable<T>` -- 从`T`中剔除`null`和`undefined`。
- `ReturnType<T>` -- 获取函数返回值类型。
- `InstanceType<T>` -- 获取构造函数类型的实例类型。

## Symbols

symbol是ECMA 2015新增的一种原生类型，symbol类型的值是通过Symbol构造函数构造的。

```ts
let sym1 = Symbol();
let sym2 = Symbol("key");
```

Symbols是不可改变且唯一的，不可能通过Symbol函数构造两个相等的Symbol，即便传递相同的参数

Symbol也可以作为对象的属性名：

```ts
let sym = Symbol();
let obj = {
    [sym]:123
};
```

Symbol可以与计算出来的属性名声明相结合来声明对象的属性和类成员。

Symbols常见的方法：

 `Symbol.hasInstance`

方法，会被`instanceof`运算符调用。构造器对象用来识别一个对象是否是其实例。

 `Symbol.isConcatSpreadable`

布尔值，表示当在一个对象上调用`Array.prototype.concat`时，这个对象的数组元素是否可展开。

 `Symbol.iterator`

方法，被`for-of`语句调用。返回对象的默认迭代器。

 `Symbol.match`

方法，被`String.prototype.match`调用。正则表达式用来匹配字符串。

 `Symbol.replace`

方法，被`String.prototype.replace`调用。正则表达式用来替换字符串中匹配的子串。

 `Symbol.search`

方法，被`String.prototype.search`调用。正则表达式返回被匹配部分在字符串中的索引。

 `Symbol.species`

函数值，为一个构造函数。用来创建派生对象。

 `Symbol.split`

方法，被`String.prototype.split`调用。正则表达式来用分割字符串。

 `Symbol.toPrimitive`

方法，被`ToPrimitive`抽象操作调用。把对象转换为相应的原始值。

 `Symbol.toStringTag`

方法，被内置方法`Object.prototype.toString`调用。返回创建对象时默认的字符串描述。

`Symbol.unscopables`

对象，它自己拥有的属性会被`with`作用域排除在外。



迭代器与生成器：

- 可迭代性：当一个对象实现了Symbol.iterator属性时，认为其是可迭代的。
- for-of与for-in语句分别迭代可迭代对象的键和值，调用对象上的Symbol.iterator方法
- 当生成目标为ES5或者ES3，迭代器只允许在Array类型上使用，在非数组值上使用for-of语句会得到一个错误，即便这些对象已经实现了Symbol.iterator属性

## 模块

ES5引入模块的概念。

模块在其自身的作用域中运行，而不是在全局作用域中。

export可以将模块作用域中的变量、函数、类导出，使用import可以将这些变量、函数和类引入。

模块使用模块加载器导入其他模块，模块加载器能够在执行模块代码之前引入模块所有的依赖项。

TS/ECMA 2015中，任何包含顶级import或者export的文件都将被当成一个模块，否则不被当成模块，其内容全局可见。

导出：

- 任何声明都可以通过export导出

- 使用`as`关键字对导出的部分重命名

- 重新导出： 重新导出功能并不会在当前模块导入那个模块或定义一个新的局部变量。

  ```ts
  // 导出原先的验证器但做了重命名
  export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
  ```

- 一个模块可以包裹多个模块，并把他们导出的内容联合在一起通过语法：`export * from "module"`。

导入：

- 导入一个模块中的某个导出内容

- 导入内容重命名

- 将整个模块导入到一个变量，并通过它来访问模块的导出部分

- 具有副作用的导入模块

  ```ts
  import { ZipCodeValidator } from "./ZipCodeValidator";
  import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
  import * as validator from "./ZipCodeValidator";
  import "./my-module.js";
  ```

默认导出：

- 使用export default，相应的import导入时，不需要添加"{}"

  ```ts
  export default class{
      //...
  }
  //===============================================
  import Hello from './Hello.ts';
  ```

  

- `export =` 和 `import = require()`：CommonJS和AMD中导入依赖的方法与export default语法不兼容，故有该用法。

  ```ts
  class A{
      hello(){}
  }
  export = A;
  //=============================================
  import A = require('./A.js')
  ```

如果想要描述非Typescript编写类库的类型，需要声明类库所暴露出的API

通常在".d.ts"文件中定义非Typescript类库的类型。

```ts
declare module "url" {
    export interface Url {
        protocol?: string;
        hostname?: string;
        pathname?: string;
    }

    export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export let sep: string;
}
```

如果不想在使用一个模块之前编写声明，可以采用简写的方式以便能够快速使用它。简写模块中导出的类型将是any

```ts
declare module "hot-new-module";
```

某些模块加载器如[SystemJS](https://github.com/systemjs/systemjs/blob/master/docs/overview.md#plugin-syntax) 和 [AMD](https://github.com/amdjs/amdjs-api/blob/master/LoaderPlugins.md)支持导入非JavaScript内容。 它们通常会使用一个前缀或后缀来表示特殊的加载语法。 模块声明通配符可以用来表示这些情况。

```ts
declare module "*!text" {
    const content: string;
    export default content;
}
// Some do it the other way around.
declare module "json!*" {
    const value: any;
    export default value;
}

import fileContent from "./xyz.txt!text";
import data from "json!http://example.com/data.json";
```

## 命名空间

命名空间的作用：组织代码，避免命名冲突

```ts
namespace HelloWorld{
    //...
}
```

多文件命名空间：将同一个命名空间拆分为多个文件存放，尽管是多个文件，但是仍旧是相同的命名空间。

引文不同文件存在依赖关系，所以需要加入引用标签告诉编译器文件之间的关联

- hello.ts

```ts
namespace HelloWorld{
    export interface A{
        sayHello():string;
    }
}
```

- hello1.ts

```ts
/// <reference path="hello.ts"/>
namespace HelloWorld{
    export interface B{
        sayWorld():string;
    }
}
```

- hello2.ts

```ts
/// <reference path="hello.ts"/>
namespace HelloWorld{
    export interface C{
        sayHi():string;
    }
}
```

- main.ts

```ts
/// <reference path="hello.ts"/>
/// <reference path="hello1.ts"/>
/// <reference path="hello2.ts"/>

const a:HelloWorld.A = {
    sayHello(){
        return "hello";
    }
}

const b:HelloWorld.B = {
    sayWorld(){
        return "world";
    }
}

const c:HelloWorld.C = {
    sayHi(){
        return "hi";
    }
}
```

命名空间涉及多文件的时候，必须确保所有编译后的代码都会被加载，有两种方式：

- 把所有的输出文件编译成一个输出文件

  ```shell
  tsc --outFile sample.js main.ts
  # 编译器会根据源码中的引用标签自动地对输出进行排序
  tsc --outFile sample.js hello.ts hello1.ts hello2.ts main.ts
  # 也可以单独地指定每个文件
  ```

- 分别编辑各个文件，然后在页面中使用script标签按照次序将生成的js文件引入。

  ```html
  <script src="hello.js" type="text/javascript" />
  <script src="hello1.js" type="text/javascript" />
  <script src="hello2.js" type="text/javascript" />
  <script src="main.js" type="text/javascript" />
  ```

别名：可以通过import语法简化命名空间的写法

```ts
namespace HelloWorld{
    export interface A{
        sayHello():string;
    }
}

import A = HelloWorld.A;

let a:A = {
    sayHello(){
        return "123";
    }
}
```

声明命名空间：

流行的程序库D3在全局对象`d3`里定义它的功能。 因为这个库通过一个 `<script>`标签加载（不是通过模块加载器），它的声明文件使用内部模块来定义它的类型。 为了让TypeScript编译器识别它的类型，我们使用外部命名空间声明。

```ts
declare namespace D3 {
    export interface Selectors {
        select: {
            (selector: string): Selection;
            (element: EventTarget): Selection;
        };
    }

    export interface Event {
        x: number;
        y: number;
    }

    export interface Base extends Selectors {
        event: Event;
    }
}

declare var d3: D3.Base;
```

### 命名空间和模块

命名空间：全局作用域下一个普通的带有名字的JS对象，用于解决命名冲突问题。

模块：外部模块，用于引入外部依赖。

命名空间和模块的陷阱：

- 对模块使用`/// <reference/>`
- 不应该对模块使用命名空间

## 模块解析

模块解析的流程：

- 尝试定位表示导入模块的文件
- 如果上面的解析失败，并且模块名是非相对的，编译器会尝试定位一个外部模块声明。
- 如果编译器还是不能找到模块，会抛出错误。

相对导入以`/、./、../`开头，除此之外为非相对导入。

非相对模块的导入可以相对于baseUrl或者路径映射来进行解析。

模块解析策略：

- 两种可供使用的解析策略：Node和Classic，使用`--moduleResolution`指定解析策略
- Classic策略：为了向后兼容保留的模块解析策略。
  - 相对模块：相对于导入者，先找.ts文件，再找.d.ts文件
  - 非相对模块：从包含导入目录文件开始，逐级上溯，每一级先找.ts文件，再找.d.ts文件
- Node策略：模仿Node.js的模块解析策略

> Node.js的模块解析策略：
>
> 相对模块：
>
> 1. 现在当前路径下找xxx.js
> 2. 当前路径下找package.json，根据其中的main属性
> 3. 找xxx目录，找里面的index.js
>
> 非相对模块：
>
> ​	逐级上溯，查找node_modules目录，对于每一个目录使用相对模块的查找办法

​	模仿Node.js的查找方法，但是每一个目录里面，按照下面的顺序找：（1）ts文件；（2）tsx文件；（3）d.ts文件；（4）package.json（如果指定了types属性）；（5）index.ts；（6）index.tsx；（7）index.d.ts。



附加的模块解析标记：用来通知编译器在源码编译成最终输出的过程中都发生了哪个转换

baseUrl：利用AMD模块加载器的应用中，使用baseUrl告诉编译器去寻找module，非相对模块导入都会被当作相对于baseUrl。baseUrl的值由命令行或者tsconfig中的baseUrl属性决定.

有时候模块不是直接放在baseUrl下的，需要在tsconfig.json文件中设置”path“来进行映射

```json
{
  "compilerOptions": {
    "baseUrl": ".", // This must be specified if "paths" is.
    "paths": {
      "jquery": ["node_modules/jquery/dist/jquery"] // 此处映射是相对于"baseUrl"
    }
  }
}
```

"paths"是相对于”baseUrl“进行解析的，如果”baseUrl“被设置成了除”.“以外的其他值，那么映射就必须做出相应的改变。

”paths“还可以指定复杂的映射，包括指定多个回退位置。

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "*": [
        "*",
        "generated/*"
      ]
    }
  }
}
```

该示例告诉编译器，匹配”*“的模块会从以下两个位置去找：

1. ”*“表示名字不发生变化
2. ”generated/*"表示模块名添加了给定的前缀



利用rootDirs指定虚拟目录：

有时候多个目录下的工程源文件在编译时会进行合并放在某个目录下，利用rootDirs可以指定这个“虚拟”目录

```json
{
  "compilerOptions": {
    "rootDirs": [
      "src/views",
      "generated/templates/views"
    ]
  }
}
```



跟踪模块解析：使用`--traceResolution`启用编译器的模块解析跟踪

使用`--noResolve`：告诉编译器不要添加任何不是在命令行上传入的文件到编译列表。编译器仍然会尝试解析模块，但是只要没有指定这个文件，那么它就不会被包含在内。

为什么在exclude列表中的模块还是会被编译器使用：使用exclude排除某些模块，使用files引入某些模块。有些是被`tsconfig.json`自动加入的，在排除的同时，还是排除所有对它使用了import或使用了三斜线引入指令的文件

## 声明合并

声明合并：TS编译器针对同一个 名字的多个独立声明合并为一个，合并和的声明具有原先多个声明的特性

TS中的声明会创建以下三种声明之一：命名空间、类型、值

- 创建命名空间的声明会新建一个命名空间，包含了使用点符号来访问时使用的名字
- 创建类型的声明时，用声明的类型创建一个类型并绑定到给定的名字上
- 创建值的声明时，会创建在JS输出中能看到的值

| **Declaration Type** | **Namespace** | **Type** | **Value** |
| -------------------- | ------------- | -------- | --------- |
| Namespace            | X             |          | X         |
| Class                |               | X        | X         |
| Enum                 |               | X        | X         |
| Interface            |               | X        |           |
| Type Alias           |               | X        |           |
| Function             |               |          | X         |
| Variable             |               |          | X         |



### 合并接口：

```ts
interface Person{
    pid:string;
    name:string;
    gender:string;
}

interface Person{
    address:string;
    degreeOfEducation:string;
}

let person:Person = {
    pid:'123456202106250012',
    name:'张三',
    gender:'男',
    address:'北京市东城区',
    degreeOfEducation:'大学本科'
}
```

- 对于非函数成员，接口的非函数成员必须是唯一的，如果不唯一则必须具有相同的类型，否则就会报编译错误。

- 对于函数成员，同名函数声明以函数重载论，并且合并接口时会按照接口定义的先后顺序进行合并，后定义的接口具有更高的优先级。

### 合并命名空间

命名空间的合并：命名空间会同时创建出命名空间和值

- 对于命名空间的合并，export的同名接口进行合并，构成单一命名空间内合并后的接口。

  ```ts
  namespace HelloWorld{
      export interface Person{
          pid:string;
          name:string;
          gender:string;
      }
  }
  
  namespace HelloWorld{
      export interface Person{
          pid:string;
          address:string;
          degreeOfEducation:string;
      }
  }
  
  let person:HelloWorld.Person = {
      pid:'123456202106251234',
      name:'张三',
      gender:'男',
      address:'北京市东城区',
      degreeOfEducation:'大学本科'
  }
  ```

- 对于命名空间里值的合并，如果已经有同名命名空间，那么之后的命名空间的导出成员会附加到已经存在的模块中。

  ```ts
  namespace HelloWorld{
      export const hello = '1';
  }
  
  namespace HelloWorld{
      export const world = '2';
  }
  
  console.log(HelloWorld.hello);
  console.log(HelloWorld.world);
  ```

- 非导出成员仅在原来的命名空间内可见

### 合并命名空间和类

命名空间和可以与其他类型的声明进行合并，只要命名空间的定义符合将要合并类型的定义

合并命名空间和类：

【注意】命名空间不能位于与之合并的类或函数前

```ts
class Hello{
    public data:string;
}
namespace Hello{
    export function sayHello():void{
        console.log("hello!")
    }
}
```

使用声明合并可以实现对现有类的扩展

对function、enum也有相同的用法

### 非法的合并

目前，类不能与其他类或变量进行合并，使用混入可以模仿类的合并

### 模块扩展

JS虽然不支持合并，但是可以通过打补丁的办法实现扩展

具体做法是：

- 利用prototype实现对象的扩展，但是TS编译器对此一无所知
- 将其告知编译器，使用declare module

```ts
import { Observable } from "./observable";
declare module "./observable" {
    interface Observable<T> {
        map<U>(f: (x: T) => U): Observable<U>;
    }
}
Observable.prototype.map = function (f) {
    // ... another exercise for the reader
}
```

如果想要实现全局扩展，使用declare global

```ts
// observable.ts
export class Observable<T> {
    // ... still no implementation ...
}

declare global {
    interface Array<T> {
        toObservable(): Observable<T>;
    }
}

Array.prototype.toObservable = function () {
    // ...
}
```

## JSX（需了解React，待续）

使用JSX：

- 给文件一个tsx的扩展名
- 启用tsx选项

JSX三种模式：

- `preserve`：生成代码中保留JSX以供后续转换操作处理，输出的文件是jsx
- `react`：生成`React.createElement`
- `react-native`：相当于`preserve`，只是生成的是js文件

as操作符：tsx文件中不能使用`<>`进行类型推断，只能使用`as`关键字代替

固有元素与基于值的元素：

- 固有元素：
  - 对于React，固有元素会生成字符串`React.createElement("div")`
  - 使用特殊的接口`JSX.IntrinsicElements`来查找
    - 默认地，如果这个接口没有指定，会全部通过，不对固有元素进行类型检查
    - 如果这个接口存在，那么固有元素的名字需要在`JSX.IntrinsicElements`接口的属性里查找
- 基于值的元素：
  - 对于React，由你自定义的元素却不会生成字符串（`React.createElement(MyComponent)`）
  - 简单的在它所在的作用域里按标识符查找
  - 定义基于值的元素的两种方式：
    - 无状态函数组件（SFC）：组件被定义成JS函数，它的第一个参数是`props`对象。 TypeScript会强制它的返回值可以赋值给`JSX.Element`
    - 类组件：
      - 元素类的类型和元素实例的类型：

## 装饰器

启用：添加编译选项experimentalDecorators，设置为true

装饰器：一种特殊类型的声明，类似于Java的注解，格式为`@expression`，其中expression是值为函数的表达式

装饰器工厂：返回值为供装饰器使用的函数

装饰器可以像Java注解一样进行组合，组合方式类似于函数的复合

装饰器求值：

- *参数装饰器*，然后依次是*方法装饰器*，*访问符装饰器*，或*属性装饰器*应用到每个实例成员。
- *参数装饰器*，然后依次是*方法装饰器*，*访问符装饰器*，或*属性装饰器*应用到每个静态成员。
- *参数装饰器*应用到构造函数。
- *类装饰器*应用到类。

装饰器的使用：基本与Java注解相同

装饰器的定义：

```ts
//类装饰器
function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}

//方法装饰器
function enumerable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.enumerable = value;
    };
}

//访问器装饰器
function configurable(value: boolean) {
    return function (target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.configurable = value;
    };
}

//属性装饰器
import "reflect-metadata";

const formatMetadataKey = Symbol("format");

function format(formatString: string) {
    return Reflect.metadata(formatMetadataKey, formatString);
}

function getFormat(target: any, propertyKey: string) {
    return Reflect.getMetadata(formatMetadataKey, target, propertyKey);
}

//参数装饰器
import "reflect-metadata";

const requiredMetadataKey = Symbol("required");

function required(target: Object, propertyKey: string | symbol, parameterIndex: number) {
    let existingRequiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyKey) || [];
    existingRequiredParameters.push(parameterIndex);
    Reflect.defineMetadata(requiredMetadataKey, existingRequiredParameters, target, propertyKey);
}

function validate(target: any, propertyName: string, descriptor: TypedPropertyDescriptor<Function>) {
    let method = descriptor.value;
    descriptor.value = function () {
        let requiredParameters: number[] = Reflect.getOwnMetadata(requiredMetadataKey, target, propertyName);
        if (requiredParameters) {
            for (let parameterIndex of requiredParameters) {
                if (parameterIndex >= arguments.length || arguments[parameterIndex] === undefined) {
                    throw new Error("Missing required argument.");
                }
            }
        }

        return method.apply(this, arguments);
    }
}
```

## 混入

混入：

- A和B是要被混入C中的类，在定义C时使用`implements`而不是`extends`引入A和B的类型而不是实现
- 在C中为混入的属性方法创建占位属性
- 定义混入操作函数，使用其执行混入。混入操作就是将某个类的全部属性和方法赋给新的类

```ts
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }

}

// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}

class SmartObject implements Disposable, Activatable {
    constructor() {
        setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
    }

    interact() {
        this.activate();
    }

    // Disposable
    isDisposed: boolean = false;
    dispose: () => void;
    // Activatable
    isActive: boolean = false;
    activate: () => void;
    deactivate: () => void;
}
applyMixins(SmartObject, [Disposable, Activatable]);

let smartObj = new SmartObject();
setTimeout(() => smartObj.interact(), 1000);

////////////////////////////////////////
// In your runtime library somewhere
////////////////////////////////////////

function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        });
    });
}
```

## 三斜线指令

类似于：

```ts
/// <reference path="..."/>
```

`/// <reference path=""/>`

用于声明文件间的依赖，三斜线引用告诉编译器在编译过程中要引入的额外的文件。

如果指定了`--noResolve`编译选项，三斜线引用会被忽略；它们不会增加新文件，也不会改变给定文件的顺序

`/// <reference types="..." />`

声明了对某个包的依赖。把 `/// <reference types="node" />`引入到声明文件，表明这个文件使用了 `@types/node/index.d.ts`里面声明的名字； 并且，这个包需要在编译阶段与声明文件一起被包含进来。仅当在你需要写一个`d.ts`文件时才使用这个指令

`/// <reference no-default-lib="true"/>`

这个指令把一个文件标记成*默认库*。 你会在 `lib.d.ts`文件和它不同的变体的顶端看到这个注释。

这个指令告诉编译器在编译过程中*不要*包含这个默认库（比如，`lib.d.ts`）。 这与在命令行上使用 `--noLib`相似。

`/// <amd-module />`

默认情况下生成的AMD模块都是匿名的。 但是，当一些工具需要处理生成的模块时会产生问题，比如 `r.js`。

`amd-module`指令允许给编译器传入一个可选的模块名