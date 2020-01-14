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

箭头函数  ()=>{}

1、如果只有一个参数，圆括号可以省略

2、如果只有一个return，花括号可以省略

3、修正了this指向问题，箭头函数的this由上下文函数的调用者绝决定

参数扩展：

`function show(a,b,...args)`

数组展开：

`show(...arr);`

默认参数：同c++

## 解构赋值

1、左右两边结构必须一样

2、右边必须是个正确的结构

3、声明和赋值不能分开

```js
let [a,b,c]=[1,2,3];
let {a,b,c}={aaa:1,bbb:2,ccc:3};
let [{a,b},[n1,n2,n3],num,str]=[{a:12,b:5},[12,5,8],8,'cxzcv'];
```

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

## 字符串

两个新方法

- startsWith

- endsWith

字符串模板

```js
let aaa = 2;
let str = `a${aaa}bc`;
```

## 面向对象

面向对象：

1、class关键字、构造器和类分开了

2、class里面直接加方法

```js
class User{
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

## json

JSON对象：

- JSON.stringify()
- JSON.parse()

json标准写法：只能用双引号、所有的名字都必须用引号引起来。

简写：名字和值一样、方法省略:function

## Promise

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

其他方法：

Promise.race()  “先入咸阳者王之”

## generator

普通函数：顺序执行，一路到底

generator函数：可以中途停顿

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

### yield

yield既可以传递参数，又可以返回

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

