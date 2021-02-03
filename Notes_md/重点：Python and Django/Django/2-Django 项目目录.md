# Django 

##  Django简介

- Python下的Web应用框架
- Python写的

| **Django** **版本** | **Python** **版本**      |
| ------------------- | ------------------------ |
| 1.8                 | 2.7, 3.2 , 3.3, 3.4, 3.5 |
| 1.9, 1.10           | 2.7, 3.4, 3.5            |
| 1.11                | 2.7, 3.4, 3.5, 3.6       |
| 2.0                 | 3.4, 3.5, 3.6, 3.7       |
| 2.1, 2.2            | 3.5, 3.6, 3.7            |

- 基于MVC模型

\## Django使用MTV模型

- - Model
  - Template
  - View

\## 下载安装查看安装的Django的版本

- 下载安装

- - https://www.djangoproject.com/download/

- 查看版本

- - python3
  - import django
  - django.get_version()

\## 使用

- 创建项目

- - django-admin.py startproject HelloWorld

- 启动项目

- - 进入项目目录

  - python3 manage.py runserver 0.0.0.0:8000

  - 报错“You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.Run 'python manage.py migrate' to apply them.” 

  - - python3 manage.py migrate

* 模版层（html）—路由层（urls.py）—视图层（views）—模型层（models）

## 1、基本文件目录及简要说明

```txt
-项目名
	-__init__.py
	-settings.py
	-urls.py
	-wsgi.py
```

* __ init__.py

  指定系统使用的数据库连接时pymysql

  ```python
  import pymysql
  pymysql.install_as_MySQLdb()
  ```

* urls.py

  在urlpatterns中指定访问路径

  ```python
  urlpatterns = [
      url('lizixing/', views.lizixing),
      url('test/', views.test),
  ]
  ```

* settings.py

  指定模版(html文件)路径templates

  ``` python
  TEMPLATES = [
      {
          'BACKEND': 'django.template.backends.django.DjangoTemplates',
          'DIRS': [os.path.join(BASE_DIR, 'templates')],
          'APP_DIRS': True,
          'OPTIONS': {
              'context_processors': [
                  'django.template.context_processors.debug',
                  'django.template.context_processors.request',
                  'django.contrib.auth.context_processors.auth',
                  'django.contrib.messages.context_processors.messages',
              ],
          },
      },
  ]
  ```

  指定数据库连接

  ```python
  DATABASES = {
      'default': {
          'ENGINE': 'django.db.backends.sqlite3',
          'NAME': 'op_console_test', # 数据库名称
          'HOST': 'op-console-test-mysql-test-writer', # 数据库地址，本机 ip 地址 127.0.0.1
          'PORT': 3306, # 端口
          'USER': 'consoletest',  # 数据库用户名
          'PASSWORD': 'czW605E_', # 数据库密码
      }
  }
  ```

  指定模型'TestModel''LizixingModel'

  ```python
  INSTALLED_APPS = [
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      'TestModel',
      'LizixingModel'
  ]
  ```

## 2、模板

### 2.1 基本配置

* 创建模版html

  ```html
  <body>
    <h1>
        {{ hello }}
        {{ user.name}}
        {{ user.age }}
    </h1>
  </body>
  ```

* 在settings.py中指定模版的位置

  ```python
  TEMPLATES = [
      {
          'BACKEND': 'django.template.backends.django.DjangoTemplates',
          'DIRS': [os.path.join(BASE_DIR, 'templates')],
          'APP_DIRS': True,
          'OPTIONS': {
              'context_processors': [
                  'django.template.context_processors.debug',
                  'django.template.context_processors.request',
                  'django.contrib.auth.context_processors.auth',
                  'django.contrib.messages.context_processors.messages',
              ],
          },
      },
  ]
  ```

* 在urls.py指定url到py文件方法的路由 views是views.py文件，lizixing是views中的方法

  ```python
  urlpatterns = [
      url('lizixing/', views.lizixing),
  ]
  ```

  * views.py文件

    ```python
    from django.shortcuts import render
    def lizixing(request):
        context = {}
        context['hello'] = 'Hello World!'
        context['user'] = {"name":"lizixing","age":19}
        return render(request, 'lizixing.html', context)
    ```
    
  * 模版html中{{}}中会根据render中传入的字典key显示value，实际html
  
  ``` html
  <body>
    <h1>
        Hello World!
        lizixing
        19
    </h1>
  </body>
  ```

#### 2.2 模版与render传参的对应

* 模版中使用{{ paramName }} 表示需要使用传参字典中的paramName对应的value替换

​	如 {{user}} ==>render(request,'xxx.html',{'user':'李籽兴'})

* **变量：**
  
  * 传参：{'user':'lizixing'}
  * 模版：{{user}}  ==>lizixing
  
* **列表**

  * 传参：{'user':['A','B','C']}

  * 模版：{{user}}  ==>['A','B','C']

    ​           {{user.0}}  ==>A

    ​           {{user.1}}  ==>B			

    ​		   {{user.2}}  ==>C

* 字典

  * 传参：{'user':{ 'usera':'A', 'userb':'B', 'userc':'C' }}

  * 模版：{{user}}  ==>{ 'usera':'A', 'userb':'B', 'userc':'C' }

    ​           {{user.usera}}  ==>A

    ​           {{user.userb}}  ==>B			

    ​		   {{user.userc}}  ==>C

### 2.3 过滤器

* 模板语法：

```html
{{ 变量名 | 过滤器：可选参数｜过滤器：可选参数 }}
{{ name|first|lower| }} <!-- html模版中取name，取第一个字符并将其转换成小写 -->
```

* 常用过滤器
  * lower 
  * first
  * truncatewords:"30"
  * truncatechars:"3"
  * addslashes
  * date:"Y-m-d"
  * default
  * length
  * Filesizeformat
  * Safe

### 2.4 if/else标签

* 语法

``` html
{% if 表达式 %}
		xxx
{% elif 表达式 %}
		xxx
{% else %}
		xxx
{% endif %}
```

* 表达式支持 not、or、and

### 2.4 for标签

* 语法

```html
<!-- 普通循环 -->
{% for i in views_list %}
	{{ i }}
{% endfor %}

<!-- 反向循环 -->
{% for i in views_list reversed %}
	{{ i }}
{% endfor %}

<!-- 循环中的序号判断 -->
{% for i in listvar %}
    {{ forloop.counter }}   <!-- 当前循环的序号，从1开始计数 -->
    {{ forloop.counter0 }}   <!-- 当前循环的序号，从0开始计数 -->
    {{ forloop.revcounter }}   <!-- 当前循环的序号，倒数，最后元素序号为1 -->
    {{ forloop.revcounter0 }}   <!-- 当前循环的序号，倒数，最后元素序号为0 -->
    {{ forloop.first }}   <!-- 当前元素是否为第一个元素 -->
    {{ forloop.last }}   <!-- 当前元素是否为最后一个元素 -->
{% endfor %}

<!-- 循环为空时进入分支 -->
{% for i in views_list reversed %}
	{{ i }}
{% empty %}
	循环为空
{% endfor %}
```

### 2.5 ifequal/ifnotequal标签

* 语法

```html
{% ifequal section 'sitenews' %}
    <h1>Site News</h1>
{% else %}
    <h1>No News Here</h1>
{% endifequal %}
```

### 2.6 include标签

* 语法

```html
{% include "nav.html" %}
```

### 2.7 自定义标签

### 2.8 配置静态文件

* 创建statics文件夹

* 配置settings文件

  ```python
  STATIC_URL = '/static/' # 别名 
  STATICFILES_DIRS = [ 
      os.path.join(BASE_DIR, "statics"), 
  ]
  ```

* 创建css、js、images、plugins存放css、js、图片、插件

### 2.9 模版继承

* 父模版预留区域

```html
{% block 名称 %} 
预留给子模板的区域，可以设置设置默认内容
{% endblock 名称 %}
```

* 子模版

  * 继承父模版

    ```html
    {% extends "父模板路径"%} 
    ```

  * 子模版重写父模版预留区域

    ```html
    { % block 名称 % }
    内容 
    {% endblock 名称 %}
    ```

    

## 3、模型

用于连接数据库操作数据库表数据

### 3.1 Django ORM

* ORM(Object Relational Mapping) 
  * 用于实现面向对象编程语言里对于不同类型系统的数据之间的转换
  * 用于在业务逻辑层和数据库层之间充当桥梁
* ORM解析过程
  * Python代码转换成sql
  * sql通过pymysql传给数据库服务器
  * 数据库中执行sql
* py代码和表的对应关系
  * 类 —— 数据库表
  * 对象——数据库行数据
  * 类属性——数据库表字段

### 3.2 数据库基本配置

* settings.py

  * DATABASES项

    ```python
    DATABASES = { 
        'default': 
        { 
            'ENGINE': 'django.db.backends.mysql',    # 数据库引擎
            'NAME': 'runoob', # 数据库名称
            'HOST': '127.0.0.1', # 数据库地址，本机 ip 地址 127.0.0.1 
            'PORT': 3306, # 端口 
            'USER': 'root',  # 数据库用户名
            'PASSWORD': '123456', # 数据库密码
        }  
    }
    ```

* __ init__.py

  ```python
  # 在与 settings.py 同级目录下的 __init__.py 中引入模块和进行配置
  import pymysql
  pymysql.install_as_MySQLdb()
  ```

### 3.3 模型基本配置

* 创建APP文件夹

  ```
  django-admin.py startapp 模型名称
  ```

  * 生成APP文件夹

  ```test
  |-- 模型名称
  |   |-- __init__.py
  |   |-- admin.py
  |   |-- models.py
  |   |-- tests.py
  |   `-- views.py
  ```

  * 在settings.py 【INSTALLED_APPS】中指定APP模型路径

  ```python
  INSTALLED_APPS = (
      'django.contrib.admin',
      'django.contrib.auth',
      'django.contrib.contenttypes',
      'django.contrib.sessions',
      'django.contrib.messages',
      'django.contrib.staticfiles',
      '模型名称',               # 添加此项
  )
  ```

* 创建表对应模型

  * 在model.py中创建表对应的model类

  ```python
  # models.py
  from django.db import models
  
  # 表名对应类名
  class Test(models.Model):
    	# 字段名对应属性，类型有models.xxxFiled指定
    	name = models.CharFiled(max_length=20)
  ```

* 数据库操作

  * 增 save

  ```python
  test = Test()
  test.name = 李籽兴
  test.save()
  ```

  * 删

  ```python
  # 删除id=1的数据
  test1 = Test.objects.get(id=1)
  test1.delete()
  
  # 另外一种方式
  # Test.objects.filter(id=1).delete()
  
  # 删除所有数据
  # Test.objects.all().delete()
  ```

  * 改

  ```python
  # 修改其中一个id=1的name字段，再save，相当于SQL中的UPDATE
  test1 = Test.objects.get(id=1)
  test1.name = 'Google'
  test1.save()
  
  # 另外一种方式
  Test.objects.filter(id=1).update(name='Google')
  
  # 修改所有的列
  Test.objects.all().update(name='Google')
  ```

  * 查

  ```python
  # 查询所有
  testList = Test.objects.all() 
  
  # 条件查询 filter相当于where
  test = Test.objects.filter(id = 1)
  
  # 获取单条数据
  test = Test.objects.get(id = 1)
  
  # 范围查询 order_by相当于OFFSET 0 LIMIT 2
  test = Test.objects.order_by('name')[0:2]
  
  # 连锁使用
  test = Test.objects.filter(name="runoob").order_by("id")
  ```

## 4、表单



### 4.1 HTTP请求

请求html

```html
<body>
  <form action="xxx" method="get">
    <input type='text' name='test'/>
  	<input type='submit' value='点一下'/> 	
  </form>
</body>
```

```html
<body>
  <form action="xxx" method="post">
    <input type='text' name='test'/>
  	<input type='submit' value='点一下'/> 	
  </form>
</body>
```

#### Get请求

* 获取requset中的get请求参数

```python
def myMethon(request):
  	# 获取request中的get请求的参数字典
    dict = request.GET
    # 获取request中get请求的具体参数值
    value = request.GET['name']
```

#### Post请求

* 获取requset中的post请求参数

```python
def myMethon(request):
  	# 获取request中的post请求的参数字典
    dict = request.POST
    # 获取request中post请求的具体参数值
    value = request.POST['name']
```

### 4.2 Requset请求

* HttpRequest对象

  常用方法：

  request['key']，相当于先requset.POST['key']再request.GET['key']

  request.has_key('key')，相当于key in request.POST or key in request.GET

  request.get_full_path()，返回包含查询字符串的请求路径

  request.is_secure()，如果发送的是HTTPS请求返回true，HTTP请求返回false

* QueryDict对象

  * POST和GET属性是QueryDict类的实例
  * QueryDict的一些与Dict不同的方法

  | **方法**    | **描述**                                                     |
  | :---------- | :----------------------------------------------------------- |
  | __getitem__ | 和标准字典的处理有一点不同，就是，如果Key对应多个Value，__getitem__()返回最后一个value。 |
  | __setitem__ | 设置参数指定key的value列表(一个Python list)。注意：它只能在一个mutable QueryDict 对象上被调用(就是通过copy()产生的一个QueryDict对象的拷贝). |
  | get()       | 如果key对应多个value，get()返回最后一个value。               |
  | update()    | 参数可以是QueryDict，也可以是标准字典。和标准字典的update方法不同，该方法添加字典 items，而不是替换它们:`>>> q = QueryDict('a=1') >>> q = q.copy() # to make it mutable >>> q.update({'a': '2'}) >>> q.getlist('a')  ['1', '2'] >>> q['a'] # returns the last ['2']` |
  | items()     | 和标准字典的items()方法有一点不同,该方法使用单值逻辑的__getitem__():`>>> q = QueryDict('a=1&a=2&a=3') >>> q.items() [('a', '3')]` |
  | values()    | 和标准字典的values()方法有一点不同,该方法使用单值逻辑的__getitem__(): |

  | **方法**                 | **描述**                                                     |
  | :----------------------- | :----------------------------------------------------------- |
  | copy()                   | 返回对象的拷贝，内部实现是用Python标准库的copy.deepcopy()。该拷贝是mutable(可更改的) — 就是说，可以更改该拷贝的值。 |
  | getlist(key)             | 返回和参数key对应的所有值，作为一个Python list返回。如果key不存在，则返回空list。 It's guaranteed to return a list of some sort.. |
  | setlist(key,list_)       | 设置key的值为list_ (unlike __setitem__()).                   |
  | appendlist(key,item)     | 添加item到和key关联的内部list.                               |
  | setlistdefault(key,list) | 和setdefault有一点不同，它接受list而不是单个value作为参数。  |
  | lists()                  | 和items()有一点不同, 它会返回key的所有值，作为一个list, 例如:`>>> q = QueryDict('a=1&a=2&a=3') >>> q.lists() [('a', ['1', '2', '3'])] ` |
  | urlencode()              | 返回一个以查询字符串格式进行格式化后的字符串(例如："a=2&b=3&b=5")。 |

## 5、视图

### 5.1 视图函数

	* 视图函数一般指urls能链接到python文件的方法
	* 每一个视图函数都应该返回一个HttpResponse对象

### 5.2 请求对象HttpRequset

#### GET属性

* QueryDict对象，包含get请求的所有参数
* 获取方法：requst.GET
* 获取内容方法：request.GET.get("key") 或request.GET['key']

#### POST属性

* QueryDict对象，包含post请求的所有参数
* 获取方法：requst.POST
* 获取内容方法：request.POST.get("key") 或request.POST['key']

#### body属性

* 数据类型是二进制字节流，是原生请求体中的内容，http中用POST（Get没有请求体）
* Http中不常用，在处理非htto形式的报文时非常有用
* 获取方法：requset.body

#### path属性

* 获取 URL 中的路径部分，数据类型是字符串。
* 获取方法：requst.path

#### methon属性

* 获取当前请求的方式，数据类型是字符串，且结果为大写。
* 获取方法：request.method

### 5.3 响应对象HttpResponse

* 响应对象主要有三种形式
  * HttpResponse()
  * render()
  * redirect()

#### HttpResponse

* 返回文本，参数为字符串，参数中可写html脚本

* 返回方法：

  ```python
  **return** HttpResponse("<a href='https://www.runoob.com/'>菜鸟教程</a> ")
  ```

#### render

* 返回文本

  * 第一个参数为request，
  * 第二个参数为页面名称字符串
  * 第三个参数为字典

* 返回方法：

  ```python
  context={'name':'lizixing'}
  return render(request, 'myHtml.html', context)
  ```

* 底层返回的也是 HttpResponse 对象

#### redirect

* 重定向，跳转新页面

  * 参数为字符串，是跳转页面的链接

* 返回方法：

  ```python
  return redirect("/index/")
  ```

* 底层返回的也是 HttpResponse 对象

## 6、路由

* 路由就是将url与Django的视图类进行链接的映射关系

* 不同版本的配置方式不太一样

### 6.1 1.1.x版本

* **url() 方法**：普通路径和正则路径均可使用，需要自己手动添加正则首位限制符号。

```python
from django.conf.urls import url # 用 url 需要引入

urlpatterns = [
    url(r'^admin/$', admin.site.urls),
    url(r'^index/$', views.index), # 普通路径
    url(r'^articles/([0-9]{4})/$', views.articles), # 正则路径
]
```

### 6.2 2.2.x版本以后

- **path()**：用于普通路径，不需要自己手动添加正则首位限制符号，底层已经添加。
- **re_path()**：用于正则路径，需要自己手动添加正则首位限制符号。

```python
from django.urls import re_path # 用re_path 需要引入
urlpatterns = [
    path('admin/', admin.site.urls),
    path('index/', views.index), # 普通路径
    re_path(r'^articles/([0-9]{4})/$', views.articles), # 正则路径
]
```

### 6.3 URL请求的类型，以及Django的url路由配置方式

1. 无参数

   链接：http://127.0.0.1:8000/hello

   路由：re_path(r'^hello/$', views.hello)

   视图：hello( request )

2. 带一个参数

   链接：http://127.0.0.1:8000/plist/china

   路由：re_path(r'^plist/(.+)/$', views.hello)

   视图：hello( request ,param1)

3. 带多个参数

   链接：http://127.0.0.1:8000/plist/p1chinap22012/（p1和p2作为分割，得到china和2012两个参数）

   路由：re_path(r'^plist/p1(**\w**+)p2(.+)/$', helloParams)

   视图：hello( request ,param1, param2)

4. 传统？传递参数

   链接：http://127.0.0.1:8000/plist/?p1=china&p2=2012

   路由：re_path(r'^plist/$', hello)

   视图：hello( request ): request.GET.get(p1)

### 6.4 正则路径中的分组

#### 无名分组

* url参数分组与视图方法的参数个数相同

  链接：http://127.0.0.1:8000/index/1995/10

  路由：re_path("^index/([0-9]{4})/([0-9]{2})/$, views.hello)

  视图：hello( request ,year,month)

#### 有名分组

* 语法：(?P<组名>正则表达式)

  链接：http://127.0.0.1:8000/index/P11995/P210

  路由：re_path("^index/(?P[0-9]{4})/(?P[0-9]{2})/$", views.index),

  视图：hello( request ,year,month) 相当于传参1995 10

### 6.5 路由转发

​	**存在问题**：Django 项目里多个app目录共用一个 urls 容易造成混淆，后期维护也不方便。

​	**解决**：使用路由分发（include），让每个app目录都单独拥有自己的 urls。

* 语法：

  path("路由路径/",include("其他APP名.urls"))

* 在各自的url中路由自己的视图

### 6.6 反向解析

* 如果路由层（urls.py）中的路由发生了改变，模版层和视图层都需要改变，比较麻烦

  ``` python
  # 路由层
  path('login/', views.login)
  
  # 视图层,跳转到login页面
  def index(request):
    return redirect('/login/')
  
  # 模版层
  <a href='login/' />
  ```

  如果路由层login改变了名字，则视图层和模版层都要改

#### 普通路径

* 解决方案：在路由层给路由起别名

  语法：

  ```python
  # 路由层， 起别名 name=‘路由别名’
  path('login/', views.login, name='login')
  
  # 视图层,跳转到login页面， 反向解析 reverse('路由别名')
  def index(request):
    return redirect(reverse('login'))
  
  # 模版层, 反向解析 "{% url '路由别名' %}"
  <a href="{% url 'login' %}" />
  ```

#### 正则路径（无名分组）

* 解决方案：在路由层给路由起别名

  语法：

  ```python
  # 路由层， 起别名 name=‘路由别名’
  re_path(r"^login/([0-9]{2})/$", views.login, name='login')
  
  # 视图层,跳转到login页面， 反向解析 reverse('路由别名', args=(参数,))
  def index(request):
    return redirect(reverse("login",args=(10,))
  
  # 模版层, 反向解析 "{% url '路由别名' 符合正则匹配的参数  %}"
  <a href="{% url 'login' 10 %}" />
  ```

#### 正则路径（有名分组）

* 解决方案：在路由层给路由起别名

  语法：

  ```python
  # 路由层， 起别名 name=‘路由别名’
  re_path(r"^login/(?P<year>[0-9]{4})/$", views.login, name="login")
  
  # 视图层,跳转到login页面， 反向解析 reverse('路由别名', kwargs={"分组名":符合正则匹配的参数})
  def index(request):
    return redirect(reverse("login",kwargs={"year":3333}))
  
  # 模版层, 反向解析 "{% url '路由别名' 分组名=符合正则匹配的参数 %}"
  <form action="{% url 'login' year=3333 %}" method="post">
  ```

### 6.7 命名空间

* **存在问题：**路由别名 name 没有作用域，Django 在反向解析 URL 时，会在项目全局顺序搜索，当查找到第一个路由别名 name 指定 URL 时，立即返回。当在不同的 app 目录下的urls 中定义相同的路由别名 name 时，可能会导致 URL 反向解析错误。

* **解决：**使用命名空间。

* 语法：

  ```python
  # 命名空间
  include(("app名称：urls"，"app名称"))
  # 视图中反向解析调用
  return redirect(reverse('app名称:路由别名'))
  # 模版中反向解析调用
  {% url 'app名称:路由别名' %}
  
  
  # 例
  path("app01/", include(("app01.urls","app01"))) 
  path("app02/", include(("app02.urls","app02")))
  
  # 不同app路由起同样的名称
  # app01
  path('login/', views.login, name='login')
  
  # app02
  path('login/', views.login, name='login')
  ```

## 7、Admin管理工具



## 8、ORM-单表实例

## 9、ORM-多表实例

## 10、ORM-聚合查询

## 11、Form组件

## 12、Django用户认证（Auth）组件

## 13、Cookie/Session

## 14、Django中间件

## 15、FBV与CBV

## 16、Nginx+uwsgi安装配置



















