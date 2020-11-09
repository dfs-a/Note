**flask程序运行过程**

1，当客户端想要获取资源时，一般会通过浏览器发起http请求

2，此时，web服务器会把来自客户端的所有请求都交给flask程序实例

3，程序实例使用werkzeug来做路由分发(URL请求和视图函数之间的对应关系)

4，根据每个URL请求，找到具体的视图函数并进行调用

- 在flask程序中，路由的实现一般是通过程序实例的装饰器实现

5，flask调用视图函数后，可以返回两种内容：

- 字符串内容：将视图函数的返回值作为响应的内容，返回给客户端(浏览器)

- HTML模板内容：获取到数据后，把数据传入到HTML模板文件中，模板引擎负责渲染http响应数据，然后返回响应数据给客户端(浏览器)



**什么是路由？路由函数有那些？**

- 客户端把请求发送给web服务器，web服务器在把请求发送给flask应用实例，应用实例需要知道对每个URL的请求要运行那些代码，所以保存了一个URL到python函数的映射关系，处理URL和函数之间的程序称为路由

- 在flask应用中定义路由的最简便方式，是应用实例提供的app.route装饰器

<img src="C:\Users\LEGION\Desktop\Learning\Flask\img\Snipaste_2020-11-07_14-48-08.png" style="zoom: 67%;" />

- 虽然使用app.route是首选方法，但不是唯一的方法，flask还支持一种更传统的方法，使用app.add_url_rule()方法，这个方法接收三个参数：url，端点名，视图函数

<img src="C:\Users\LEGION\Desktop\Learning\Flask\img\Snipaste_2020-11-07_14-57-09.png" style="zoom:67%;" />

- 这里index()这样处理入站请求的函数称为视图函数



**路由定义的基本方式：**

- 使用methods参数指定可接受的请求方式，可以是多种

  ```python
  @app.route('/',methods=['GET','POST'])
  def index():
      return render_template('index.html')
  ```



**路由传参：**

将同一类url映射到同一个视图函数处理

路由传递的参数默认当做string处理

指定参数类型，会调用系统的路由转换器进行匹配和转换

- 大致原理是将参数强转为int，如果成功，则可以进行路由匹配

- 如果参数无法转换成功，就无法匹配该路由

```python
#使用同一个视图函数，来显示不同用户的信息
#<>定义路由的参数，<>内需要起个变量接收数据
#对接收数据类型进行定义int，float
@app.route('/orders/<int:order_id>')
def get_order_id(order_id):
    #需要在视图函数的()内填入参数名，那么后面的代码才能去使用
    return 'order_id = %s' %order_id
```

Flask常用扩展包**：

​		Flask-SQLalchemy:操作数据库

​		Flask-migrate:管理迁移数据库

​		Flask-Mail:邮件

​		Flask-WTF:表单

​		Flask-Bable:提供国际化和本地化支持,翻译

​		Flask-script:插入脚本

​		Flask-Login:认证用户状态

​		Flask-OpenID:认证

​		Flask-RESTful:开发REST API的工具

​		Flask-Bootstrap:集成前端Twitter Bootstrap框架

​		Flask-Moment:本地化日期和时间

​		Flask-Admin:简单而可扩展的管理接口的框架



**Flask文档**：

中文文档：http://docs.jinkan.org/docs/flask/

英文文档：http://flask.pocoo.org/docs/0.12/





