### 配置

flask_wtf无需在应用层初始化，但是他要求应用配置一个密钥，密钥是一个由随机字符串构成的唯一字符串，通过加密或签名以不同的方式提升应用的安全性，flask使用这个密钥保护用户会话，以防被篡改，每一个应用的密钥应都不一样

```python
app.config['SECRET_KEY'] = 'DFS'
```

app.config字典用于存储flask,扩展和应用自身的配置变量，使用标准的字典句法就能把配置添加到app.config对象中，这个对象还提供了一些方法，可以从文件或环境中导入配置

flask_wtf之所以要求应用配置一个密钥，是为了防止表单遭到跨站请求伪造(csrf)攻击，

为了增强安全性，密钥不应该直接写入源码，而应该保存在环境变量中

**web表单类**

- web表单是web应用程序的基本功能
- 它是html页面中负责数据采集的部件，表单由三个部分组成，表单标签，表单域，表单按钮，表单允许用户输入数据，负责HTML页面数据采集，通过表单将用户输入的数据提交给服务器
- 在flask中，为了处理web表单，我们一般使用flask-WTF扩展，它封装了WTForms，并且它有验证表单数据的功能

- flask请求对象包含客户端在请求中发送的全部信息，，对包含表单数据的POST请求来说用户填写的信息通过request.form访问

  pip install flask-wtf

```python
from flask_wtf import FlaskForm
from wtforms.validators import DataRequired,EqualTo
from wtforms import SelectField,StringField,PasswordField,SubmitField
class nameform(FlaskForm):
    name = StringField('what is your name?',validators=[DataRequired()])
    submit = SubmitField('Submit')
```

表单中的字段都定义为类变量，而各个类变量的值是相应字段类型的对象，StringField类表示属性为type='text'的html<input>元素,SubmitField类表示属性为type="submit"的html<input>元素，字段构造函数的第一个参数是把表单渲染成html时使用的标注(label)



StringField构造函数中的可选参数validators指定一个由验证函数组成的列表，在接受用户提交的数据之前验证数据，验证函数DataRequired()确保提交的字段内容不为空



**WTForms支持的HTML标准字段**



|      字段对象      |                  说明                   |
| :----------------: | :-------------------------------------: |
|    StringField     |                文本字段                 |
|   TextAreaField    |              多行文本字段               |
|   PasswordField    |              密码文本字段               |
|    HiddenField     |              隐藏文件字段               |
|     DateField      |   文本字段，值为datetime.date文本格式   |
|   DateTimeField    | 文本字段，值为datetime.datetime文本格式 |
|    IntegerField    |           文本字段，值为整数            |
|    DecimalField    |      文本字段，值为decimal.Decimal      |
|     FloatField     |          文本字段，值为浮点数           |
|    BooleanField    |         复选框，值为True和False         |
|     RadioField     |               一组单选框                |
|    SelectField     |                下拉列表                 |
| SelectMutipleField |         下拉列表，可选择多个值          |
|     FileField      |              文件上传字段               |
|    SubmitField     |              表单提交按钮               |
|     FormField      |      把表单作为字段嵌入另一个表单       |
|     FieldList      |           一组指定类型的字段            |



**WTForms验证函数**

|   验证函数    |                          说明                          |
| :-----------: | :----------------------------------------------------: |
| DataRequired  |               确保转换类型后字段中有数据               |
|     Email     |                    验证电子邮件地址                    |
|    EqualTo    | 比较两个字段的值，常用于要求输入两次密码进行确认的情况 |
| InputRequired |               确保转换类型前字段中有数据               |
|   IPAddress   |                    验证IPv4网络地址                    |
|    Length     |                  验证输入字符串的长度                  |
|  MacAddress   |                      验证MAC地址                       |
|  NumberRange  |               验证输入的值在数字范围之内               |
|   Optional    |         允许字段中没有输入，将跳过其他验证函数         |
|    Regexp     |                使用正则表达式验证输入值                |
|      URL      |                        验证URL                         |
|     UUID      |                        验证UUID                        |
|     AnyOf     |               确保输入值在一组可能的值中               |
|    NoneOf     |              确保输入值不在一组可能的值中              |



### 重定向和用户会话

重定向作为post请求的响应，而不是使用常规响应，重定向是一种特殊的响应，响应内容包含的是url，而不是html代码的字符串，浏览器收到这种响应，会向重定向的url发起get请求，显示页面的内容。这个页面的加载可能要多花几毫秒，因为要先把第二个请求发给服务器，除此之外，用户 不会察觉到有什么不同，现在，前一个请求是get请求，所以刷新命令能像预期的那样正常运行，这个技巧成为post/重定向/get模式

```python
@app.route('/',methods=['GET','POST'])
def index():
    name_form = nameform()
    if name_form.validate_on_submit():
        session['name'] = name_form.name.data
        print(session)
        return redirect(url_for('index'))
```

redirect()函数，这是flask提供的辅助函数，用于生成http重定向响应，redirect()函数的参数是重定向的url，

url_for()函数的第一个且唯一必须指定的参数是端点名，即路由的内部名称，默认情况下路由的端点是相应视图函数的名称，



### 闪现消息

请求完成后，需要让用户知道状态发生了变化，可以是确认消息，警告或是错误提示，典型例子是：用户提交有一项错误的登录表单后，服务器发回的响应重新渲染登陆表单，并在表单上面显示消息，提示用户名或密码无效，flask内置这个功能，提示用户名或密码无效

```python
@app.route('/',methods=['GET','POST'])
def index():
    name_form = nameform()
    if name_form.validate_on_submit():
        old_name = session.get('name')
        if old_name is not None and old_name != name_form.name.data:
            flash('looks like you have changed your name')
        session['name'] = name_form.name.data
        print(session)
        return redirect(url_for('index'))
    return render_template('index_demo.html',current_time=datetime.utcnow(),name_form=name_form,name=session.get('name'))
```



但是仅调用flash()函数并不能把消息显示出来，应用的模板必须渲染这些消息，最好在基模板中渲染闪现消息，因为这样所有页面都能显示需要显示的消息， flask把get_flashed_messages()函数开放给模板，用于获取并渲染闪现消息

```html
  {% for message in get_flashed_messages() %}
    <div>
    <button type="button" class="close" data-dismiss="alert">&times;</button>
        {{message}}
    </div>
    {% endfor %}
```

这里使用了循环，因为在之前的请求循环中每次调用flash()函数时都会生成一个消息，所以可能有多个消息在排队等待显示，get_flashed_messages()函数获取的消息在下次调用时不会再次返回，因此闪现消息只显示一次，然后就消失了

