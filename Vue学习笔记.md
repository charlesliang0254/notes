<h1>Vue学习笔记</h1>
[TOC]

## 创建一个最简单的vue应用：

- 引入Vue.js
- 创建Vue对象，el——CSS选择器，data——绑定的数据对象，使用“{{name}}”绑定数据

```html
<html>
    <head>
        <meta charset="utf-8">
        <title>Test Page</title>
        <script src="vue.js" type="text/javascript" charset="utf-8"></script>
    </head>
    <body>
        <div id="#app">
            {{a}}
        </div>
        <script>
        	let vm = new Vue({
                el:"#app",
                data:{
                    a:"hello"
                }
            });
        </script>
    </body>
</html>
```


## 数据与方法：

Vue对象的属性与方法都以$开头

改变数据：初始化、给$data添加数据

$watch：观测数据变化

```js
vm.$watch(function(newVal,oldVal){
    console.log(newVal,oldVal);
});
```

## 生命周期

常见的生命周期钩子：

- beforeCreate：实例初始化之后，数据观测（data observer）和event/watcher事件配置之前被调用
- created：在实践创建完成后被立即调用，在这一步，实例已完成以下的配置：数据观测（data observer），属性和方法的运算，watch/event事件回调。然而，挂载阶段还没开始，
- beforeMount：在挂载之前被调用，相关的渲染函数首次被调用
- mounted：被新创建的vm.$el所替换，挂载成功
- beforeUpdate：在更新操作之前调用
- updated：在更新操作完成之后被调用
- beforeDestroy：实例销毁之前，在这一步，实例依然完全可用
- destroyed：Vue实例销毁后调用，调用后，Vue实例所绑定的一切东西都会被解除绑定，所有的事件监听器都会被移除，所有的子实例都会被销毁。
- errorCaptured：当捕获一个来自子孙组件的错误时被调用。
- ...

> 在生命周期钩子中杜绝箭头函数的使用

## 模板语法

### 插值

1、文本：

```html
<span id="app">Message: {{message}}</span>
```

v-once：一次性插值，数据改变时，插值处的内容不会改变

```html
<span id="app" v-once>Message：{{message}}</span>
```

2、原始HTML：

在html元素上使用v-html插入原始HTML

```html
<div v-html="rawHtml"></div>
插值后的结果：
<div>
    <span style="font:10px 宋体,serif;color:black;"></span>
</div>
    
<script>
	let vm = new Vue({
        el:"#app",
        data:{
            message:"hello",
            rawHtml:'<span style="font:10px 宋体,serif;color:black;"></span>'
        }
    });
</script>
```

3、特性

给html元素动态绑定属性值，使用`v-bind:属性`

```html
<!DOCTYPE HTML>
<html lang="en" xmlns:v-bind="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <script src="./plugins/vue/vue.js" type="text/javascript" charset="UTF-8"></script>
    <style>
        .hello{
            width:100px;
            height:100px;
            border:2px black solid;
            background:red;
        }
    </style>
</head>
<body>
<div id="app">
    <div v-bind:class="cls"></div>
</div>
<script>
    let vm = new Vue({
        el: '#app',
        data: {
            cls:'hello'
        }
    });
</script>
</body>
</html>
```

4、使用javascript表达式

```html
<span>
	{{number+1}}
    {{cond?"yes":"no"}}
    {{msg.split(' ').reverse().join(' ')}}
</span>
```

### 指令

指令：带有v-前缀的特殊特性，指令特性的值一般是一个单个的javascript表达式（v-for例外）。指令的作用是根据表达式值的变化，将连带影响响应式地作用于DOM。

> v-if：如果为true时标签将被渲染出来，如果为false时标签将不被渲染出来。

- 参数：指令能够携带一个“参数”，在指令后用冒号表示

> v-on:事件，用于监听DOM事件

```html
<a v-on:click="do something">...</a>
```

- 修饰符：用点号指明的特殊后缀，表明指令应该以特殊方式绑定。

> .prevent修饰符告诉v-on指令对于触发的事件调用event.preventDefault()

```html
<form v-on:submit.prevent="onSubmit">
    ...
</form>
```

> 使用@click完成元素事件和函数的绑定，使用.stop修饰符可以阻止事件冒泡

```html
<div id="app">
    <div class="div1" @click="fun1">
        <div class="div2" @click.stop="fun2"></div>
    </div>
</div>
<script>
    let vm = new Vue({
        el: '#app',
        methods:{
            fun1(){
                alert("click the div 1");
            },
            fun2(){
                alert("click the div 2");
            }
        }
    });
</script>
```

## class与style绑定

一般写法：

```html
<div v-bind:class="hello"></div>
```

### 绑定HTML class

1、对象语法：可以传递一个对象，动态地切换对象

```html
<div 
     class="static" 
     v-bind:class="{active:isActive;'text-danger':hasError;}">
</div>

data:{
	isActive:true,
	'text-danger':false
}
```

2、数组语法：

```html
<div v-bind:class="[activeClass, errorClass]"></div>
data:{
	activeClass:'active',
	errorClass:'text-danger'
}
```

### 绑定内联样式

1、对象语法：

```html
<div v-bind:style="{color:mycolor,fontSize:myFontSize+'px'}">
    长风破浪会有时，直挂云帆济沧海。
</div>

data:{
	mycolor:'red',
	myFontSize:20
}
```

通常绑定到一个对象上比较好：

```html
<div v-bind:style="styleObject">
    长风破浪会有时，直挂云帆济沧海。
</div>

data:{
	styleObject:{
		color:'red',
		fontSize:'30px'
	}
}
```

2、数组语法

类似绑定class的操作

## 条件渲染

v-if、v-else-if、v-else指令

v-show：值为true时正常显示HTML元素，值为false时添加`display:none;`

二者区别：前者不渲染，后者渲染并添加隐藏的样式

## 列表渲染

`v-for`：类似于jstl中的c:forEach标签

`v-bind:key`：为了能够对dom元素进行排序，需要将数据的键绑定到dom元素上，使用`:key`

```html
<div id="app">
    <ul>
        <li v-for="item,index in items" :key="index">
            序号：{{index}}<br>
            姓名：{{item.name}}<br>
            密码：{{item.password}}<br>
        </li>
    </ul>
    <ul>
        <li v-for="value,key in obj" :key="key">
        	{{key}}:{{value}}
        </li>
    </ul>
</div>
data:{
    items:[
        {
            name:"孙悟空",
            password:"123"
        },
        {
            name:"猪八戒",
            password:"abc"
        },
        {
            name:"沙僧",
            password:"cmyk"
        }
    ],
	obj:{
		name:"张三",
		studentNo:2018903672,
		school:"信息学院",
		department:"软件工程",
		cls:2018240207
	}
}
```

## 事件绑定

v-on:click  用于绑定事件

可以向事件中传递自定义参数，也可以访问DOM原始事件

```html
<div id="app">
    <button v-on:click="onclick">button1</button>	
    <button v-on:click="show('abc',$event)">button2</button>
</div>
<script>
    new Vue({
        el:"#app",
        data:{
            message:"Good morning!"
        }
        methods:{
            onclick(){
                alert("hi");
        		alert(this.message);//可以在事件处理函数中访问data中定义的数据
            },
            show(msg,event){
                alert("Message:"+msg);
                alert("Event:"+event);
            }
        }
    });
</script>
```

事件修饰符：

- .stop：阻止事件冒泡
- .prevent：submit事件不再重载页面
- .capture：使用事件捕获模式，即父元素先响应事件，子元素再响应事件
- .self：事件不是内部触发的
- .once：click事件只会触发一次
- .passive：以passive: true;模式添加侦听器

## 表单输入绑定

v-model指令：在表单input、textarea、select元素上创建双向数据绑定

> v-model会忽略所有表单数据的value、checked、selected等初始值，以Vue的data域为准

v-model在内部为不同的元素抛出不同的事件

| 元素           | 属性    | 事件   |
| -------------- | ------- | ------ |
| text/textarea  | value   | input  |
| checkbox/radio | checked | change |
| select         | value   | change |

```html
<!DOCTYPE HTML>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <script src="./plugins/vue/vue.js" type="text/javascript" charset="UTF-8"></script>
</head>
<body>
<div id="app">
    <input type="text" name="username" placeholder="username" v-model="username">
    <p>username: {{username}}</p>
</div>
<script>
    let vm = new Vue({
        el: '#app',
        data:{
            username:""
        }
    });
</script>
</body>
</html>
```

## 组件基础

创建组件：

```js
Vue.component(name,{...})
```

props：给组件添加多个属性（数组），属性值将添加到data中

data：提供一个函数，返回一个对象，对象键值对将被添加到data中

template：组件的html模板

- slot标签，将组件标签包含的内容嵌入模板的位置

methods：组件的方法

this.$emit()：为组件自定义一个事件，第一个参数的事件名，其余参数为事件提供的实际参数。

```html
<!DOCTYPE HTML>
<html lang="en" >
<head>
    <meta charset="UTF-8">
    <script src="./plugins/vue/vue.js" type="text/javascript" charset="UTF-8"></script>
</head>
<body>
<div id="app">
    <hello title="123" @myclick="myclickfun" >
        <div style="color:cyan;font-size:32px;">勇气</div>
    </hello>
</div>
<script>
    Vue.component('hello',{
        props:['title'],
        template:'<div @click="clk" style="width:100px;height:100px;background:red;">title={{title}},count={{count}}<slot></slot></div>',
        data:function(){
            return {
                count:0
            }
        },
        methods:{
            clk(){
                this.count++;
                this.$emit('myclick',this.count);
            }
        }
    });
    new Vue({
        el:'#app',
        methods:{
            myclickfun(e){
                console.log(e);
            }
        }
    });
</script>
</body>
</html>
```

局部组件的创建：创建Vue对象时，传入一个components参数。

```js
new Vue({
    /* ... */
    components:{
        hello:{
            /* hello组件的定义 */
        }
    }
});
```

## 单文件组件

1、安装npm

2、安装cnpm

npm install -g cnpm --registry=https://registry.npm.taobao.org

3、安装vue-cli

cnpm install -g @vue/cli

4、安装webpack

cnpm install -g webpack

webpack是javascript的打包器



### 使用Vue ui创建一个项目

创建一个Vue项目：vue ui

运行Vue项目：

- npm run serve
- npm run build
- npm run lint

单文件组件基本格式：

```vue
<template></template>
<script></script>
<style></style>
```

导出组件：

```js
export default{
    name:"HelloWorld",
    props:{
        msg:{
            Type:String,
            default:" test msg"
        }
    },
    methods:{
        
    },
    data(){
        return {
            key:value
        }
    }
}
```

导入组件

```js
import HelloWorld from './components/HelloWorld.vue'
```

### 使用Vue cli命令行创建vue项目

创建项目：vue init webpack 项目名称

命令输入后，进入安装状态，需要用户输入一些信息



## Vue实战

### 前后端分离

将一个应用的前端代码后后端代码分开写

如果不使用前后端分离模式，会出现一些问题。

- 传统Java Web开发模式，前端使用JSP开发，JSP不是由后端开发者独立完成的
- 前端使用html + css +javascript，通过ajax与后端交换数据，前后端只要协调好接口文档就能分开独立开发，但是仍然是一个单独的应用
- 前后端分离模式下，前端与后端是两个应用，分别开发，分开部署。

实现前后端分离的技术：Spring Boot + Vue.js

### 创建一个Spring Boot+Vue的项目

1、使用vue ui创建一个vue项目，不使用git时关闭git，选择手动配置，选择router和vuex，取消校验器，打开历史模式，不保存模板

2、使用IDEA导入创建的Vue项目，在插件市场安装Vue插件，将javascript版本改为ES6

3、在IDEA的运行/调试配置中新建一个npm，选择命令为run，脚本为serve。然后点击运行按钮启动项目，点击停止按钮关闭项目。

4、创建一个Spring Boot项目，选择lombok、spring web、spring jpa、mysql-connector

applicationProperties配置文件：

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
    username: root
    password: ******
    driver-class-name: com.mysql.jdbc.Driver
  jpa:
    show-sql: true
    properties:
      hibernate:
        hbm2ddl:
          dialect: org.hibernate.dialect.MySQL5InnoDBDialect
        format_sql: true
server:
  port: 8181
```

跨域访问的解决方案：

- 使用@CrossOrigin注解
- 使用如下的全局跨域访问的配置

```java
@Bean
public WebMvcConfigurer webMvcConfigurer(){
    return new WebMvcConfigurer() {
        @Override
        public void addCorsMappings(CorsRegistry registry) {
            registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("GET","HEAD","POST","PUT","DELETE","OPTIONS")
                .allowCredentials(true)
                .maxAge(3600)
                .allowedHeaders("*");
        }
    };
}
```

- 使用Filter配置跨域访问

```java
@Bean
public Filter simpleCorsFilter(){
    return (request, response, chain) -> {
        HttpServletResponse resp = (HttpServletResponse) response;
        resp.setHeader("Access-Control-Allow-Origin","*");
        resp.setHeader("Access-Control-Allow-Methods","GET,POST,PUT,DELETE,OPTIONS,HEAD");
        resp.setHeader("Access-Control-Max-Age","3600");
        resp.setHeader("Access-Control-Allow-Headers",
                       "access-control-allow-origin, authority, content-type, version-info, X-Requested-With");
        chain.doFilter(request,resp);
    };
}
```

在dao层使用jpa：

- 实体类使用Entity、Table注解，Id字段使用Id注解
- 使用lombok注解Data
- 定义dao层接口并继承JpaRepository接口

### Element UI

Element UI的使用：

- 在Vue客户端中安装vue-cli-plugin-element插件
- 在element ui官网查看element ui的使用方法
- 将组件代码引入项目中进行修改

Vue+Element UI：

Vue集成Element UI

Element UI后台管理系统主要的标签：

- el-container：构建整个页面框架

- el-aside：构建左侧菜单

- el-menu：左侧菜单内容

  - :default-openeds  默认展开的菜单，通过菜单的index值关联

  - :default-active  默认选中的菜单，也是通过菜单的index值关联

    > 注意index值是字符串类型必须加单引号，多选使用[]数组

- el-submenu：可展开的菜单

  - index：菜单的索引值，文本类型，而不是数值类型

- template：对应el-submenu的菜单名

- i：设置菜单标签，通过class属性设置

  - el-icon-message、el-icon-menu、el-icon-setting

- el-menu-item：菜单的叶子节点，不可再展开

  - index

### Vue router来动态构建左侧菜单

在vue文件中使用router-view创建显示子模块的区域

在index.js文件中使用children属性添加子路由

渲染页面时，对于给定的路径，在路由树中找到对应的从根节点到叶子节点的路径，然后先渲染父组件，然后再将子组件渲染在router-view中

menu与router的绑定：

- 给el-menu标签添加router属性
- 在页面中添加router-view标签
- 给el-menu-item标签添加index，index值就是跳转位置
- 给父路由添加redirect属性，表示默认跳转位置

### 使用axios完成前后端的数据交互

```javascript
axios.get('http://localhost:8181/student/findAll/0/5')
	.then(req=>{
    	let content = req.data.content;
        for(let i=0;i<content.length;i++){
            content[i].gender = (content[i].gender===1?'男':'女');
        }
        this.tableData = content;
        this.totalElements = req.data.totalElements;
	})
	.catch(resp=>alert('error!!'));
axios.post('http://localhost:8181/student/add',this.form)
	.then(req=>alert(req.data))
	.catch(resp=>alert('error!!'));
```

- 注意：使用application/json协议，后台Spring Controller如果要使用一个且只有一个对象接收参数时，必须使用@RequestBody