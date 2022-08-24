# express框架学习笔记

## 快速入门

1. HelloWorld：

```js
const express = require('express') // 引入依赖
const app = express() // 创建实例
const port = 3000 // 监听的端口

// 设置get请求接口
app.get('/', (req, res) => {
  res.send('Hello World!')
})

// 执行监听端口
app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

2. Express应用程序生成器：express-generator

- 运行应用程序生成器

```shell
npx express-generator

# 对于较老版本的node，可以使用npm将express应用程序生成器安装到全局环境中并执行即可
npm install -g express-generator
express
```

- 安装所有依赖包

```sh
npm install
```

- 启动应用

```shell
# 在linux/maxos中执行
DEBUG=myapp:* npm start

# 在windows命令行执行
set DEBUG=myapp:* & npm start

# 在windwos Powershell中执行
$env:DEBUG='myapp:*'; npm start
```

3. 路由基础

定义路由：

```js
app.METHOD(PATH,HANDLER)

// app是一个express实例
// METHOD是请求方式，小写，如get、post、put、delete
// PATH是服务器路径
// HANDLER是路由匹配成功的处理器
```

4. 托管静态文件

```js
express.static(root[,options])
// root是存放静态资源的目录
// options是相关的配置
```

```js
app.use(express.static('public'))
```

使用多个静态资源目录，可以多次调用static静态资源中间件函数

可以为静态资源目录指定一个虚拟路径：

```js
app.use('static',express.static('public'));
```

一般情况下不使用相对于当前文件的目录，而是使用项目的上下文目录：
```js
app.use('static',express.static(path.join(__dirname,'public')));
```

## 路由

```js
import express from 'express';
const app = express();

app.get('/',(req,res)=>{
    res.send('hello world')
})
```

能够支持所有请求类型的路由方法：

```js
app.all('/secret',(req,res,next)=>{
    // ...
    next(); // 将控制权交给下一个处理器
})
```

路由路径可以是字符串、字符串模板和JS正则表达式

`? + * ( )`是它们对应正则表达式的子集，`- .`在字符串路径中则会被当成普通字符解释

> 字符串中的?表示出现0或1次，+表示至少出现1次，*表示任意字符串，()会把包围的内容当成整体。JS正则中的符号还是原来的含义

在路径字符串中使用美元符（$），要使用`([])`包围，并使用`\`进行转义

```
/data/$book

转移后：
/data/([\$])book
```

路径参数：

```js
app.get('users/:userId/books/:bookId',(req,res)=>{
    const {params:{userId,bookId}} = res;
    console.log(`userId = ${userId}, bookId = ${bookId}`);
    res.send("get params");
});
```

尽管连字符`-`和点`.`在字符串路径中被解释为普通字符，当时可以用于分割路径参数

为了更精确地控制路径参数匹配，可以使用`()`在后面附加一个正则表达式

```js
app.get('user/:userId(\\d+)',(req,res)=>{})
```

路由处理器：

可以提供多个类中间件的函数来处理请求，唯一的例外是这些回调函数可能会调用`next('route')`来绕过剩余的路由回调

路由处理器可以以函数，函数数组或二者的混合来提供

```js
// 函数
app.get('/example/b', function (req, res, next) {
  console.log('the response will be sent by the next function ...')
  next()
}, function (req, res) {
  res.send('Hello from B!')
})

// 函数数组
var cb0 = function (req, res, next) {
  console.log('CB0')
  next()
}

var cb1 = function (req, res, next) {
  console.log('CB1')
  next()
}

var cb2 = function (req, res) {
  res.send('Hello from C!')
}

app.get('/example/c', [cb0, cb1, cb2])

// 混合
var cb0 = function (req, res, next) {
  console.log('CB0')
  next()
}

var cb1 = function (req, res, next) {
  console.log('CB1')
  next()
}

app.get('/example/d', [cb0, cb1], function (req, res, next) {
  console.log('the response will be sent by the next function ...')
  next()
}, function (req, res) {
  res.send('Hello from D!')
})
```

响应对象的方法：


|Method|	Description|
|--|--|
|res.download()	|推送一个将被下载的文件|
|res.end()	|结束处理响应的进程|
|res.json()	|发送一个JSON响应|
|res.jsonp()	|发送一个支持JSONP的JSON响应|
|res.redirect()	|请求重定向|
|res.render()	|渲染视图模板|
|res.send()	|发送一个类型不定的响应|
|res.sendFile()	|以八进制流的形式发送文件|
|res.sendStatus()	|设置响应码并将其字符串的表示作为响应的主体body|

```js
// 下载
router.get('/file',(req,res,next)=>{
  res.download('C:/Users/lenovo/Desktop/111.txt','helloworld',err=>{
    console.log(err)
  })
})

// 结束处理响应的进程
router.get('/status',(req,res,next)=>{
  res.status(406).end();
})








