# NodeJS

### 1、从命令行运行nodejs脚本

```sh
node app.js
```

### 2、从nodejs退出

- ctrl-c
- 程序中使用`process.exit(1)`，默认退出码为0，表示成功。 其他退出码 http://nodejs.cn/api/process.html#process_exit_codes
- 设置`process.exitCode = 1`

### 3、读取环境变量

```js
process.env.NODE_ENV
```

### 4、使用nodejs repl

- 执行js脚本
  
- 省略文件名，进入repl
  
  - tab自动补全
  - 特殊变量 `_`
  - 点命令  使用.help查看点命令

### 5、从命令行接收参数：

```
node app.js
node app.js joe
node app.js name=joe
```

```js
process.argv.forEach((val,index)=>{
    console.log(`${index}: ${val}`);
});
```

建议使用minimist库处理参数

### 6、输出到命令行

普通输出

```js
let x = 'hello';
let y = 'world';
console.log(x,y);
```

格式化输出

```js
console.log('我的%s已经%d岁了','猫',2);
// 格式化符号 %s %d %i %o
```

清空控制台

```js
console.clear(); // 清空控制台
```

计数器

```js
let str = 'hello';
let str2 = 'world';
console.count(str);// hello: 1
console.count(str2);// world: 1
console.count(str);// hello: 2
console.count(str2);// world: 2
```

打印堆栈轨迹

```js
const f1 = ()=>console.trace();
const f2 = ()=>f1();
f2();
/*
Trace
    at f1 (repl:1:24)
    at f2 (repl:2:16)
    at repl:3:1
    at Script.runInThisContext (vm.js:120:18)
    at REPLServer.defaultEval (repl.js:433:29)
    at bound (domain.js:427:14)
    at REPLServer.runBound [as eval] (domain.js:440:12)
    at REPLServer.onLine (repl.js:760:10)
    at REPLServer.emit (events.js:327:22)
    at REPLServer.EventEmitter.emit (domain.js:483:12)
*/
```

计算耗时

```js
async function fun(){
    // 计时开始和计时结束必须使用相同的标签
    console.time('dosomething');
    await new Promise(resolve=>setTimeout(resolve,5000));
    console.timeEnd('dosomething');
}

fun();
```

标准错误输出

```js
console.error('操作失败');
```

为输出着色

```js
//使用转义序列
console.log('\x1b[33m%s\x1b[0m', '你好')

// 建议使用chalk库
const chalk = require('chalk')
console.log(chalk.yellow('你好'))
```

创建进度条：

使用progress软件包

### 7、从命令行接收输入

从7版本开始引入readline模块

```js
const readline = require('readline').createInterface({
  input: process.stdin,
  output: process.stdout
})

readline.question(`你叫什么名字?`, name => {
  console.log(`你好 ${name}!`)
  readline.close()
})
```

其他用于输入的库：`readline-sync`、`inquirer`

### 8、node的模块系统

使用`require`和`module.exports`相结合

### 9、npm包管理器

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

### 10、事件循环

Nodejs运行在单线程上，所有IO基元都是非阻塞的

调用堆栈：js每次调用函数时，会将函数压入调用堆栈，函数正常/异常返回之后出栈

消息队列：

- 调用setTimeout时，浏览器/Node会启动定时器，定时器超时时会将回调函数放入消息队列中
- 用户触发的事件也会在消息队列中排队
- 事件循环赋予调用堆栈优先级，首先处理调用堆栈中的任务，一旦没有任何东西便处理消息队列中的任务

作业队列：

ES6中引入，会尽快执行异步函数的结果，而不是放在调用堆栈的末尾。在当前函数结束之前resolve的Promise会在当前函数之后被立即执行

process.nextTick()：指示引擎在当前操作结束（下一个事件循环滴答开始之前）调用此函数

setImmediate()：异步（但是要尽可能快）执行某些代码时使用，同样是在事件循环的下一个迭代中执行

EventEmitter类：

```js
const EventEmitter = require('events');
const eventEmitter = new EventEmitter();
eventEmitter.on('start',(number)=>{
  console.log('开始')
});
eventEmitter.emit('start',1);
```

其他事件交互方法：

- `once()`：添加单次监听器
- `removeListener()/off()`：从事件中移除事件监听器
- `removeAllListeners()`：移除事件的所有监听器


### 11、搭建HTTP服务器

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

### 12、文件

使用fs模块

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

### 13、Buffer

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

### 14、流

使用fs模块读写文件时，会先将文件全部读入内存，这样会花费大量时间

```js
const http = require('http')
const fs = require('fs')

const server = http.createServer((req, res) => {
  const stream = fs.createReadStream(__dirname + '/data.txt') // 创建读文件流
  stream.pipe(res)
})
server.listen(3000)
```

pipe：获取来源流，并将其通过管道传输到目标流

```js
src.pipe(dest1).pipe(dest2)
```

流的API

```js
process.stdin 返回连接到 stdin 的流。
process.stdout 返回连接到 stdout 的流。
process.stderr 返回连接到 stderr 的流。
fs.createReadStream() 创建文件的可读流。
fs.createWriteStream() 创建到文件的可写流。
net.connect() 启动基于流的连接。
http.request() 返回 http.ClientRequest 类的实例，该实例是可写流。
zlib.createGzip() 使用 gzip（压缩算法）将数据压缩到流中。
zlib.createGunzip() 解压缩 gzip 流。
zlib.createDeflate() 使用 deflate（压缩算法）将数据压缩到流中。
zlib.createInflate() 解压缩 deflate 流
```

不同类型的流：

- `Readable`: 可以通过管道读取、但不能通过管道写入的流（可以接收数据，但不能向其发送数据）。 当推送数据到可读流中时，会对其进行缓冲，直到使用者开始读取数据为止。
- `Writable`: 可以通过管道写入、但不能通过管道读取的流（可以发送数据，但不能从中接收数据）。
- `Duplex`: 可以通过管道写入和读取的流，基本上相对于是可读流和可写流的组合。
- `Transform`: 类似于双工流、但其输出是其输入的转换的转换流。

创建可读流：

```js
const Stream = require('stream');
const readableStream = new Stream.Readable(); // 获取可读流
readableStream._read = ()=> {};//实现可读流的_read方法
/*
也可以使用read选项实现_read
const readableStream = new Stream.Readable({
  read(){}
})
*/

// 流已经初始化，可以发送数据
readableStream.push('hi!');
readableStream.push('ho!');
```

创建可写流：

```js
const Stream = require('stream');
const writableStream = new Stream.Writable(); // 获取可写流
// 实现可写流的write方法
/**
 * 可写流的_write方法
 * @param {Buffer} chunk 被写入的数据
 * @param {string} encoding 写入数据的编码
 * @param {Function} next 继续写入的回调函数
 */
writableStream._write = (chunk,encoding,next) => { 
  console.log(chunk.toString());//输出可写流的内容
  next();//执行下一步
}

//现在可以使用以下方式传输可写流
process.stdin.pipe(writableStream);
```

从可读流中获得数据：

```js
const Stream = require('stream');

// 创建可读流
const readableStream = new Stream.Readable({
  read(){}
});

// 创建可写流
const writableStream = new Stream.Writable();

// 实现可写流的_write方法
writableStream._write = (chunk, encoding, next) => {
  console.log(chunk.toString());
  next();//必须执行next方法
}

/*
也可以使用readable事件，直接消费可读流
readableStream.on('readable',()=>{
  console.log(readableStream.read())
})
*/

//将可读流通过管道输出到可写流上
readableStream.pipe(writableStream);

//向可读流推送数据
readableStream.push('hi!');
readableStream.push('ho!');

//使用信号通知已结束写入的可写流
writableStream.end();
```

发送数据到可写流：

```js
writableStream.write('hey!\n');
```
