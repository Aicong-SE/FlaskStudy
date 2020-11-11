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

使用render_template()方式渲染，flask会这templates文件夹中查找模板，如果应用程序是一个模块，则该文件夹位于该模块旁边，如果它是一个包，则它实际上位于包中

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

#### 文件上传

HTML表单上设置enctype="nultipart/form-data"，通过`files`来访问这些文件，每一个上传的文件都会存入字典中，通过方法save()可以将文件存储在服务器文件系统上。

```python
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
```

#### cookie

读取cookie

```python
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
```

存储cookie

```python
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

#### 重定向和错误

使用`redirect()`将请求重定向到另一个路由；使用`abort()`提前中止请求

```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

使用`errorhandler()`错误处理器自定义错误页

```python
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

#### 关于响应

* 如果返回正确类型的响应对象，则直接从视图返回该响应对象
* 如果是字符串，则使用该数据和默认参数创建响应对象。
* 如果是命令，则使用`jsonify`.
* 如果返回元组，元组中的项可以提供额外的信息。此类元组必须以形式（响应`(response, status)``(response, headers)`或`(response, status, headers)`。`status`值将覆盖状态代码`headers`其他标头值的列表或字典。
* 如果这些都无效，Flask 将假定返回值是有效的 WSGI 应用程序，并将其转换为响应对象。

使用`mark_response()`在视图中获取生成的响应对象

```python 
@app.errorhandler(404)
def not_found(error):
    return render_template('error.html'), 404

您只需用make_response()表达式，然后获取响应对象来修改它，然后返回它：

@app.errorhandler(404)
def not_found(error):
    resp = make_response(render_template('error.html'), 404)
    resp.headers['X-Something'] = 'A value'
    return resp
```

#### API与JSON

如果响应为dict类型，flask将自动转为json响应,若想将dict以外的类型转为json，需要使用jsonify()

```python 
@app.route("/users")
def users_api():
    users = get_all_users()
    return jsonify([user.to_json() for user in users])
```

#### sessiion

```python
from flask import Flask, session, redirect, url_for, request
from markupsafe import escape

app = Flask(__name__)

# Set the secret key to some random bytes. Keep this really secret!
app.secret_key = b'_5#y2L"F4Q8z\n\xec]/'

@app.route('/')
def index():
    if 'username' in session:
        return 'Logged in as %s' % escape(session['username'])
    return 'You are not logged in'

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    return '''
        <form method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''

@app.route('/logout')
def logout():
    # remove the username from the session if it's there
    session.pop('username', None)
    return redirect(url_for('index'))
```

#### logging

```python 
app.logger.debug('A value for debugging')
app.logger.warning('A warning occurred (%d apples)', 42)
app.logger.error('An error occurred')
```

#### 返回文件

```python
from flask import Flask, send_file
app = Flask(__name__)

app.route('/login')
def login():
    return send_file("templates/login.html")
```

