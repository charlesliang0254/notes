# webpack学习笔记

## 概念

入口（entry）：webpack打包的入口，作为构建依赖图的开始

输出（output）：输出打包后的结果

loader：webpack只能理解JS和JSON文件，loader让webpack能够处理其他类型的文件。loader有两个属性，`test`属性和`use`属性，`test`属性用于识别哪些文件会被执行转换，`use`属性定义在进行转换时，应该使用哪个loader。

插件（plugins）：执行更加广泛的工作，包括打包优化、资源管理、注入环境变量

模式（mode）：通过选择`develop`、`production`、`none`之中的一个，来设置`mode`参数

浏览器兼容性（brower compatibility）：支持所有符合ES5标准的浏览器，如果想要支持旧版本的浏览器，需要提前加载polyfill

webpack5运行在nodejs v10.13.0+版本上

### 入口

写法：

- 单个入口简写
- multi-main简写
- 描述入口的对象

描述入口的对象属性：

- `dependOn`：当前入口所依赖的入口，必须在该入口被加载之前被加载

- `filename`：指定要输出的文件名称

- `import`：启用时需要加载的模块

- `library`：指定library选项，为当前entry构建一个library

- `runtime`：运行时chunk的名称，如果设置了就会创建一个新的运行时chunk

- `publicPath`：当该入口的输出文件在浏览器中被引用时，为它们指定一个公共的URL地址

【注意】

- runtime和dependOn不应在同一个入口上同时使用

- 确保runtime不会指向已存在的入口名称

- dependOn不能是循环引用的

常见的场景：分离APP（应用程序）和vender（第三方库）入口；多页面应用程序

### 输出

用法：

- 最低要求：设置一个对象，配置输出文件的文件名`filename`

- 多个入口起点的情况下，应该使用占位符`[]`确保每个文件具有唯一的名称 

### loader

用于对模块的源代码进行转换，loader可以使你在导入或加载模块时预处理文件。

应用场景：

- 将文件从Typescrip转换为Javascript
- 将内联图像转换为Data URL
- 甚至允许在Javascript模块中直接导入css文件

```js
module.exports = {
    module:{
        rules:[
            {test:/\.css$/,use:'css-loader'},
            {test:/\.ts$/,use:'ts-loader}
        ]
    }
}
```

使用：

- 配置方式（推荐）：在`webpack.config.js`中指定加载器，加载器从右向左（从下向上）取值执行

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // [style-loader](/loaders/style-loader)
          { loader: 'style-loader' },
          // [css-loader](/loaders/css-loader)
          {
            loader: 'css-loader',
            options: {
              modules: true
            }
          },
          // [sass-loader](/loaders/sass-loader)
          { loader: 'sass-loader' }
        ]
      }
    ]
  }
};
```

- 内联方式：在每个import语句中显示指定loader

```js
// 使用!（非!前缀）用于分割多个加载器
import Styles from 'style-loader!css-loader?modules!./styles.css';

// 使用!前缀，禁用所有配置的普通loader
import Styles from '!style-loader!css-loader?modules!./styles.css';

// 使用!!前缀，禁用所有已配置的loader，包括preLoader、loader、postLoader
import Styles from '!!style-loader!css-loader?modules!./styles.css';

// 使用-!前缀，禁用所有已配置的preLoader和loader，但是不禁用postLoaders
import Styles from '-!style-loader!css-loader?modules!./styles.css';

// 使用选项可以传递查询参数
import Styles from 'xxx-loader?key=value&&foo=bar';
import Styles from 'xxx-loader?{"key":"value","foo":"bar"}'
```

loader的特性：

- 支持链式调用
- 支持同步和异步
- 运行在node中，能够执行任何操作
- 推荐使用options对象配置，不建议使用query参数
- 插件扩展了loader的特性
- 支持产生额外的任意文件

### 插件

> 如果在插件中使用了 webpack-sources 的 package，请使用 require('webpack').sources 替代 require('webpack-sources')，以避免持久缓存的版本冲突。


插件：被webpack编译器调用的一个具有apply方法的Javascript对象

```js
// 插件名称
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';

// 插件类定义
class ConsoleLogOnBuildWebpackPlugin {
  // 常见具有apply方法，这个方法传入编译器对象作为参数
  apply(compiler) {
    compiler.hooks.run.tap(pluginName, //1. 第一个参数表示插件的名称
        //2. 第二个参数是一个回调函数
        (compilation) => {
        console.log('webpack 构建过程开始！');
        });
  }
}

module.exports = ConsoleLogOnBuildWebpackPlugin;
```

配置方式：

- 配置文件中配置`plugins`属性

- 使用Node API模式将`plugins`属性传入插件

## 配置

配置文件是JS文件，文件导出配置对象

遵循CommonJS模块规范

避免访问CLI参数（使用--env）、导出不确定结果、编写超长配置文件等操作

支持将单个配置导出为object，function或Promise，支持导出多个配置

支持其他配置语言，例如TS、CoffeeScript、Babel和Jsx

## 模块

webpack模块支持以各种方式表达模块之间的依赖关系：

- ES2015模块
- CommonJS模块
- AMD `define`和`require`语句
- css/sass/less文件中的`@import`语句
- css中的`url()`或者HTML中的`<img src=''>`文件中的图片链接

webpack中模块解析规则

- webpack使用`enhanced-resolve`，webpack能够解析三种模块路径（绝对路径、相对路径、模块路径）

  - 绝对路径：无需解析
  - 相对路径：拼接路径，得到绝对路径
  - 模块路径：在配置文件中`resolve.modules`属性指定的所有目录检索模块，可通过`resolve.alias`配置别名来替换不同初始模块路径。如果package中包含package.json文件，则根据`resolve.exportsModules`配置选项中指定的字段检索导出的模块

- 完成路径解析之后，判断路径指向文件还是目录：

  - 文件：文件具有扩展名，则直接将文件打包；否则，将使用`resolve.extensions`选项作为文件扩展名来解析
  - 目录：如果包含package.json文件，则根据`resolve.mainFields`来确定文件路径；否则，根据`resolve.mainFiles`选项中指定的文件名，在这个目录下匹配。使用`resolve.extensions`选项用类似的方法解析文件扩展名。


loader解析也遵循相同的规则，但是可以使用`resolveLoader`改变loader的解析规则

在watch模式下，只有被修改的文件才会被移除出缓存；如果关闭了watch模式，那么每次编译都会先把缓存清掉

模块的原理：

- 区分本地模块和远程模块：

  - 本地模块：属于当前构建一部分的模块
  - 远程模块：不属于当前构建，从所谓的运行时容器中加载的模块

- 加载远程模块被认为是异步操作；使用远程模块时，这些异步操作会被放置在位于远程模块和入口点之间的下一次chunk加载操作中；chunk操作通常是通过调用`import()`实现的

- 容器是通过容器的入口加载的，该入口暴露了对特定模块的异步访问。暴露的访问分为两个步骤：加载模块（异步的）和执行模块（同步的）

-  容器可以嵌套使用，可以使用来自其他容器的模块，容器之间也可以循环依赖

- 每个构建都充当一个容器，也可以将其他构建作为容器。每个容器都能够访问其他容器暴露出来的模块

- 共享模块：既可重写，又可以作为向嵌套容器提供重写的模块，通常指向每个构建中相同的模块

构建块：

- `ContainerPlugins`：使用指定的公开模块来创建一个额外的容器入口

- `ContainerReferencePlugin`：将特定的引用添加到作为外部资源的容器中，并允许从这些容器中导入远程模块。还会调用这些容器的`override`API来为它们提供重载。

- `ModuleFederationPlugin` 组合了 ContainerPlugin 和 ContainerReferencePlugin

## target

target配置打包的结果，可以编译成node环境下的包，也可以编译成web环境下的包。通过设置多个target配置可以实现多target