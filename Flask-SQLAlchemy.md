### flask使用数据库

**flask-sqlalchemy扩展**

- 安装flaks-sqlalchemy：

pip install flask-sqlalchemy

- 如果连接mysql需要安装mysqldb：

pip install flask-mysqldb



**使用flask-sqlalchemy管理数据库**

flask的数据库配置

- app.config['SQLALCHEMY_DATABASE_URL']='mysql://root用户名:mysql密码@127.0.0.1:3306/test'

其他设置

```
#动态追踪修改设置，如未设置只会提示警告，不建议开启
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
#查询时会显示原始sql语句
app.config['SQLALCHEMY_ECHO'] = True
```

| 名字                      | 备注                                                         |
| ------------------------- | ------------------------------------------------------------ |
| SQLALCHEMY_DATABASE_URL   | 用于连接数据库                                               |
| SQLALCHEMY_BINDS          | 一个映射binds到连接url的字典                                 |
| SQLALCHEMY_ECHO           | 如果设置为TRUE，sqlalchemy会记录所有发给stderr的语句，这对调试有用 |
| SQLALCHEMY_RECORD_QUERIES | 可以用于显式地禁用或启动查询记录，查询记录，在调试或测试模式自动启动， |
| SQLALCHEMY_NATIVE_UNICODE | 可以用于显示禁用原生unicode支持，当使用不合适的指定无编码的数据库默认值时，这对于一些数据库适配器是必须的 |
| SQLALCHEMY_POOL_SIZE      | 数据库连接池的大小，默认是引擎默认值（默认5）                |
| SQLALCHEMY_POOL_TIMEOUT   | 设定连接池的连接超时时间，默认是10                           |
| SQLALCHEMY_POOL_RECYCLE   | 多少秒后自动回收连接，这对mysql为必要，它默认移除闲置多于8小时的连接，注意！如果使用了mysql，flask-SQLALchemy自动设置值为2小时 |

**常用的SQLAlchemy字段类型**

| 类型名       | python中类型      | 说明                                              |
| ------------ | ----------------- | ------------------------------------------------- |
| Integer      | int               | 普通整数，一般32位                                |
| Smalllnteger | int               | 取值范围小的整数，一般16位                        |
| Biglnteger   | int或long         | 不限制精度的整数                                  |
| Float        | float             | 浮点数                                            |
| Numeric      | decimal.Decimal   | 普通整数一般32位                                  |
| String       | str               | 变长字符串                                        |
| Text         | str               | 变长字符串，对较长或不限长度的字符串做了优化      |
| Unicode      | unicode           | 变长unicode字符串                                 |
| UnicodeText  | unicode           | 变长unicode字符串，对较长或不限长的字符串做了优化 |
| Boolean      | bool              | 布尔值                                            |
| Date         | datetime.date     | 时间                                              |
| Time         | datetime.datetime | 日期and时间                                       |
| LargeBinary  | str               | 二进制文件                                        |

**常用的SQLAlchemy列选项**

| 选项名      | 说明                                              |
| ----------- | ------------------------------------------------- |
| primary_key | 如果为true，代表表的主键                          |
| unique      | 如果为true，代表这列不允许出现重复的值            |
| index       | 如果为True，为这列创建索引，提高查询效率          |
| nullable    | 如果为true，允许有空值，如果为false，不允许有空值 |
| default     | 为这列定义默认值                                  |

常用的SQLAlchemy关系选项

| 选项名         | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| backref        | 在关系的另一模型中添加反向引用                               |
| primary join   | 明确指定两个模型之间使用的联结条件                           |
| uselist        | 如果为false，不使用列表，而是用标量值                        |
| order_by       | 指定关系中记录的排序方式                                     |
| secondary      | 指定多对多中记录的排序方式                                   |
| secondary join | 在SQLAlchemy中无法自行决定时，指定多对多关系中的二级联结条件 |

