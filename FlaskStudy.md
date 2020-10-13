[TOC]

#### Hello World

```python
from flask import Flask  # 导入Flask,
app = Flask(__name__) # 创建此类的实例

@app.route('/') # 用于告诉Flask什么URL应该触发我们的函数
def hello_world():
    return "hello World" # 响应给网页

if __name__ == '__main__':
    app.run(host='127.0.0.1',port=8888, debug=True) # 运行
```

#### 路由变量规则

> * <variable_name>: 函数会接收“variable_name”关键字传参
> * <converter: variable_name>: 使用转换器指定参数类型

```python
@qpp.route('user/<username>') # 关键字传参
def func(username)

@app.route('user/<string:username>') # 指定参数类型
```

指定类型：

| 类型   | 说明                           |
| ------ | ------------------------------ |
| string | （默认）接受不带斜杠的任何文本 |
| int    | 正整数                         |
| float  | 正浮点数                       |
| path   | 可以带斜杠的文本               |
| uuid   | UUID字符串                     |

#### url_for()

> 反向通常比对URL进行硬编码更具描述性。
>
> 您可以一口气更改URL，而不必记住手动更改硬编码的URL。
>
> URL构建透明地处理特殊字符和Unicode数据的转义。
>
> 生成的路径始终是绝对路径，避免了浏览器中相对路径的意外行为。

```python
@app.route('/')
def index():
    return 'index'

@app.route('/login')
def login():
    return 'login'

@app.route('/user/<username>')
def profile(username):
    return '{}\'s profile'.format(escape(username))

with app.test_request_context():
    print(url_for('index'))
    print(url_for('login'))
    print(url_for('login', next='/'))
    print(url_for('profile', username='John Doe'))
```

输出：

> /
> /login
> /login?next=/
> /user/John%20Doe

#### http方法

使用装饰器route()的method参数处理不同的方法

```python
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        pass
    else:
        pass
```

#### 静态文件

在包中或模块旁创建”static“文件夹

#### 渲染模板

使用render_template()方式渲染，flask会这template文件夹中查找模板，如果应用程序是一个模块，则该文件夹位于该模块旁边，如果它是一个包，则它实际上位于包中

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name) # 模板名，关键字传参
```

#### 请求对象

> from flask import request

使用`method`获取请求方式，使用`form`获取POST或PUT请求传输的数据，如果键不存在则`KeyError`,建议使用`args`

```python
request.method # 获取请求方式
request.form['键'] # 获取传输的数据
request.args.get('键','默认值') # 获取传输的数据，若没有则使用末尾
```



