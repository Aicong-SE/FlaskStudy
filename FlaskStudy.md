[TOC]

#### Hello World

```python
from flask import Flask  # 导入Flask,
app = Flask(__name__) # 创建此类的实例

@app.route('/') # 用于告诉Flask什么URL应该触发我们的函数
def hello_world():
    return "hello World" # 响应给网页
```
