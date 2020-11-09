## 模板

**返回响应内容-模板**

- 模板其实是一个包含响应文本的文件，其中用占位符(变量)表示动态部分，告诉模板引擎其具体的值需要从使用的数据中获取

- 使用真实值替换变量，在返回最终得到的字符串，这个过程称为渲染

- flask是使用jinja2这个模板引擎来渲染模板

**使用模板的好处：**

- 视图函数只负责业务逻辑和数据处理(业务逻辑方面)

- 模板取到视图函数的数据结果进行展示(试图展示方面)

- 代码结构清晰，耦合度低



### jinja2

概念：

- jinja2：是python下一个被广泛应用的模板引擎，是由python实现的模板语言，它的设计思想来源于django的模板引擎，并扩展了其语法和一系列强大的功能，是flask内置的模板语言

- 模板语言：是一种被设计来自动生成文档的简单文本格式，在模板语言中，一般都会把一些变量传给模板，替换模板的特定位置上预先定义好的占位变量名

渲染模板函数：

- flask提供的render_template函数封装了该模板引擎

- render_template函数的第一个参数是模板的文件名，后面的参数都是键值对，表示模板中变量对应的真实值



### 使用

**注释：使用{# #}进行注释**

{# {{ name }} #}



**变量代码块{{}}**

{{}}来表示变量名，这种{{}}语法叫做变量代码块，这是一种特殊的占位符，告诉模板引擎这个位置的值从渲染模板时使用的数据中获取

{{ post.title }}

jinja2模板中的变量代码块可以是任意python类型或者对象，只要它能够被python的str()方法转换为一个字符串就可以，如：可以通过下面的方式显示一个字典或者列表中的某个元素

{{ your_dict['key'] }}

{{ your_list[0] }}



**控制代码块**

用{% %}定义的控制代码块，可以实现一些语言层次的功能，比如循环或者if语句

```jinja2
{% for index in my_list %}
    {% if index > 3 %}
        {{ index }} <br>
    {% endif %}
{% endfor %}
```



### **过滤器**

过滤器的本质就是函数，有时候我们不仅仅只是需要输出变量的值，我们还需要修改变量的显示，甚至格式化，运算等等，而在模板中是不能直接调用python中的某些方法，那么这就用到了过滤器

- 使用方式：变量名|过滤器

  ```jinja2
  {{ variable | filter_name(*args) }}
  ```

- 如果没有任何参数传给过滤器，则可以把括号省略掉

  ```jinja2
  {{ variable | filter_name }}
  ```

  

**链式调用**

在jinja2中，过滤器是可以支持链式调用的，如下：

```jinja2
   {{ "hello a d" | reverse | upper}}
```

**过滤器方法：**

- lower：把值转成小写

  ```jinja2
  {{ 'HELLO' | lower }}
  ```

  upper：把值转成大写

  ```jinja2
  {{ 'hello' | upper }}
  ```

  title：把值中的每个单词的首字母转成大写

  ```jinja2
  {{ 'hello' | title }}
  ```

  reverse：字符串反转

  ```jinja2
  {{ 'length' | reverse }}
  ```

  format：格式化输出

  ```jinja2
  {{ '%s is %d' | format('name',17) }}
  ```

  striptags：渲染之前把值中所有的html标签都删除

  ```jinja2
  {{ '<em>hello</em>' | striptags }}
  ```

  truncate：字符串截断

  ```jinja2
  {{'hello every one' | truncate(9)}}
  ```

  trim：把值得首尾空格删掉
  
  ```jinja2
  {{ ' hello every one ' | trim }}
  ```
  
  

**列表操作**

- first:取第一个元素

  ```jinja2
  {{ [1,2,3,4,5,6,7] | first }}
  ```

  last:取最后一个元素

  ```jinja2
  {{[1,2,3,4,5,6,7] | last}}
  ```

  length:获取列表长度

  ```jinja2
  {{[1,2,3,4,5,6] | lenght}}
  ```

  sum:列表求和

  ```jinja2
  {{ [1,2,3,4,5,6] | sum }}
  ```

  sort:列表排序

  ```jinja2
  {{ [1,2,3,4,5,6] | sort }}
  ```



**语句块过滤**

```jinja2
{% filter upper %}
	文字内容
{% endfilter %}
```





### Bootstrap模板

**Flask-Bootstrap定义了很多其他区块，都可在衍生模板中使用**

pip install flask-bootstrap

| 区块名       | 说明                     |
| ------------ | ------------------------ |
| doc          | 整个HTML文档             |
| html_attribs | <html>标签得属性         |
| html         | <html>标签中得内容       |
| head         | <head>标签中得内容       |
| title        | <title>标签中得内容      |
| metas        | 一组<meta>标签           |
| styles       | CSS声明                  |
| body_attribs | <body>标签得属性         |
| body         | <body>标签中得内容       |
| navbar       | 用户定义得导航栏         |
| content      | 用户定义得页面内容       |
| scripts      | 文档底部得javascript声明 |

表中很多区块都是Flask-Bootstrap自用的，如果直接覆盖可能会导致一些问题，如：bootstrap的css和javascript文件在styles和scripts区块中声明，如果应用需要向已经有内容的块中添加新内容，必须使用jinja2提供的super()函数，例如：如果要在衍生模板中添加新的javascript文件，那么需要重新定义script区块

```jinja2
{% block scripts %}
{{super()}}
<script type="text/javascript" src="my_javascript.js"></script>
{% endblock %}
```





### 链接

- 任何具有多个路由的应用都需要可以连接不同页面的链接，例如导航栏

- 在模板中直接编写简单路由的url链接不难，但对于包含可变部分的动态路由，在模板中构建正确的url就很困难了，而且，直接编写url会对代码中定义的路由产生不必要的依赖关系，如果重新定义路由，模板中的链接可能会失效，

- 为了避免以上问题，flask提供了url_for辅助函数，它使用应用的url映射中保存的信息生成url

- url_for()函数最简单的用法是以视图函数名(或者app.add_url_route()定义路由时使用的端点名)作为参数，返回对应的URL，例如，在当前版本的hello.py应用中调用url_for('index')得到的结果是‘/’，即根目录，

- 调用url_for('index',_external=True)返回的则是绝对地址，

**生成连接应用内不同路由的链接时，使用相对路径就足够了，如果要生成在浏览器之外使用的链接，则必须使用绝对路径，例如在电子邮件中发送的链接**

使用url_for()生成动态url时，将动态部分作为关键字参数传入，例如：url_for('user',name='john',_external=True)的·返回结果是http://localhost:5000/user/john

传给url_for()的关键字参数，不仅限于动态路由中的参数，非动态的参数也会添加到查询字符串中例如：url_for('user',name='john',page=2,version=1)的返回结果是http://localhost:5000/user/john?page2&version1





### 静态文件

默认设置下，flask在应用根目录中名为static的子目录中寻找静态文件





### 使用Flask-Moment本地化日期和时间

pip install flask-moment

初始化flask-moment

```python
from flask_moment import Moment
moment  Moment(app)
```

在基模块scripts中引用这个库，同时还保留基模块中定义的原始内容，，需要注意的是这个区块在flask-bootstrap的基模板中已经预定义，因此放在base.html的任何位置都行

```jinja2
{% block scripts %}
{{ super() }}
{{ moment.include_moment() }}
{% endblock %}
```

为了处理时间戳，flask-Moment向模板中开放了moment对象，

```python
@app.route('/')
def index():
    return render_template('index_demo.html',current_time=datetime.utcnow())
```

渲染模板变量

```html
    <p>the local date and time is {{ moment(current_time).format('LLL') }}</p>
    <p>that was {{ moment(current_time).fromNow(refresh=True) }}</p>
```

format('LLL')函数根据客户端计算机中的时区和区域设置渲染日期和时间，参数决定了渲染的方式，从L到LLL分别对应不同的复杂度，format()函数还可以接收很多自定义的格式说明符

fromNow()渲染相对时间戳，而且会随着时间的推移自动刷新显示的时间，这个时间戳最开始显示为“a few seconds ago” 但设定refresh=True参数后，其内容会随着时间的推移而更新，几分钟后文本变成为“a minute age”  "2 minutes age"等等



Flask-Moment实现了Moment.js的format(),fromNow(),fromTime(),calendar(),valueOf()和unix等方法 



Flask-Moment渲染的时间戳可实现多种语言的本地化，语言可在模板中选择，方法是在引入Moment.js之后，立即把两个字母的语言代码传给locale()函数，例如：配置Moment.js使用西班牙语的方式，

```jinja2
{% block scripts %}
{{ super() }}
{{ moment.include_moment() }}
{{ moment.locale('es') }}
{% endblock %}
```

