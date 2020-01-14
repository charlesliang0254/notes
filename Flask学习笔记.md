[TOC]

## Flask环境搭建

- Flask的安装与配置：略

- python虚拟环境的安装与配置

- requirements.txt文件：类似于pom.xml，主要用于记录依赖项及其版本号

  `pip freeze >requirements.txt`：生成依赖项文件requirements.txt

  `pip install -r requirements.txt`：根据依赖项文件requirements.txt下载对应的依赖项

## 创建一个Flask项目

```python
# -*- coding:utf-8 -*-
from flask import Flask, render_template # 导入Flask扩展

app = Flask(__name__)  # 创建Flask应用实例，传入__name__是为了说明资源所在的路径


# 定义路由及视图函数，通过装饰器实现
@app.route('/helloworld')
def hello_world():
    return 'Hello World!'  # 可以返回两种内容，字符串和html模板


@app.route('/index')
def index():
    return render_template('index.html')


# 启动程序
if __name__ == '__main__':
    app.run()  # 执行程序实例，运行一个简易的服务器
```

- 修改Flask项目部署的url和端口号：PC左上角Edit Configurations，添加Addition Options如下：

  `--host=localhost --port=8080`

## 路由定义的基本方式

请求方式的限定：

```python
@app.route('/',methods=['GET','POST'])  # 默认只支持GET
```

路由传参示例：

```python
@app.route('/order/<order_id>')
def get_order_id(order_id):
    print(order_id)  # 参数类型默认为字符串
    return 'order_id = %s' % order_id

# 限定order_id的类型为int
@app.route('/order/<int:order_id>')
def get_order_id(order_id):
    print(order_id)
    return 'order_id = %d' % order_id
```

## Jinja2模板引擎

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Home</title>
</head>
<body>
    <h1>{{ info }}</h1>
    <h2>{{ info }}</h2>
    <h3>{{ info }}</h3>
</body>
</html>
```

```python
@app.route('/')
def index():
    return render_template('index.html',info='Hello World')
```

jinja2语法：

- 注释：`{#下面是一个变量代码块的使用#}`

- 变量代码块：

  ```html
  {# 变量 #}
  {{ info }}
  
  {# 列表的使用 #}
  {{ my_list }}
  {{ my_list.1 }}
  {{ my_list[1] }}
  
  {# 字典的使用 #}
  {{ my_dict }}
  {{ my_dict.username }}
  {{ my_dict['username'] }}
  ```

- 控制代码块：

  ```html
  {% for item in my_list %}
      {{ item }} <br/>
  {% endfor %}
  
  {% for item in my_list %}
      {% if item > 3 %}
          {{ item }} <br/>
      {% endif %}
  {% endfor %}
  ```

- 过滤器：

  ```html
  {{ url_str | upper }}
  {{ url_str | reverse }}
  ```

  常见的过滤器：
  
  - safe：渲染时不转义
  - capitalize：首字母大写，其他字母小写
  - lower：小写
  - upper：大写
  - title：每个单词的首字母大写
  - trim：去掉首尾空格
  - striptags：渲染之前把值中所有的HTML标签都删掉
  - join：拼接多个值为字符串
  - round：四舍五入
  - int/float/string：把值转换为整型、浮点型、字符串
  - abs：返回一个数的绝对值
  - default：如果当前变量的值为空，则展示默认值
  - first：返回集合的第一个值
  - last：返回集合的最后一个值
  - format：格式化字符串
  - length：返回集合的长度
  - replace：替换字符串
  - wordcount：计算字符串中单词的个数

