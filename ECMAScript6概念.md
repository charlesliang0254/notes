## ECMAScript6概念

兼容性：

http://kangax.github.io/compat-table/es5/

http://kangax.github.io/compat-table/es5/

ES6新特性：变量、函数、数组、字符串、面向对象、Promise、generator、模块化

## 变量

var变量的问题：

1、可以重复声明

2、无法限制修改

3、没有块级作用域

let：不能重复声明，变量，块级作用域

const：不能重复声明，常量，块级作用域

## 函数

参数扩展：

`function show(a,b,...args)`

- 剩余参数之后不能有其他参数
- 函数的length属性不包括rest参数

数组展开：

`show(...arr);`

默认参数：

- 类似c++默认参数

- 【注意】默认参数是惰性取值的

- 函数的length属性：表示第一个默认参数前面的参数的个数

- 一旦设置了默认参数，函数进行声明初始化时，参数会形成单独的作用域，并且持续到初始化结束。因此默认值中用到的变量对于函数体内是不可见的。

- 函数默认值的应用：

  ```js
  function throwException(){
      throw new Error("missing parameter!")
  }
  // param不能为空，否则执行throwException
  function hello(param = throwException()){
      //...
  }
  // param的默认值是undefined，表示该参数可以省略
  function hello2(param = undefined){
      //...
  }
  ```

默认参数与解构赋值结合使用：

```js
// 第一个参数不能省略
function hello({a,b}={a:5,b:3}){
    console.log(`a = ${a}`);
    console.log(`b = ${b}`);
}

// 第二个参数不能省略
function hello2({a=1,b=2}){
    console.log(`a = ${a}`);
    console.log(`b = ${b}`);
}
hello();
// a=5, b=3
hello({});//外层的对象不可以省略，但是内层的对象可以省略
// a=1, b=2
```

函数的严格模式：

- 从ES5开始，函数内部可以设定为严格模式
- ES2016中，规定只要函数参数使用了默认值、解构赋值、扩展运算符，函数内部不能显式设定为严格模式

函数的name属性表示函数名

- 匿名函数name属性值为实际的函数名
- Function构造函数构造的函数name属性值为”anonymous"
- bind返回的函数，name属性值会加上“bound”前缀

箭头函数 ：语法与C++、Java中lambda表达式基本一致，略

【注意】

1、箭头函数体内this的指向就是定义时所在的对象，而不是使用时所在的对象，箭头函数中this指向是固定的

2、不可以当作构造函数，不可以使用new命令

3、不可以使用arguments对象，使用rest参数代替

4、不可以使用yield命令，不能用作生成器函数

5、除了this，箭头函数中也没有arguments、super、new.target

绑定this：使用双冒号（::）绑定函数的this



## 解构赋值与扩展运算符

解构赋值

- 左右两边结构必须一样

- 右边必须是个正确的结构

- 声明和赋值不能分开
- 解构赋值仍然是浅拷贝，JS中只有序列化与反序列化、lodash才提供了深拷贝的方法，除此之外都是浅拷贝
- 解构赋值中使用`...`时不会复制继承自原型对象的属性

```js
let [a,b,c]=[1,2,3];
let {a,b,c}={aaa:1,bbb:2,ccc:3};
let [{a,b},[n1,n2,n3],num,str]=[{a:12,b:5},[12,5,8],8,'cxzcv'];
```

数组中的扩展运算符：

- 基本用法

```js
let arr = [1,2,3];
// 函数调用中使用扩展运算符
let maxval = Math.max(...arr);

// 构造数组时使用扩展运算符
let arr2 = [4,5,6,...arr,...arr]; // [4,5,6,1,2,3,1,2,3]

// 与解构赋值结合使用
let [first,secopond,...rest] = [1,2,3,4,5,6];
console.log(first); // 1
console.log(second); // 2
console.log(rest); // [3,4,5,6]

// 将字符串转成数组
let arr3 = [...'abcdef'];
console.log(arr3); // ['a','b','c','d','e','f']

// 利用扩展运算符能够获得字符串真正的长度（32位的Unicode字符被认为是一个字符）
console.log([...'x\uD83D\uDE80y'].length);
// 凡是涉及Unicode32位字符串都会有类似的问题，应该使用扩展运算符改写

```

- 任何实现了Iterator接口的对象都可以使用扩展运算符展开
- 生成器函数也能使用扩展运算符展开

对象中的扩展运算符：

- 类似于数组的扩展运算符，数组扩展是按照数组下标扩展，而对象的扩展是按照对象属性名扩展的

- 对象的扩展运算符也能够和解构赋值结合使用

- 扩展运算符只会复制对象自身的属性，不会复制继承的属性。如果想要完整地克隆一个对象，还需要指定对象的原型

  ```js
  // 建议写法
  const obj2 = Object.assign(
  	Object.create(Object.getPrototypeOf(obj)),
      obj
  );
  ```

  

- 扩展运算符能够合并多个对象

- 使用扩展运算符时，后面同名的属性会覆盖前面的属性

- 扩展运算符的参数对象中如果有取值函数get，这个方法将会执行



## 数组

map        映射，一个对一个

reduce    汇总，求统计量

filter        过滤器

forEach   循环（迭代）

```javascript
let arr = [12,5,8];
let result = arr.map(function(item){
    alert(item);
    return item*2;
});
console.log(result);

let sum=arr.reduce((tmp,item,index)=>{
    console.log(tmp+","+item+","+index);
    return tmp+item;
});

let sum=arr.filter(item=>item%3==0);

arr.forEach((item,index)=>{
    alert(item);
});
```

Array.from：用于将两类对象转化为真正的数组，类似数组的对象和可遍历对象。

```js
// 类似数组的对象转换为数组
// 所谓类似数组的对象，指的就是对象中必须含有length属性
let arr = Array.from({
    '0':'a',
    '1':'b',
    '2':'c',
    length:3
});

// NodeList转换为数
let result = document.body.querySelectorAll('div');
let arr2 = Array.from(result);

// from函数的第二个参数可以指定对数组元素所做的处理
let arr3 = Array.from([1,2,3],x=>x*x);
// 等价于 Array.from([1,2,3]).map(x=>x*x);

// 如果map函数中用到了this，可还可以传入第三个参数绑定this
let arr4 = Array.from(
    [1,2,3],
    function(x){
        return this.fun(x);
    },
    {
        fun(x){
            return x*x;
        }
    }
); // [1,4,9]

let arr5 = Array.from(
    [1,2,3],
    function(x){
        return this.fun(x);
    },
    {
        fun(x){
            return x*x*x;
        }
    }
); // [1,8,27]

// 将字符串转换为数组，可以避免JS将32位Unicode当成2个字符的情况
let len = Array.from('\u{10000}\u{10001}').length; // 2
```

- 使用数组的slice方法也可以实现该特性

Array.of：将一组值转换为数组，避免了使用构造函数过多的重载

```js
Array.of(1,2,3,4,5); // [1,2,3,4,5]
```

copyWithin：将当前数组内指定区间的成员复制到当前数组的其他位置（会覆盖原有成员），后两个参数是可选参数

```js
[1,2,3,4,5].copyWithin(0,3,4); // [4,2,3,4,5]
```

find和findIndex：查找数组成员/成员下标

```js
[1,2,3,4,5].find((item,index,arr)=>item>3); // 4
[1,2,3,4,5].find((item,index,arr)=>item>3); // 4
```

fill：数组填充

entries、keys、values方法：数组也支持获得键值对、键、值

includes：判断数组是否包含某个成员

数组的空位：指的是数组的某个位置没有任何值，空位不是undefined，一个位置的值为undefined依然是有值的。ES5、ES6对空位的处理不同，应避免出现空位

## 对象

允许直接写入变量作为对象的属性和方法

属性和方法的简写

允许字面量对象的属性名（包括方法的名称）使用[]，属性名表达式如果是对象会转换为一个字符串

方法的name属性返回方法名（如果是getter、setter、匿名函数、bind创建的函数等有特殊的名称）

Object.is：避免了`==`类型转换的问题和`===`运算符正负零相等、NaN不等于自身的问题

Object.assign：对象赋值，注意仍然是浅拷贝

Object.getOwnPropertyDescriptor：获得对象属性的描述符

属性的可枚举性（enumerable）：

- ES5中for-in语句、Object.keys方法、JSON.stringify方法会忽略不可枚举的属性，ES6中Object.assign方法会忽略不可枚举的属性。以上4个方法中，只有for-in语句会返回继承的属性（使用ES6实现类继承的除外）
- ES6规定所有Class的原型方法都是不可枚举的
- 操作中尽量不要引入继承的属性，应该使用Object.keys代替for-in语句

属性的遍历：

- for-in语句：包含自身以及继承属性（非Symbol）
- Object.keys：包含自身可枚举的属性（非Symbol）
- Object.getOwnPropertyNames：包含自身属性（非Symbol）
- Object.getOwnPropertySymbols：包含自身Symbol属性名属性
- Reflect.ownKeys：包含自身属性（包括Symbol）
- 属性遍历顺序：
  - 属性名：数值=>字符串=>Symbol
  - 属性名为数值的按数值大小排序
  - 属性名为字符串的属性按生成时间排序
  - 属性名为Symbol的属性按生成时间排序

原型：

- `__proto__`：原型对象，避免使用
- Object.setPrototypeOf、Object.getPrototypeOf：设置、读取对象的原型
- Object.keys、Object.values、Object.entries

引入getOwnPropertyDescriptors方法，获得自身全部属性的描述对象

- 引入该方法的原因是Object.assign方法不能拷贝对象的get或set属性。assign只会复制属性的值，但是不会复制属性的赋值或取值方法。而直接使用定义属性描述对象的方法则可以将一个属性完整的特性复制下来，而不是仅仅赋值属性的值

- 与Object.create配合使用实现对象的克隆、mixin模式

Null传导运算符（`?.`）：先判断对象是否为空，再尝试读取它的属性

- 四种用法：

  ```js
  obj?.prop
  obj?.[expr]
  func?.(...args)
  new C?.(...args)
  ```

  



## 字符串

字符串方法

- String.fromCodePoint：ES5提供的字符串方法，用于支持大于2字节的unicode字符

- String.raw：返回一个斜杠都被转义的字符串

  ```js
  console.log(String.raw`\n\n\n`)//'\\n\\n\\n'
  ```

- codePointAt：返回给定位置的一个字符（可能为2字节、4字节）

- normalize：将字符的不同表示方式统一为一种形式。可选的参数为`NFC`、`NFD`、`NFKC`、`NFKD`

- includes、startWiths、endWiths：子串查找、前缀匹配、后缀匹配。支持第二参数设置查找的起始位置，但endWiths的第二参数表示匹配的结束位置。

- repeat：表示将原字符串重复若干次

- padStart、padEnd：ES2017引入，字符串补全至若干位，如果字符串长度超过补全的长度，则返回原字符串。

- trimStart、trimEnd：ES2019引入，分别用于消除头部和尾部的空格。

- matchAll：返回正则表达式在当前字符串的所有匹配

- replaceAll：ES2021中新增，用于替换字符串中所有的匹配项

字符串模板

```js
let aaa = 2;
let str = `a${aaa}bc`;
```

## 正则表达式

- 新增u修饰符：正则表达式中支持4字节unicode，使用了u修饰符之后，正则表达式的点、大括号、量词、预定义模式、i修饰符、转义的都能够兼容大于4字节的unicode字符串。新增unicode属性，判断正则表达式是否是使用了u修饰符

  4字节unicode字符串的表示法

  ```js
  /\u{20BB7}/u.test('𠮷');// true
  ```

- 新增y修饰符，能够让正则表达式每一次的匹配都是从头部开始；新增sticky属性判断是否设置了y修饰符

- 新增flags属性，能够返回正则表达式的修饰符

- 新增s修饰符，从而让（.)也能够匹配行终止符（回车、换行、行分隔符、段分隔符），称之为`dotAll`模式

- 支持后行断言

  ```txt
  先行断言：x只有在y前面才匹配
  /x(?=y)
  先行否定断言：x只有不在y前面才匹配
  /x(?!y)
  后行断言：x只有在y后面才匹配
  /(?<=y)x
  后行否定断言：x只有不在y后面才匹配
  /(?<!y)x
  后行断言的反斜杠引用也与通常的顺序相反，必须放在对应的那个括号之前
  
  ```

- Unicode属性类

  ```txt
  新的类的写法：
  \p{...}  \P{...}
  其中\P是\p的反向匹配
  
  格式：
  \p{UnicodePropertyName=UnicodePropertyValue}
  \p{UnicodePropertyName}
  \p{UnicodePropertyValue}
  eg:
  \p{Script=Greek}
  \p{Deciml_Number}
  \p{Number}
  ...
  ```

- 具名捕获组

  ```js
  const regex = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/
  const str = "2021-07-01";
  const result = str.match(regex);
  let year = result.groups.year;
  let month = result.groups.month;
  let day = result.groups.day;
  ```

  具名捕获组与解构赋值结合使用：

  ```js
  let date = "2021-06-25 10:00:00";
  let regex = /(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2}) (?<hour>\d{2}):(?<minute>\d{2}):(?<second>\d{2})/
  let {groups:{year,month,day,hour,minute,second}} = date.match(regex);
  console.log(`year = ${year}`);
  console.log(`month = ${month}`);
  console.log(`day = ${day}`);
  console.log(`hour = ${hour}`);
  console.log(`minute = ${minute}`);
  console.log(`second = ${second}`);
  ```

- 添加正则匹配索引特性（略）

- 添加matchAll方法

## 数值的扩展

提供二进制和八进制新的写法：

```js
0B101010
0b101010
0O777
0o777
```

提供`isFinite`和`isNaN`方法

提供`parseInteger`和`parseFloat`方法

提供`isInteger`方法

提供常量`EPSILON`，表示JS能够识别的最小精度，值为`2^-52+1`

安全整数与`isSafeInteger`方法，其中安全整数表示JS能够准确表示的整数，（`-2^53<x<2^53`），引入常量MIN_SAFE_INTEGER和MAX_SAFE_INEGER表示这两个值

Math对象新增属性/方法/运算符：

```txt
trunc  截断小数
sign   符号函数
cbrt   立方根
clz32  将参数转换为32位无符号整数的形式，然后返回32位值里面有多少个前导的0
imul   32位带符号整数形式相乘的结果，超过32位会溢出
fround 返回一个数的32为单精度浮点数形式
hypot  求几何平均数
expm1  e^x-1
log1p  ln(1+x)
log10  lg(x)
log2   log2(x)
6个双曲函数
**     指数运算符
```

提供了BigInt对象

## 面向对象

面向对象：

1、class关键字、构造器和类分开了

2、class里面直接加方法

```js
class User{
    static ANONYMOUS = new User("ANONNYMOUS","");

    constructor(name,password){
        this.name = name;
        this.password = password;
    }
    
    showName(){
        return this.name;
    }
    
    showPassword(){
        return this.password;
    }

    get data(){
        return this._data;
    }

    set data(data){
        this._data = data;
    }

    static createUser(name,password){
        return new User(name,password);
    }
}
```

继承：

```js
class VipUser extends User{
    constructor(name,password,level){
        super(name,password);
        this.level = level;
    }
    
    showLevel(){
        return this.level;
    }
}
```

Class也可以使用表达式的方式来定义：

```js
const Hello = class A{} // 此处的类名是Hello而不是A，A只在类的内部可用
const Hello1 = class{}//此处省略了内部的类名
const hello2 = new class{//定义一个立即执行的Class
    //...
}();
```

类不存在变量提升，必须先声明后使用

this默认指向类的实例，但是必须保证使用`实例.成员`的方式来调用。如果实例方法提取出来单独使用，this的指向就是方法的执行环境。对于这个问题，有三种解决办法：在构造方法中绑定this、使用箭头函数、使用Proxy在获取方法的时候自动绑定this

`new.target`属性：在构造函数中返回new命令所调用的构造函数

- 如果不是通过new命令调用的，返回为`undefined`
  
- 子类继承父类的时，返回子类，利用这一点可以模拟“抽象类不能实例化”

super关键字：

- 调用父类构造器，但是注意绑定的对象仍然是this

- super作为对象时在普通方法中指向父类的原型对象，在静态方法中指向父类

- 由于super指向父类的原型对象，因此定义在父类实例上的属性和方法不能使用super访问；但是因为使用super调用父类构造函数时，绑定的对象就是this，所以实际上父类中定义的实例属性和方法就是子类对象的属性和方法，可以直接使用this调用；如果子类中定义了同名的实例属性和方法，就会把在父类构造器中给属性赋的值覆盖掉。

利用混入模式，可以模拟“多继承”

```js
function mix(...mixins){
    class Mix{}
    for(let mixin of mixins){
        copyProperties(Mix,mixin);
        copyProperties(Mix.prototype,mixin.prototype);
    }
    return Mix;
}

function copyProperties(target,source){
    for(let key of Reflect.ownKeys(source)){
        if(!["constructor","prototype","name"].includes(key)){
            let desc = Object.getOwnPropertyDescriptor(source,key);
            Object.defineProperty(target,key,desc);
        }
    }
}
```

## Symbol

Symbol是JS第七种数据类型，typeof Symbol的结果是“symbol"，Symbol值不是对象不能添加属性

Symbol值通过Symbol函数生成，表示独一无二的值，Symbol函数的参数只是用来描述Symbol，并不能唯一确定一个Symbol

Symbol不能与其他类型进行运算

Symbol可以转换为字符串、布尔值，但是不能转换为数值

Symbol值可以作为对象的属性名，访问Symbol类型名称的属性时只能使用`[]`运算符

Symbol独一无二的特性可以用来定义码值

Symbol类型属性名的属性不能通过一般的方法被遍历到，所以利用这个特性可以定义一些只希望内部使用的方法

Symbol.for：根据名称查找Symbol，如果没有找到就新建一个。该方法为Symbol登记的名称是全局环境的，可以在不同的iframe和service worker中取得同一个值。Symbol和Symbol.for的区别在于后者会被登记在全局环境中供搜索。

Symbol.keyFor：返回一个已经登记在全局环境中的Symbol的键名。

利用Symbol实现单例模式：使用Symbol.for注册/获取一个Symbol值，使用Symbol.for获得的Symbol值必然是唯一的，在顶级对象中使用这个Symbol值查询属性值，则得到的属性值也必定是唯一的

内置的Symbol值：

- Symbol.hasInstance：使用instanceof运算符时会调用hasInstance方法

  ```js
  foo instanceof Foo
  // 相当于
  Foo[Symbol.hasInstance](foo)
  ```

- Symbol.isConcatSpreadable：对象的Symbol.isConcatSpreadable属性等于一个布尔值，表示该对象使用Array.prototype.concat()时是否可以展开

  ```js
  let arr = ['c','d'];
  ['a','b'].concat(arr,'e');//['a','b','c','d','e']
  arr[Symbol.isConcatSpreadable]//undefined
  
  let arr2 = ['c','d'];
  arr2[Symbol.isConcatSpreadable] = false;
  ['a','b'].concat(arr2,'e');//['a','b',['c','d'],'e']
  ```

- Symbol.species：对于一些内置类型如Array、Promise等在创建新的对象时，调用原对象的this.constructor[Symbol.species]属性指向的函数，而不是直接使用对象原本的构造函数，这样，如果想对这些内置类型进行扩展，又希望创建对象时不要创建派生类的对象，而是内置类型的对象，就可以通过重写Symbol.species取值器来改变默认创建派生类的方法

  ```js
  class MyArray extends Array{
      static get [Symbol.species](){
          return Array;
      }
  }
  
  let arr = new MyArray(1,2,3);
  let arr2 = arr.map(x=>x*x);
  console.log(arr2 instanceof MyArray);//false
  console.log(arr2 instanceof Array);//true
  
  // 这里，map方法会调用this.constructor[Symbol.species]来创建衍生的数组对象
  // 在定义MyArray类时，我们重写了get [Symbol.species]，故，返回的数组arr2就是Array的实例
  ```

- Symbol.match：用于扩展字符串的match方法的行为

  ```js
  let obj = {
      [Symbol.match](){
          return 1;
      }
  }
  "123".match(obj);//1
  // 相当于
  obj[Symbol.match]("123");//1
  ```

- Symbol.replace：用于扩展字符串的replace方法的行为

  ```js
  let obj = {
      [Symbol.replace](searchValue,replaceValue){
          return "hello";
      }
  }
  "123".replace(obj,"world");// "hello"
  // 相当于
  obj[Symbol.replace]("123","world");// "hello"
  ```

- Symbol.search：用于扩展字符串search方法的行为

- Symbol.split：用户扩展字符串split方法的行为

- Symbol.iterator：指向对象默认迭代器方法

- Symbol.toPrimitivie：指向一个方法，对象被转为原始类型的值时会调用这个方法，返回该对象对应的原始的类型值。该方法被调用的时候会传入一个参数值，表示当前运算的模式，一共有三种模式

  - 'number'：该场合需要转换成数值
  - 'string'：该场合需要转换成字符串
  - 'default'：该场合可以转换成字符串也可以转换成数值

- Symbol.toStringTag：该方法定义了Object默认toString方法返回值[object Object]中object后面的标签值。

- Symbol.unScopables：该方法指向一个对象，指定了使用with关键字时哪些属性会被with环境排除。

  ```js
  Array.prototype[Symbol.unscopables]
  /*
  {
  	copyWithin:true,
  	entries:true,
  	fill:true,
  	find:true,
  	findIndex:true,
  	includes:true,
  	keys:true
  }
  */
  ```

## Set & Map

### Set

基本特性：

- 成员唯一，没有重复
- 不会添加重复的值
- 构造器可以接受一切可枚举对象
- 使用“Same-value Equlity”判断相等，类似与`===`，但NaN等于自身

实例属性和方法：

- 构造器
- 属性：size
- 方法：add、delete、has、clear、keys、values、entries、forEach
- 支持扩展运算符

### WeakSet

WeakSet与Set的区别：

- 成员只能是对象
- 对象都是弱引用
- WeakSet不可遍历

属性和方法：

- 方法：add、delete、has

### Map

基本特性：

- 提供键值对的映射关系，键的类型不局限于字符串
- 不会添加键相等的键值对，键是唯一的，不会重复，如果是对象则和内存地址绑定，如果是基本类型则由值决定

实例的属性和操作方法：

- 属性：size
- 方法：set、get、has、delete、clear、keys、values、entries、forEach

### WeakMap

基本特性：

- 结构和Map类似，用于生成键值对的集合
- 只接受对象作为键名（null除外），不接受其他类型作为键名
- 键名所指向的对象不计入垃圾回收机制
- 使用场景：在某个对象中存放数据，但是不想让垃圾回收机制将引用计算在内，有助于防止内存泄漏
- WeakMap没有遍历操作（keys、values、entries）和size属性，不支持clear操作

实例方法：

- 实例方法：get、set、has、delete

应用：

- DOM元素监听事件，一旦DOM元素消失，那么对应的监听函数也会被销毁
- 部署私有属性

## Proxy

代理对象：

```js
let proxy = new Proxy(target,handler);
// target  代理的目标对象
// handler 定义代理的行为本身
```

要使代理对象发挥作用，必须对代理对象进行操作，而不是目标对象。

一个使用的技巧就是将代理对象设置到目标对象的proxy属性中。

代理对象也可以作为其他对象的原型对象

Proxy支持的拦截操作：

- `get(target,propKey,receiver)`：拦截属性的访问操作
- `set(target,propKey,receiver,value,receiver)`：拦截属性的赋值操作
- `has(target,propKey)`：拦截in运算符操作，返回一个布尔值。has函数只能对in运算符起作用，但是对for-in循环的in不起作用
- `deleteProperty(target,propKey)`：拦截delete运算符操作，返回一个布尔值
- `ownKeys(target)`：拦截获取自身属性描述符的函数，返回一个包含目标表对象所有自身属性的属性名的数组。注意，使用`Object.keys`时，有三类属性会被ownKeys方法自动过滤，目标对象上不存在的属性、属性名为Symbol值、不可遍历的属性。如果目标对象中包含不可配置的属性，则该属性必须被ownKeys方法返回，否则就会报错。如果目标对象是不可扩展的，这是ownKeys方法返回的数组之中必须包含原对象的所有属性，且不能包含原对象的所有属性，否则就会报错。
- `getOwnPropertyDescriptor(target,propKey)`：拦截getOwnPropertyDescriptor同名函数，返回属性的描述对象
- `defineProperty(target,propKey,propDesc)`：拦截Object同名函数，返回一个布尔值
- `preventExtensions(target)`：拦截Object同名函数，返回一个布尔值。只有目标对象不可扩展时，才能返回true
- `getPrototypeOf(target)`：拦截所有试图获取原型对象的方法和运算，返回原型对象。具体来说，主要拦截以下操作：`__proto__`、`isPrototypeOf`、`getPrototypeOf`、`instanceOf`
- `isExtensible(target)`：拦截Object同名函数，返回一个布尔值
- `setPrototypeOf(target,proto)`：拦截Object同名函数，返回一个布尔值。该方法只能返回布尔值，佛祖额就会发生类型转换，如果目标对象不可扩展，该方法不得改变目标对象的原型。
- `apply(target,object,args)`：目标对象如果是一个函数，则拦截函数的执行
- `construct(target,args)`：拦截代理对象作为构造函数调用的操作。

代理对象在进行代理操作时，不能违背目标对象属性描述对象确定的性质。

Proxy.revocable：返回一个可取消的Proxy实例。

```js
let target = {};
let handler = {};
let {proxy,revoke} = Proxy.revocable(target,handler);
proxy.foo = 123;
proxy.foo // 123
revoke();
proxy.foo // TypeError: Revoked
```

## Reflect

设计目的；

- 替换Object类一些方法
- 重新设计Object类某些方法的实现
- 让Object操作变成函数行为
- 与Proxy对象对应起来，完成默认的行为

Reflect支持的操作：

- `get(target,name,receiver)`：查找并返回目标对象的属性

- `set(target,name,value,receiver)`：设置目标对象的属性值

- `has(obj,name)`：判断对象是否具有某个属性

- `deleteProperty(obj,name)`：删除目标对象的属性

- `construct(target,args)`：调用目标函数作为构造函数

- `getPrototypeOf(obj)`：获得对象的原型

- `setPrototypeOf(obj,newProto)`：设置对象的新原型

- `apply(func,thisArg,args)`：相当于Function.prototype.apply.call(func,thisArg,args)

- `defineProperty(target,propertyKey,attributes)`：为对象定义属性，用于取代Object类同名方法

- `getOwnPropertyDescriptor(target,propertyKey)`：获得指定属性的描述符

- `ownKeys(target)`：返回对象的所有属性

- `preventExtensions(target)`：使一个对象变成不可扩展的

- `isExtensible(target)`：判断当前对象是否可扩展

  

## json

JSON对象：

- JSON.stringify()
- JSON.parse()

json标准写法：只能用双引号、所有的名字都必须用引号引起来。

简写：名字和值一样、方法省略:function

## Promise

基本使用：

异步：A与B无关，并发

同步：A与B相关，非并发

Promise——消除异步操作，用同步的形式实现异步的操作。

```js
function createPromise(url){
    return new Promise(function(resolve,reject){
        //resolve成功，reject失败
        $.ajax({
            url,
            dataType:'json',
            success(data){
                resolve(data);
            },
            error(err){
                reject(err);
            }
        });
    })
}

Promise.all(
    createPromise('/data/123'),createPromise('/data/456')
).then(function(){
    alert("all done");
},function(){
    alert("at least one failed")
});
```

ajax封装了Promise

```js
Promise.all([
    $.ajax({url:'data/123',dataType:'json'}),
    $.ajax({url:'data/456',dataType:'json'})
]).then(function(results){
    let [arr,json] = results;
    alert('成功了');
    console.log(arr,json);
},function(){
    alert('失败了');
})
```

Promise的基本特性：

- 对象的状态不受外界的影响，Promise对象代表一个异步操作，有三种状态：Pending、Fulfilled、Rejected
- 一旦状态改变就不会再发生变化
- 无法取消Promise，一旦新建就会立即执行，无法中途取消
- 如果不设置回调函数，Promise内部抛出的错误就不会反应到外部

常用的方法：

- then：为Promise添加状态从Pending变为Fulfilled时的回调函数，Promise中传入的方法正常结束的时候会调用then传入的第一个回调函数，并将Promise的返回值作为参数传递给这个回调函数；Promise中传入的方法异常结束的时候会调用then传入的第二个回调函数，并将Promise的异常信息传入这个回调函数。
- catch：为Promise添加状态从Pending变为Rejected时的回调函数，Promise中传入的方法抛出异常的时候会调用catch传入的回调函数，并将Promise传入方法的返回值作为参数传递给这个回调函数。
    - Promise对象的错误具有“冒泡”的性质，会一直向后状态直到被捕获。如果错误一直没有被捕获，那么只会在控制台输出错误信息，但是抛出至外层代码中
    - 为了在then或者catch中也能够向外层代码抛出错误，可以使用`setTimeout(handler,0)`将错误抛出放在下一个事件循环中。
    - Node中有一个unhandledRejection事件，专门用于监听未捕获的reject错误
- Promise.all：将多个Promise实例p1,p2,...,pn包装成一个新的Promise实例p，p状态为fulfilled当且仅当p1,p2,...,pn的状态都是fulfilled，此时返回值为所有Promise实例结果的数组；p的状态为rejected当且仅当存在一个Promise实例pk∈{p1,p2,...,pn}的状态为rejected，此时错误信息为pk的错误信息。
    - 如果参数中传入的Promise本身捕获了错误，那么错误信息不会抛给Promise.all

- Promise.race：将多个Promise实例包装成一个新的Promise实例，最先返回或者抛出异常的Promise实例决定了最终Promise实例状态的变化。
- Promise.resolve：将现有对象转换为Promise对象
    - 参数为Promise对象：直接传入不做任何转换
    - 参数为thenable对象（具有then方法）：转换为Promise对象传入并且立即执行then方法
    - 参数为非thenable对象或非对象：传入新建Promise对象，状态为fulfilled
    - 不带有任何参数则Promise.resolve直接返回一个fulfilled状态的Promise实例，该实例是在本轮“事件循环”结束时执行（setTimeout(0)是在下一轮“事件循环”开始时执行）
- Promise.reject：返回一个新的Promise实例，状态为rejected，传入的参数原封不动作为rejected的理由
- 两个需要自己部署的方法：
    - done：在Promise实例调用链的最后，使用`setTimeout(handler,0)`将异常抛出至外层代码。
    - finally：使用Promise.resolve封装传入的回调函数并附加至Promise调用链上

```js
//done
Promise.prototype.done = function(onFulfilled,onRejected){
    this.then(onFulfilled,onRejected)
        .catch(reason=>setTimeout(()=>{throw reason;},0));
}

//finally
Promise.prototype.finally = function(callback){
    let P = this.constructor;
    return this.then(
        value => P.resolve(callback()).then(()=>value),
        reason => P.resolve(callback()).then(()=>{throw reason;});
    )
}
```

- Promise.try：不知道或者不想区分函数f是否是同步函数还是异步函数，但是想用Promise来处理它的时候使用（如果直接使用Promise.resolve().then则会在本轮“事件循环”的末尾执行）。（目前浏览器还不支持）
  - 让同步函数同步执行、异步函数异步执行的统一的API：（1）async函数返回Promise；（2）使用new Promise()；（3）使用Promise.try


## 遍历器

使用Typescript语言描述遍历器

```typescript
interface Iterable{
    [Symbol.iterator]():Iterator
}

interface Iterator{
    next(value?:any):IterationResult
}

interface IterationResult{
    value:any,
    done:boolean
}
```

所有具有Iterable结构的实例都可以使用for-of语句进行遍历

遍历开始时首先调用[Symbol.iterator]方法获取遍历器对象，然后再重复调用遍历器的next方法获得每一个元素，直到遍历完全。

原生具备Iterable接口的数据结构有：Array、Map、Set、String、TypedArray、函数的arguments对象、NodeList对象

对于array-like结构（具有length属性、存在数值键名），可以直接使用数组的[Symbol.iterator]方法；普通对象部署数组的[Symbol.iterator]方法无效

如果[Symbol.iterator]没有返回一个有效的遍历器，那么就会报错。

调用遍历器的场合：

- 数组和Set的解构赋值
- 扩展运算符（`...`）
- `yield*`
- `for...of`
- `Array.from()`
- Map和Set等集合
- `Promise.all()`和`Promise.race()`
- 字符串

遍历器最简单的实现方法是生成器函数

遍历器对象的其他方法：

- `return`：循环提前退出时被调用的方法，注意必须返回一个对象告知循环已结束（对象中done属性为true）
- `throw`：配合生成器函数，使用不多

## 生成器

普通函数：顺序执行，一路到底

生成器函数：可以中途停顿

生成器函数的特性：

- 会返回一个遍历器对象，该对象可以依次遍历生成器函数的每一个状态
- 是特殊的函数，使用`function*`来定义，内部使用yield输出结果至调用方。
- 生成器函数调用之后不会立即执行，而是先返回一个指向内部状态的遍历器对象，遍历该对象或者使用手动调用next会使生成器函数状态变化，函数将执行直到遇到下一个yield或者函数正常/异常返回

```js
function *show(){
    alert('a');
    yield;//停顿点
    alert('b');
}

let genObj = show();
genObj.next();//走一步
genObj.next();//再走一步
```

应用场景：请求数据，不用写回调函数

底层原理：用一个generator切分了很多小函数

yield：

- 遇到yield就暂停执行生成器函数，并将yield后跟的值作为返回对象的value属性值
- 调用next方法会让生成器继续执行，直到遇到下一个yield或函数结束。next方法可以传递参数，参数将作为上一个yield语句的返回值，next方法返回的数据是本次yield的值。第一次调用next传递的参数是无效的，因为此时“上一个yield语句并不存在。

- yield既可以传递参数，又可以返回值。如果函数没有return语句，那么返回对象的value就是undefined
- yield语句只能放在生成器函数中，并且如果yield参与了表达式，要用括号括起来
- 与遍历器的关系：生成器函数就是遍历器生成函数，因此可以把生成器赋值给对象的[Symbol.iterator]属性，从而使得对象可以遍历

```js
function *fun(a,b){
    console.log("a = "+a);
    console.log("b = "+b);
    let res = yield "yield point";
    console.log("res = "+res);
    return "all done";
}

let gen = fun(1,2);
console.log(111111111111111);
console.log(gen.next(3));
console.log(222222222222222);
console.log(gen.next(4));
console.log(3333333333333333);
```

结果展示：

```txt
111111111111111
a = 1
b = 2
{ value: 'yield point', done: false }
222222222222222
res = 4
{ value: 'all done', done: true }
3333333333333333
```

相关函数：

- throw：在函数体外抛出错误，然后可以在函数体内捕获错误。throw方法被捕获之后会附带执行下一条yield
- return：返回指定的值，并结束生成器函数的遍历

`yield*`：把一个可遍历对象的元素一个个取出来，每一个添加一个yield

```js
function* fun(){
    yield 'a';
    yield 'b';
    return 'result';
}

function* doFun(func){
    // yield*的双重效果：
    // fun()的返回值‘result’作为yield*的返回值赋给了result
    // fun() yield的内容作为doFun() yield的内容
    let result = yield* func;
    console.log(result);
}

[...doFun(fun())];//['a','b']
```

使用生成器演示前序遍历二叉树：

```js
function Node(value,left,right){
    this.value = value;
    this.left = left;
    this.right = right;
}

let root = new Node(1,new Node(2,new Node(4),new Node(5)),new Node(3,new Node(6),new Node(7)));

function* preOrder(root){
    yield root.value;
    if(root.left){
        yield* preOrder(root.left);
    }
    if(root.right){
        yield* preOrder(root.right);
    }
}

console.log(...preOrder(root));

```

作为对象属性的生成器函数省略`:function`时，应该把`*`放在最前面

生成器函数的this：

- 生成器函数返回的总是一个遍历器，而不是函数绑定的this对象
- 不能够直接将生成器函数当成构造函数使用，如果想要将生成器函数也当成普通的构造函数使用，可以使用call方法将函数绑定到某个对象上；如果想要实现遍历器对象和this对象的统一，可以使用call方法将生成器函数绑定到函数的原型对象上

生成器的应用：

- 实现状态机

- 协程

  > 协程：一种程序运行的方式，可理解为“协作的线程”或者“协作的函数”
  >
  > 多个协程可以交替执行，但是同一时刻执行的协程只能有一个。区别于普通线程，协程的执行权由自己分配，不由执行环境决定。

- 异步操作的同步化表达：

  - 在没有生成器函数的时候，需要同步执行的操作通过回调函数传入
  - 有了生成器函数，需要同步执行的函数可以放在yield语句的下方，从而实现了异步操作的同步化表达

- 控制流的管理

- 利用生成器函数可以在任意对象上部署Iterable接口

- 作为一种数据结构

Thunk函数：

- JS中thunk函数：将多参数函数fun转换成只接受回调函数callback的一个函数，这个用于转换的函数就是Thunk函数，其中callback函数会在fun函数执行完之后被调用具体做法如下

  ```js
  function f(args,callback){
      //...
  }

  function thunk(args){
      return function(callback){
          f(args,callback);
      }
  }

  //...
  const g = thunk(args);
  g(callback);
  ```

- JS Thunk函数转换器：
  
  ```js
  const Thunk = function(fn){
      return function(...args){
          return function(callback){
              return fn.call(this,...args,callback);
          }
      }
  }
  ```

  生产环境中建议使用Thunkify模块

Thunk函数的自动流程管理：

- 定义一个生成器函数gen，生成器函数中有若干异步操作，将这些异步操作都封装成Thunk函数，Thunk函数返回一个只接受callback参数的函数，将这个函数yield出去。【注意】这个只接受callback参数的函数才是真正执行异步操作的函数

- 定义一个run函数作为自动执行生成器函数的函数，在run函数中调用生成器函数gen，获得生成器函数的遍历器

- 在run函数中定义一个next函数，调用gen函数的next方法执行下一步，判断生成器函数是否结束，如果没有结束再调用gen中yield出来的“只接受callback参数的函数”。在run函数中先执行一下next方法。

```js

// 执行异步操作的生成器函数
function* gen(){
    // 定义一些参数
    // ...

    // 这里的doSomething是一个Thunk函数
    // 虽然调用了doSomething，但是，由于是Thunk函数，所以这里真正的异步操作还没有执行
    let f1 = yield doSomething(arg1);//返回值是一个只接受callback参数的函数
    let f2 = yield doSomething(arg2);//返回值是一个只接受callback参数的函数
    // ...
    let fn = yield doSomething(argn);//返回值是一个只接受callback参数的函数
}

// 控制生成器函数执行流程的函数
function run(fn){
    let gen = fn();

    // 控制生成器函数执行下一步
    function next(err,data){
        let result = gen.next(data);//调用生成器的next函数，返回值的value属性就是上面yield的只接受callback参数的函数
        if(result.done) return;
        result.value(next);//调用这个只接受callback参数的函数，将控制生成器函数执行下一步的next函数作为callback传入
        // 这一句执行完之后Thunk函数封装的异步操作才会真正被执行，并且callback是让生成器继续下一步
    }

    // 一开始并不存在上一步，所以手动调用一次
    next();
}

run(gen);
```

co模块：用于生成器函数的自动执行，使用co模块无需编写生成器的执行器

- co函数返回一个Promise对象，可用于添加回调函数

- co模块的原理：Thunk函数和Promise对象都能够将生成器的执行权交回，co模块将这两种执行器包装成一个模块。使用co模块必须注意，只能yield Thunk函数或Promise对象。如果数组或对象的成员全部都是Promsie对象，也可以使用co模块。

- 基于Promise对象的自动执行：将Thunk函数中的回调函数替换为Promise对象的then方法，也能实现生成器函数的自动执行

```js
function run(gen){
    let g = gen();
    function next(data){
        let result = g.next(data);
        if(result.done) return;
        result.value.then(function(data){
            next(data);
        })
    }
    next();
}
```

- co模块支持并发的异步操作，允许某些操作同时进行，得到它们全部完成再执行下一步，这时要把并发的操作放在一个数组/类数组对象里面


## async函数

async函数：一种生成器函数的语法糖

async函数相对于生成器函数的改进有：

- 生成器函数的自动执行必须依赖执行器，所以才有了co模块，async函数自带执行器

- async函数提供了更好的语义

- co模块约定yield命令后面只能是Thunk函数或者Promise对象，async函数中await命令后面可以是Promise对象和原始类型的值

- async函数的返回值是Promise对象，生成器函数的返回值是遍历器对象

用法：

```js

async function hello(){
    let result = await m1();
    let result2 = await m2();
    let result3 = await m3();
    return [result,result2,result3];
}
```

其余用法还有很多，此处略过

语法：

- 返回Promise对象，内部return语句返回的值会作为Promise对象then方法回调函数接收到；async函数内部抛出的错误会导致返回的Promise对象变为rejected状态，抛出的错误对象则会被catch方法回调函数接收到。

- Promise对象的状态变化：只有async函数内部的异步操作执行完，或者遇到return或抛出错误，Promise对象的状态才会发生比变化。

- await命令：正常情况下await命令后接Promise对象，如果不是，则会被转成Promise对象并被立即resolve；await命令后面的Promise对象如果变为rejected状态，则reject的参数会被catch方法的回调函数接收到；在async函数中使用try-catch块可以使得async函数内部抛出的错误在内部捕获，之后的异步操作还可以继续执行

异步遍历器：

- 异步遍历器的最大特点就是，调用遍历器的next方法返回的是一个Promise对象

- 一个对象的异步遍历器部署在`[Symbol.asyncIterator]`上

- 异步遍历器一般会被调用两次：

  - 第一次调用next方法返回一个Promise对象
  - 第二次调用then方法，等到Promise对象resolve之后，再返回一个表示当前数据成员信息的对象

- 异步遍历器的next方法是可以连续调用的，不用等到上一个返回的Promise对象执行完毕之后再调用next方法


`for await ... of`：用于遍历异步遍历器接口，循环遍历异步遍历器指向的Promise对象，一旦这个对象resolve了，就传入循环体中。

- 如果发生了错误，使用try-catch在循环外部捕获

- 该语法也可以适用于同步遍历器的遍历

异步生成器函数：

```js
async function* gen(){
    for(let i=0;i<100;i++){
        yield new Promise(resolve=>setTimeout(resolve,1000,i));
    }
}

for await(const elem of gen()){
    console.log(elem);
}
```

## 修饰器

修饰器：是一个函数，用来修改类的行为

- 修饰器需要携带参数，可以在外面再套一层函数

- 如果想要为类添加一个实例属性，可以调用`target.prototype`

```js
@testable
class MyTestableClass{
    // ...
}

function testable(target){
    target.isTestable = true;
}

MyTestableClass.isTestable = true;

// 如果修饰器需要携带参数，可以这样定义
@testable(true)
class MyTestableClass{
    // ...
}

function testable(isTestable){
    return function(target){
        target.isTestable = isTestable;
    }
}
```
## 模块

### ES6模块的用法

ES6模块与CommonJS的区别：

- ES6模块不是对象，而是通过export命令显式指定输出的代码，再通过import命令输入，可以实现静态加载

- 不再需要UMD模块格式

- 不再需要做成全局变量或者navigator对象的属性

- 不再需要对象作为命名空间

import和export命令：

```js
// 输出变量
export const fisrtName = 'Michael';

// 输入变量
import {firstName} from './xxx.js';

// 输出多个变量
const a=1;
const b=1;
export {a,b}

// 输入多个变量
import {a,b} from './xxx.js'

// 输出类
export class A{
    // ...
}


// 输出函数
export function hello(){
    // ...
}


// 输出时重新命名
function v1(){}
function v2(){}
export {
    v1 as streamV1,
    v2 as streamV2,
}

// export命令规定的是对外的接口，因此必须与模块内部的变量建立一一对应关系
// 下面的写法是错误的
export 1;
var m = 1;
export m;

// 输入时重新指定一个0.

```

- export语句输出的接口与其对应的值是动态绑定关系，即通过该接口可以取到模块内部实时的值。这一点区别于CommonJS

- export命令可以出现在模块的任何位置，只要处于模块顶层就可以，但是不能处于块级作用域内。

- import命令会提升至整个模块的头部执行

- import命令是静态执行，所以不能使用表达式和变量

- import命令可以只执行模块的加载，不引入对象或变量

```js
import 'lodash';
```

模块的整体加载：

```js
import * as circle from './circle';

console.log('the area of the circle: '+circle.area(4));
console.log('the circumustance of the circle:'+circle.circumference(4));
```

- 模块整体加载得到的变量不允许运行时改变

默认输出指令：

```js
//1.js
export default function(){
    // ...
}

//2.js
export default class A{
    // ...
}

//main.js
import Func from './1.js';
import A from './2.js';
// ...
```

- 一个模块只能有一个默认输出

- 本质上默认输出会输出一个default的变量或方法，所以export default命令后面不能跟变量的声明，export default命令会将后面的表达式值赋值给default变量

- 同理，export命令后面也不能直接跟一个字面值常量，必须指定对外输出的变量

- import命令可以同时输出默认变量和普通变量

```js
import _,{each,each as forEach} from 'lodash';
```

export和import的复合写法：

```js

export { foo,bar} from 'my_module';

// 等同于
import { foo,bar } from 'my_module';
export { foo,bar };
```

具名接口可以改为：

```js
export { foo as foo1, bar as bar1} from 'my_module';

// 等同于
import { foo as foo1, bar as bar1} from 'my_module';
export { foo1,bar1};
```

模块继承

```js
export * from 'my_module';
// ...
```

`export *`命令会忽略模块内的default

import():

- import命令静态导入，使用`import()`可以实现运行时模块导入。

- import函数返回一个Promise，可以设置导入成功或失败时的回调函数，导入的模块会作为一个对象传入回调函数

- 适用场合：按需加载、条件加载、模块路径的动态导入

### 模块的加载实现

#### 浏览器加载：

1、传统加载

```html
<!-- 页面内嵌的脚本 -->
<script type="application/javascript">
    // module code
</script>

<!-- 外部脚本 -->
<script type="application/javascript" src="path/to/my_module.js"></script>
```

异步加载的语法：

```html
<script src="path/to/my_module.js" defer></script>
<script src="path/to/my_module.js" async></script>
```

defer是渲染完后执行，async是下载完后执行，async脚本不能保证加载顺序。


2、加载规则

浏览器加载ES6模块时也使用`<script>`标签，但是要加入`type = "module"`属性

```html
<script type="module" src="foo.js"></script>
```

带有`type = "module"`属性的脚本，浏览器都是异步加载的，即等到整个页面渲染完成后再执行模块脚本。

`<script>`标签的async属性也可以打开，这时只要加载完成，渲染引擎就会中断渲染立即执行，执行完成后再恢复渲染。

ES6模块也允许内嵌在网页中，但是要加上`type="module"`属性。

对于html页面引入的外部脚本，需要注意以下几点：

- 代码是在模块作用域中运行，而不是在全局作用域中运行。模块内部的顶层变量是外部不可见的。
- 模块脚本自动采用严格模式，无论有没有声明`use strict`
- 模块之中可以使用`import/export`命令（`.js`后缀不可省略，需要提供绝对url或者相对url）
- 在模块之中顶层的`this`关键字返回`undefined`
- 同一个模块如果加载多次，将只执行一次

#### ES6模块与CommonJS模块的差异：

- CommonJS模块输出的是一个值的复制，ES6模块输出的是一个值的引用
- CommonJS模块是运行时加载，ES6模块是编译时输出接口

由于ES6模块输出的只是一个“引用”，因此输出的变量是不允许赋值

`export`通过接口输出的是同一个值，不同的脚本加载这个接口得到的都是相同的实例。

#### Node加载

一个JS脚本如果包含`import/export`命令，就被Node认为是一个ES6模块，否则就被Node认为是一个CommonJS模块。如果想让Node认为是一个ES6模块，即便没有输出接口，也需要加上一句`export {};`。

引入一个模块时，如果不指定绝对路径，也不指定`./ ../`，那么Node会以当前脚本所在目录开始，逐级上溯，按照`模块名.js 模块名/package.json 模块名/index.js`的次序寻找脚本。

import命令加载CommonJS模块：

```js
// a.js
module.exports = {
    foo: 'hello',
    bar: 'world'
};

// 等同于
export default {
    foo: 'hello',
    bar: 'world
}
```

CommonJS的输出缓存机制在ES6 import中依然适用。

require命令加载ES6模块：

采用require命令加载ES6模块时，ES6模块的所有输出接口都会成为输入对象的属性

由于CommonJS存在缓存机制，模块内`export`语句之后对输出变量的改变不会反映在模块外部

#### 循环加载

CommonJS模块的加载原理：CommonJS一个模块就是一个脚本文件，require命令第一次加载脚本的时就会执行整个脚本，然后在内存中生成一个对象，将模块输出的接口都放在`exports`属性的下边。以后再次加载模块时，都不会再执行该模块，直接到`exports`属性下取值

CommonJS模块解决循环加载：A加载B时会先停下来执行B，B又加载A，A尚未完成，B只会得到已经输出的部分

基于此，使用CommonJS输入变量的时候格外小心：

```js
const a = require('a'); // 安全的写法
const foo = require('a').foo; // 危险的写法

exports.good = function(args){
    return a.foo('good',args); // 使用的是最新的值
}

exports.bad = function(args){
    return foo('bad',args); // 使用的是一个部分加载的值
}
```

ES6模块的加载原理：动态引用，输出的变量不会被缓存，而是成为一个指向被加载模块的引用。

ES6模块的转码：es6-module-transpiler、SystemJS


