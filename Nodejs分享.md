# Nodejs分享

## 什么是Nodejs

2009.3	Ryan Dahl

基于事件驱动、非阻塞IO、WEB服务器

Chrome与Nodejs的联系与区别：

- Chrome中使用V8作为Javascript引擎，使用Webkit作为布局引擎，Chrome中Javascript只能限制于Chrome沙箱中运行（不能访问本地文件）
- Nodejs中也使用V8作为Javascript引擎，但是没有浏览器的限制，可以访问本地资源，libuv

Nodejs的一些特点：

- 异步非阻塞IO：Don't call me，I will call you。发起IO请求之后不等待调用结束继续执行
- 事件：普遍采用事件机制，配合异步IO，将事件点暴露给业务逻辑
- 单线程：
  - 优点：避免出现多线程编程线程同步、死锁等问题
  - 缺点：多核支持、健壮性、大量计算占用CPU等
- 跨平台：基于libuv实现跨平台，libuv用来兼容不同操作系统

Nodejs的学习资料：

官网教程：http://nodejs.cn/learn/introduction-to-nodejs

API文档：  http://nodejs.cn/api/documentation.html

朴灵 《深入浅出Node.js》

异步编程《 Javascript高级程序设计》、阮一峰《ECMAScript 6 入门教程》

Node.js 如何处理 ES6 模块 https://www.ruanyifeng.com/blog/2020/08/how-nodejs-use-es6-module.html

## 模块机制

两种模块机制：

- CommonJS规范
- ES6规范 import export

CommonJS规范：

1、模块引用：require

2、模块定义：

- exports：用于导出当前模块的方法或变量
- module：指代当前模块，exports是module的属性

3、模块标识：模块名、相对路径、绝对路径

Node加载和编译模块的过程：路径分析 => 文件定位 => 编译执行

- 模块的分类

  - 核心模块：Node提供的模块，Node进程启动之后直接加载进内存，省略了路径分析和文件定位步骤，在路径分析中优先判断，加载速度最快
  - 文件模块：运行时动态加载，需要经历完整的路径分析、文件定位和编译执行步骤。

模块的缓存： 优先从缓存加载，Node缓存的是编译和执行后的对象（浏览器缓存的则是文件）

### 路径分析与文件定位

模块标识符分析：

- 核心模块：优先级仅次于缓存加载的模块，自定义模块的标识符不能与核心模块的标识符相同
- 路径形式的文件模块：以`/`开头的路径是绝对路径，以`.`或`..`开头的路径是相对路径，require函数会将路径转换为模块文件的真实路径，并以此作为缓存的索引。路径形式的文件模块给出了准确的文件路径，查找起来比非路径形式的文件模块更快
- 非路径形式文件模块（自定义模块）：由用户编写，非路径形式呈现，也不是核心模块


模块路径：定位文件模块时的具体查找策略，表现为一个数组，`module.paths`属性记录了模块路径。

模块路径的生成规则为：

```js
if 当前目录是根目录：
模块路径 := [当前文件目录下的node_modules目录]
else:
模块路径 = [当前文件目录下的node_modules目录, ...父目录的模块路径]
```

文件定位：针对给定的路径，Node会按照以下的规则定位文件

- 文件分析：

  ```js
  匹配<文件名> => 匹配<文件名>.js => 匹配<文件名>.json => 匹配<文件名>.node
  ```

- 目录分析：

  ```python
  if package.json文件存在:
      读取package.json文件
      return main属性指定文件名
  elif index.js文件存在:
      return index.js文件
  elif index.node文件存在:
      return index.node文件
  elif index.json文件存在:
      return index.json文件
  else
  	return
  ```

### 模块编译

1、JS模块的编译

模块编译：定位到模块文件之后，进行模块的编译，创建模块对象module

```js
function Module(id, parent) { 
    this.id = id; 
    this.exports = {}; 
    this.parent = parent;
    if (parent && parent.children) { 
        parent.children.push(this); 
    }
    this.filename = null;
    this.loaded = false; 
    this.children = []; 
}
```



不同扩展名文件的加载方式：

- .js文件：通过fs模块读取文件后编译执行
- .node文件：通过c/c++编写的扩展文件，通过dlopen方法加载最后生成的文件
- .json文件：通过fs模块读取文件后，使用JSON.parse方法解析返回结果
- 其余扩展名文件：当作js文件处理

编译成功的模块会被缓存在Module._cache对象上

require.extensions：系统中已有的扩展加载方式

Node加载每一个模块文件时，会将不同模块的内容包装在函数作用域中，避免污染全局作用域。

```js
(function(exports,require,module,__filename,__dirname){
 	// ...模块文件的内容，引用export形参可以导出属性
 });
```

vm.runInThisContext函数：编译和执行Javascript代码，但是不会影响本地作用域（使用eval会影响本地作用域）

- 形参：要编译和执行的Javascript代码（该方法还有第2、3个参数，此处略）
- 返回值：最后一个语句执行后返回的结果

  ```js
  const vm = require('node:vm');
  let localVar = 'initial value';
  
  const vmResult = vm.runInThisContext('localVar = "vm";');
  console.log(`vmResult: '${vmResult}', localVar: '${localVar}'`);
  // 打印: vmResult: 'vm', localVar: 'initial value'
  
  const evalResult = eval('localVar = "eval";');
  console.log(`evalResult: '${evalResult}', localVar: '${localVar}'`);
  // 打印: evalResult: 'eval', localVar: 'eval'
  ```

经过包装的模块代码由vm.runInThisContext函数执行，返回的是一个具体的函数。

执行这个函数，将当前模块的exports属性、require方法、module模块对象自身、模块文件完整的文件名和文件目录作为参数传入。



2、C/C++模块的编译

通过`process.dlopen()`方法加载和执行C/C++模块，底层通过libuv兼容不同操作系统



3、JSON文件的编译：

Node利用`fs`模块同步读取JSON文 件的内容之后，调用`JSON.parse()`方法得到对象，然后将它赋给模块对象的 `exports`，以供外部调用。 

JSON文件在用作项目的配置文件时比较 有用。如果你定义了一个JSON文件作为 配置，那就不必调用fs模块去异步读取 和解析，直接调用`require()`引入即可。



### 核心模块

核心模块：

- C/C++部分：src目录下，直接加载进内存，不需要像文件模块一样查找文件并加载
- JavaScript部分：lib目录下，JavaScript核心模块源文件通过`process.binding('natives')` 取出，编译成功的模块缓存 到`NativeModule._cache`对象上，文件模块则 缓存到`Module._cache`对象上



## npm包

包和npm是将模块联系起来的一种机制

安装所有依赖，依赖定义在`package.json`

```sh
npm install
```

安装单个软件包

```sh
npm install <package-name> # 安装软件包但是不添加条目到package.json中

npm install <package-name> --save # 安装软件包并添加条目到package.json文件的dependencies

npm install <package-name> --save-dev # 安装软件包并添加条目到package.json文件的devDependencies
```

更新软件包

```sh
npm update
```

npm还支持版本控制，可以显式指定库的版本

运行任务

```sh
npm run <task-name>
```

```js
// package.json
{
  "scripts": {
    "start-dev": "node lib/server-development",
    "start": "node lib/server-production"
  },
}
```

两种安装类型：

- 本地安装
- 全局安装

```sh
npm install lodash # 本地安装
npm install -g lodash # 全局安装
npm root -g # 全局安装的位置
```

使用安装包：

- 如果是模块，直接导入即可
- 如果是可执行文件，直接放到node_modules下的.bin目录下,使用`npx`执行

package.json

```js
{
  "name": "test-project", // 应用/软件包的名称
  "author": "NodeJS中文网 <mail@nodejs.cn> (http://nodejs.cn)" // 作者
  /*"author": {
    "name": "NodeJS中文网",
    "email": "mail@nodejs.cn",
    "url": "http://nodejs.cn"
  },*/
  "contributors": ["NodeJS中文网 <mail@nodejs.cn> (http://nodejs.cn))"],//贡献者
  /*"contributors": [
    {
      "name": "NodeJS中文网",
      "email": "mail@nodejs.cn",
      "url": "http://nodejs.cn"
    }
  ]，*/
  "bugs": "https://github.com/nodejscn/node-api-cn/issues", // 跟踪软件包缺陷
  "homepage": "http://nodejs.cn",// 设置软件包的首页
  "license":"MIT",//指定软件包的许可证
  "keywords": [ //此属性包含与软件包功能相关的关键字数组
    "email",
    "machine learning",
    "ai"
  ],
  "repository":"github:nodejscn/node-api-cn",//指定了程序包仓库所在的位置
  /*"repository": {
    "type": "git",
    "url": "https://github.com/nodejscn/node-api-cn.git"
  },
  "repository": {
    "type": "svn",
    "url": "..."
  },*/
  "version": "1.0.0", // 表明了当前项目的版本
  "description": "A Vue.js project", // 应用或软件包的简短描述
  "main": "src/main.js", // 应用程序的入口点
  "private": true, // 如果设置为true，可以防止应用程序/软件包被意外地发布到npm
  "scripts": { // 定义了一组可以运行的node脚本
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "unit": "jest --config test/unit/jest.conf.js --coverage",
    "test": "npm run unit",
    "lint": "eslint --ext .js,.vue src test/unit",
    "build": "node build/build.js"
  },
  "dependencies": { // 设置了作为依赖安装的npm软件包的列表
    "vue": "^2.5.2"
  },
  "devDependencies": { // 设置了作为开发依赖安装的npm软件包的列表
    "autoprefixer": "^7.1.2",
    "babel-core": "^6.22.1",
    "babel-eslint": "^8.2.1",
    "babel-helper-vue-jsx-merge-props": "^2.0.3",
    "babel-jest": "^21.0.2",
    "babel-loader": "^7.1.1",
    "babel-plugin-dynamic-import-node": "^1.2.0",
    "babel-plugin-syntax-jsx": "^6.18.0",
    "babel-plugin-transform-es2015-modules-commonjs": "^6.26.0",
    "babel-plugin-transform-runtime": "^6.22.0",
    "babel-plugin-transform-vue-jsx": "^3.5.0",
    "babel-preset-env": "^1.3.2",
    "babel-preset-stage-2": "^6.22.0",
    "chalk": "^2.0.1",
    "copy-webpack-plugin": "^4.0.1",
    "css-loader": "^0.28.0",
    "eslint": "^4.15.0",
    "eslint-config-airbnb-base": "^11.3.0",
    "eslint-friendly-formatter": "^3.0.0",
    "eslint-import-resolver-webpack": "^0.8.3",
    "eslint-loader": "^1.7.1",
    "eslint-plugin-import": "^2.7.0",
    "eslint-plugin-vue": "^4.0.0",
    "extract-text-webpack-plugin": "^3.0.0",
    "file-loader": "^1.1.4",
    "friendly-errors-webpack-plugin": "^1.6.1",
    "html-webpack-plugin": "^2.30.1",
    "jest": "^22.0.4",
    "jest-serializer-vue": "^0.3.0",
    "node-notifier": "^5.1.2",
    "optimize-css-assets-webpack-plugin": "^3.2.0",
    "ora": "^1.2.0",
    "portfinder": "^1.0.13",
    "postcss-import": "^11.0.0",
    "postcss-loader": "^2.0.8",
    "postcss-url": "^7.2.1",
    "rimraf": "^2.6.0",
    "semver": "^5.3.0",
    "shelljs": "^0.7.6",
    "uglifyjs-webpack-plugin": "^1.1.1",
    "url-loader": "^0.5.8",
    "vue-jest": "^1.0.2",
    "vue-loader": "^13.3.0",
    "vue-style-loader": "^3.0.1",
    "vue-template-compiler": "^2.5.2",
    "webpack": "^3.6.0",
    "webpack-bundle-analyzer": "^2.9.0",
    "webpack-dev-server": "^2.9.1",
    "webpack-merge": "^4.1.0"
  },
  "engines": { // 设置了此软件包/应用程序在那个版本的Nodejs上运行
    "node": ">= 6.0.0",
    "npm": ">= 3.0.0"
  },
  "browserslist": ["> 1%", "last 2 versions", "not ie <= 8"] // 用于告知要支持哪些浏览器及其版本
}
```

package-lock.json文件：跟踪被安装的每个软件包的确切版本，以便产品可以以相同的方式被 100％ 复制（即使软件包的维护者更新了软件包）。package-lock.json 会固化当前安装的每个软件包的版本，当运行 npm install时，npm 会使用这些确切的版本

查看npm包安装的版本

```sh
npm list # 查看本地安装软件包的版本
npm list -g # 查看全局安装软件包的版本
npm list --depth=0 # 查看嵌套层次为0时的软件包版本
npm list cowsay # 通过指定名称来获取特定软件包的版本
npm view cowsay version # 查看软件包在npm仓库上最新可用的版本
```

安装npm包的旧版本

```sh
npm install cowsay@1.2.0 # 安装软件包的旧版本
npm install -g cowsay@1.2.0 # 全局安装软件包的旧版本
npm view cowsay versions # 列出软件包所有的以前的版本
```

依赖包的更新

```sh
npm outdated # 发觉软件包的新版本

# 更新主版本，先安装npm-check-updates
ncu -u
npm update
```

npm语义控制版本

```sh
主版本.次版本.补丁版本
```

当进行不兼容的API更改时，升级主版本

当以向后兼容的方式添加功能时，则升级次版本

当进行向后兼容的缺陷修复时，则升级补丁版本

```sh
^            # 只执行不更改最左边非零数字的更新
~            # 只更新补丁版本
> >= <= < =  # 接受高于/高于等于/低于等于/低于/等于
-            # 接受一定范围的更新
||           # 组合集合 例如 < 2.1 || > 2.6
无符号        # 仅接受指定的特定版本
latest       # 使用可用的最新版本
```

卸载软件包

```sh
npm uninstall <package-name>

npm uninstall -S <package-name>
npm uninstall --save <package-name>

npm uninstall -D <package-name>
npm uninstall -save-dev <package-name>

npm uninstall -g <package-name>
```

npx包运行器

```sh
npx <command-name>
npx cowsay "你好"

# 使用不同的Nodejs版本运行代码
npx node@10 -v #v10.18.1
npx node@12 -v #v12.14.1

# 直接从 URL 运行任意代码片段
npx https://gist.github.com/zkat/4bc19503fe9e9309e2bfaa2c58074d3
```

## 异步IO

异步调用：

- 发起异步调用

- 封装请求对象

  > 请求对象：JS发起调用到内核执行完IO操作的过渡状态中存在的一种中间产物

- 设置参数和回调函数

- 将请求对象放入线程池等待执行

线程池：

- 获得可用的线程资源
- 执行请求对象中的IO操作
- 将执行完成的结果放在请求对象中
- 调用完成，归还线程

事件循环：

- 创建主循环：在进程启动时，Node便会创建一个类似于 while(true)的循环，每执行一次循环体的过程我们称为Tick。

- 获取请求对象：每个Tick的过程就是查看是否有事件待处理，如果有，就取出事件及其相关的回调函数。如果存在关联的回调函数，就执行它们。

> 观察者：每个事件循环中有一个或者多个观察者，判断是否有事件处理的过程就是向这些观察者询问是否有要处理的事件。
>
> 事件循环是典型的生产者-消费者模型，异步IO、网络请求是事件的生产者，事件循环从观察者处消费事件

- 取出回调函数和结束调用执行：然后进入下个循环，如果不再有事件处理，就退出进程。

观察者：。

- 获取完成的IO交给IO观察者



非IO的异步API：

```js
setTimeout() // 与浏览器中的函数作用相同，单次执行定时任务
setInterval() // 与浏览器中的函数作用相同，多次重复执行定时任务
process.nextTick() // 将回调函数延迟至事件循环的下一次Tick时执行，与setTimeout(fn,0)的区别在于该函数更轻量级，无需操作红黑树数据结构
setImmediate() // 将回调函数延迟至事件循环的下一次Tick时执行，但是优先级要低于process.nextTick()


function hello(){
	this.$refs.form1.validate(valid=>{
        this.$refs.form2.validate(valid=>{
            his.$refs.form2.validate(valid=>{
                
            })
        })
    })
}

async function hello(){
    const valid1 = await new Promise(resolve=>this.$refs.form1.validate(resolve));
    // valid1判断
    const valid2 = await new Promise(resolve=>this.$refs.form1.validate(resolve));
    // valid2...
    const valid3 = await new Promise(resolve=>this.$refs.form1.validate(resolve));
    // valid3...
}

```

异步编程：Promise、Generator、async/await

## Node常用的模块

### http

```js
const http = require('http')

const port = 3000

const server = http.createServer((req, res) => {
  res.statusCode = 200
  res.setHeader('Content-Type', 'text/plain')
  res.end('你好世界\n')
})

server.listen(port, () => {
  console.log(`服务器运行在 http://${hostname}:${port}/`)
})
```

执行GET请求：

```js
const https = require('https')
const options = {
  hostname: 'nodejs.cn',
  port: 443,
  path: '/todos',
  method: 'GET'
}

const req = https.request(options, res => {
  console.log(`状态码: ${res.statusCode}`)

  res.on('data', d => {
    process.stdout.write(d)
  })
})

req.on('error', error => {
  console.error(error)
})

req.end()
```

执行POST请求：

```js
const https = require('https')

const data = JSON.stringify({
  todo: '做点事情'
})

const options = {
  hostname: 'nodejs.cn',
  port: 443,
  path: '/todos',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': data.length
  }
}

const req = https.request(options, res => {
  console.log(`状态码: ${res.statusCode}`)

  res.on('data', d => {
    process.stdout.write(d)
  })
})

req.on('error', error => {
  console.error(error)
})

req.write(data)
req.end()
```

### fs模块

open  打开文件，参数包括文件路径、打开方式、回调函数

```js
const fs = require('fs')

fs.open('/Users/joe/test.txt', 'r', (err, fd) => {
  //fd 是文件描述符。
})

// 打开方式 r w a +
```

openSync 同步打开文件，返回文件描述符

```js
const fs = require('fs')

const fd = fs.open('/Users/joe/test.txt', 'r')

```

stat()  获得文件属性信息

```js
fs.stat('path/to/file',(err,stats)=>{
    stats.isFile() //true
    stats.isDirectory() //false
    stats.isSymbolicLink() //false
    stats.size //1024000 //= 1MB
})
```

statSync()  获得文件属性信息同步版本

文件路径：

```js
const path = require('path')
const notes = '/users/joe/notes.txt'

path.dirname(notes) // /users/joe
path.basename(notes) // notes.txt
path.extname(notes) // .txt

// 指定第二个参数来获取不带扩展名的文件名
path.basename(notes, path.extname(notes)) //notes 

// 连接路径
const name = 'joe'
path.join('/', 'users', name, 'notes.txt') //'/users/joe/notes.txt' 

//相对路径的绝对路径计算
path.resolve('joe.txt')

// 规范化
path.normalize('/users/joe/..//test.txt') //'/users/test.txt'
```

读写文件

```js
const fs = require('fs')

// 异步读文件
fs.readFile('/Users/joe/test.txt', 'utf8' , (err, data) => {
  if (err) {
    console.error(err)
    return
  }
  console.log(data)
})

// 同步读文件
try {
  const data = fs.readFileSync('/Users/joe/test.txt', 'utf8')
  console.log(data)
} catch (err) {
  console.error(err)
}

// 异步写文件

const content = '一些内容'

fs.writeFile('/Users/joe/test.txt', content, err => {
  if (err) {
    console.error(err)
    return
  }
  //文件写入成功。
})

// 同步写文件

const content = '一些内容'

try {
  const data = fs.writeFileSync('/Users/joe/test.txt', content)
  //文件写入成功。
} catch (err) {
  console.error(err)
}

fs.writeFile('/Users/joe/test.txt', content, { flag: 'a+' }, err => {})

// 追加文件
const content = '一些内容'

fs.appendFile('file.log', content, err => {
  if (err) {
    console.error(err)
    return
  }
  //完成！
})
```

目录操作：

```js
fs.access()
fs.mkdir(dir)
fs.mkdirSync(dir)
fs.exists(folderName)
fs.existsSync(folderName)
fs.readdir()
fs.readdirSync()
fs.rename()
fs.renameSync()
fs.rmdir()
fs.rmdirSync()
```

fs的直接替代品fs-extra

### Buffer

创建Buffer：

```js
Buffer.from(array)
Buffer.from(arrayBuffer[, byteOffset[, length]])
Buffer.from(buffer)
Buffer.from(string[, encoding])

const buf = Buffer.alloc(1024) // 使用零值初始化
const buf = Buffer.allocUnsafe(1024) // 不使用零值初始化
```

Buffer可以像数组一样被访问

更改Buffer的内容

```js
const buf = Buffer.alloc(4)
buf.write('Hey!')
```

复制Buffer

```js
const buf = Buffer.from('Hey!')
let bufcopy = Buffer.alloc(4) //分配 4 个字节。
buf.copy(bufcopy)

// 默认情况下，会复制整个 buffer。 另外的 3 个参数可以定义开始位置、结束位置、以及新的 buffer 长度
const buf = Buffer.from('Hey!')
let bufcopy = Buffer.alloc(2) //分配 2 个字节。
buf.copy(bufcopy, 0, 0, 2)
bufcopy.toString() //'He'
```

切片 buffer

```js
const buf = Buffer.from('Hey!')
buf.slice(0).toString() //Hey!
const slice = buf.slice(0, 2)
console.log(slice.toString()) //He
buf[1] = 111 //o
console.log(slice.toString()) //Ho
```