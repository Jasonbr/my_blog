# my_blog
一、创建新项目

在工程目录下打开命令框，运行以下指令：

[cpp] view plain copy

 
$ django-admin startproject my_blog  



文件结构如下：

my_blog
├── manage.py
└── my_blog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

1 directory, 5 files

二、创建app

建立一个article app

[cpp] view plain copy

 
$ python manage.py startapp article  



此时项目结构如下：

── article
│   ├── __init__.py
│   ├── admin.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
├── my_blog
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py


并在my_blog/my_blog/setting.py下添加新建app

[cpp] view plain copy

 
INSTALLED_APPS = [  
    'django.contrib.admin',  # 默认添加后台管理功能  
    'django.contrib.auth',  
    'django.contrib.contenttypes',  
    'django.contrib.sessions',  
    'django.contrib.messages',  
    'django.contrib.staticfiles',  
    'article',  # 这里填写的是app的名称  
]  



三、应用migrations，激活模型

添加完app后，运行下列命令：

[cpp] view plain copy

 
$ python manage.py migrate  



输出：



四、设置数据库

Django项目建成后，默认设置了使用SQLite数据库，在my_blog/my_blog/setting.py 中可以查看和修改数据库设置

[cpp] view plain copy

 
DATABASES = {  
    'default': {  
        'ENGINE': 'django.db.backends.sqlite3',  
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),  
    }  
}  

可以设置其他数据库：MySQL, PostgreSQL等。


五、创建Models

在my_blog/article/models.py下编写如下程序：

[cpp] view plain copy

 
# -*- coding: utf-8 -*-  
from __future__ import unicode_literals  
  
from django.db import models  
  
# Create your models here.  
class Article(models.Model) :  
    title = models.CharField(max_length = 100)  #博客题目  
    category = models.CharField(max_length = 50, blank = True)  #博客标签  
    date_time = models.DateTimeField(auto_now_add = True)  #博客日期  
    content = models.TextField(blank = True, null = True)  #博客文章正文  
  
    #python2使用__unicode__, python3使用__str__  
    def __unicode__(self) :  
        return self.title  
  
    class Meta:  #按时间下降排序  
        ordering = ['-date_time']  

其中__unicode__(self) 函数Article对象要怎么表示自己，一般系统默认用<Article: Article object> 来表示对象，通过这个函数可以告诉系统使用title字段来表示这个对象。


CharField 用于存储字符串，max_length设置最大长度
TextField 用于存储大量文本
DateTimeField 用于存储时间，auto_now_add设置True表示自动设置对象增加时间

六、同步数据库


[cpp] view plain copy

 
$ python manage.py migrate #命令行运行该命令  



因为已经执行过该命令了，因此会出现如下提示：


现在需要执行下面的命令：

[cpp] view plain copy

 
$ python manage.py makemigrations  


输出：


现在重新运行以下命令：

[cpp] view plain copy

 
$ python manage.py migrate  



出现如下提示表示操作成功：


migrate命令按照app顺序建立或者更新数据库，将models.py与数据库同步。

七、使用Shell进行数据库的增删改查

运行命令：

[cpp] view plain copy

 
$ python manage.py shell  





在这里可以进行数据库的增删改查

[cpp] view plain copy

 
>>> from article.models import Article  
>>> #create数据库增加操作  
>>> Article.objects.create(title = 'Hello World', category = 'Python', content = '我们来做一个简单的数据库增加操作')  
<Article: Article object>  
>>> Article.objects.create(title = 'Django Blog学习', category = 'Python', content = 'Django简单博客教程')  
<Article: Article object>  
  
>>> #all和get的数据库查看操作  
>>> Article.objects.all()  #查看全部对象, 返回一个列表, 无对象返回空list  
[<Article: Article object>, <Article: Article object>]  
>>> Article.objects.get(id = 1)  #返回符合条件的对象  
<Article: Article object>  
  
>>> #update数据库修改操作  
>>> first = Article.objects.get(id = 1)  #获取id = 1的对象  
>>> first.title  
'Hello World'  
>>> first.date_time  
datetime.datetime(2014, 12, 26, 13, 56, 48, 727425, tzinfo=<UTC>)  
>>> first.content  
'我们来做一个简单的数据库增加操作'  
>>> first.category  
'Python'  
>>> first.content = 'Hello World, How are you'  
>>> first.content  #再次查看是否修改成功, 修改操作就是点语法  
'Hello World, How are you'  
  
>>> #delete数据库删除操作  
>>> first.delete()  
>>> Article.objects.all()  #此时可以看到只有一个对象了, 另一个对象已经被成功删除  
[<Article: Article object>]    


八、Admin后台管理界面

Django有一个优秀的特性，内置了Django admin后台管理界面，方便管理者进行添加和删除网站的内容。

设置Admin，可以在my_blog/my_blog/setting.py中查看

INSTALLED_APPS = (
    'django.contrib.admin',  #默认添加后台管理功能
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'article'
)


同时也可以添加进入后天管理的url，可以在my_blog/my_blog/urls.py中查看

[cpp] view plain copy

 
urlpatterns = [  
    # url(r'^$', 'article.views.home'),  
    # url(r'^(?P<my_args>\d+)/$', 'article.views.detail', name='detail'),  
    url(r'^admin/', admin.site.urls),  # 可以使用设置好的url进入网站后台  
    url(r'^$', 'article.views.home'),  
]  



九、创建超级用户

使用如下命令创建超级用户，可以登录后台，对数据库进行操作

[cpp] view plain copy

 
$ python manage.py createsuperuser  




输入用户名，邮箱，密码就能够创建一个超级用户，然后运行服务器，在浏览器中输入localhost:8000/admin

界面如下所示：


输入账号密码可以进入后台管理，



发现并没有数据库信息，不能操作数据库，于是在my_blog/article/admin.py中加入代码：

[cpp] view plain copy

 
from django.contrib import admin  
from article.models import Article  
# Register your models here.  
admin.site.register(Article)  


保存后，刷新下页面，如下所示：



使用第三方插件美化后台界面，例如使用django-admin-bootstrap美化后台管理界面
安装插件

[cpp] view plain copy

 
$ pip install bootstrap-admin  

配置：

在my_blog/my_blog/setting.py中修改INSTALLED_APPS


INSTALLED_APPS = (
    'bootstrap_admin',  #一定要放在`django.contrib.admin`前面
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'article',
)

from django.conf import global_settings
TEMPLATE_CONTEXT_PROCESSORS = global_settings.TEMPLATE_CONTEXT_PROCESSORS + (
    'django.core.context_processors.request',
)
BOOTSTRAP_ADMIN_SIDEBAR_MENU = True


刷新界面




INSTALLED_APPS = (
    'bootstrap_admin',  #一定要放在`django.contrib.admin`前面
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'article',
)

from django.conf import global_settings
TEMPLATE_CONTEXT_PROCESSORS = global_settings.TEMPLATE_CONTEXT_PROCESSORS + (
    'django.core.context_processors.request',
)
BOOTSTRAP_ADMIN_SIDEBAR_MENU = True
​
十、View和URL

网页程序的逻辑：request进来 -> 从服务器获取数据 -> 处理数据 -> 把网页呈现出来

url 设置相当于客户端向服务器发出request请求的入口，并用来指明要调用的程序逻辑
views 用来处理程序逻辑，然后呈现到template（一般为GET方法，POST方法略有不同）
template 一般为html+CSS的形式，主要是呈现给用户的表现形式

Django中views里面的代码时一个一个函数逻辑，处理客户端（浏览器）发送的HTTPRequest，然后返回HTTPResponse。
那么开始在my_blog/article/views.py中编写简单的逻辑


[cpp] view plain copy

 
from django.shortcuts import render  
from django.http import HttpResponse  
# Create your views here.  
def home(request):  
    return HttpResponse("Hello World, Django")  



如何使这个逻辑在http请求进入时被调用，这里需要在my_blog/my_blog/urls.py中进行url设置


[cpp] view plain copy

 
# -*- coding: utf-8 -*-  
from django.conf.urls import patterns, include, url  
from django.contrib import admin  
  
urlpatterns = [  
    # url(r'^$', 'article.views.home'),  
    # url(r'^(?P<my_args>\d+)/$', 'article.views.detail', name='detail'),  
    url(r'^admin/', admin.site.urls),  
    url(r'^$', 'article.views.home'),  
]  



url() 函数有四个参数，两个是必须的：regex和views，两个可选的：kwargs和name
regex是regular expression的简写，这是字符串中的模式匹配的一种语法，Django将请求的URL从上至下依次匹配列表中的正则表达式，直到匹配到一个为止。
view当Django匹配了一个正则表达式就会调用指定的view逻辑，上面代码中会调用article/views.py中的home函数
kwargs任意关键字参数可传一个字典至目标view
name命名你的URL，使url在Django的其他地方使用，特别是在模块中

很多时候我们希望给view中的函数逻辑传入参数，从而呈现我们想要的结果
现在再my_blog/article/views.py 加入如下代码：


[cpp] view plain copy

 
def detail(request, my_args):  
    return HttpResponse("You're looking at my_args %s." % my_args)  



在my_blog/my_blog/urls.py 中设置对应的url

[cpp] view plain copy

 
urlpatterns = [  
    # url(r'^$', 'article.views.home'),  
    # url(r'^(?P<my_args>\d+)/$', 'article.views.detail', name='detail'),  
    url(r'^admin/', admin.site.urls),  
    url(r'^$', 'article.views.home'),  
    url(r'^(?P<my_args>\d+)/$', 'article.views.detail', name='detail'),  
]  


^(?P<my_args>\d+)/$这个正则表达式的意思是将传入的一位或者多位数字作为参数传递到views中的detail作为参数, 其中?P<my_args>定义名称用于标识匹配的内容

输入以下url：
http://localhost:8000/1000/



尝试传参访问数据库

修改my_blog/article/views.py中的代码：


[cpp] view plain copy

 
from django.shortcuts import render  
from django.http import HttpResponse  
from article.models import Article  
# Create your views here.  
def home(request):  
    return HttpResponse("Hello World, Django")  
  
def detail(request, my_args):  
    post = Article.objects.all()[int(my_args)]  
    str = ("title = %s, category = %s, date_time = %s, content = %s"   
        % (post.title, post.category, post.date_time, post.content))  
    return HttpResponse(str)  


访问 http://localhost:8000/1/
1表示访问数据库的article表中的第一行数据




十一、第一个Template

到目前为止还没有写前端，只是简单的把后端数据显示到页面上，没有涉及HTML代码。
目前流行的前端工具有Bootstrap和Pure，本文中使用更轻量级的Pure
1、
在app的文件夹article下创建templates文件夹
文件构成如下：

my_blog
├── article
│   ├── __init__.py
│   ├── templates
│   ├── admin.py
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   ├── __init__.py
│   │   └── __pycache__
│   │       ├── 0001_initial.cpython-34.pyc
│   │       └── __init__.cpython-34.pyc
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── manage.py
├── my_blog
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-34.pyc
    │   ├── settings.cpython-34.pyc
    │   ├── urls.cpython-34.pyc
    │   └── wsgi.cpython-34.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py
 
把template创建在app的文件目录下就不需要设置templates的位置路径了
2、需要设置templates路径
在my_blog下添加文件名, 文件夹名为templates
mkdir templates
#看到当前文件构成
my_blog
├── article
│ ├── __init__.py
│ ├── __pycache__
│ │ ├── __init__.cpython-34.pyc
│ │ ├── admin.cpython-34.pyc
│ │ ├── models.cpython-34.pyc
│ │ └── views.cpython-34.pyc
│ ├── admin.py
│ ├── migrations
│ │ ├── 0001_initial.py
│ │ ├── __init__.py
│ │ └── __pycache__
│ │ ├── 0001_initial.cpython-34.pyc
│ │ └── __init__.cpython-34.pyc
│ ├── models.py
│ ├── tests.py
│ └── views.py
├── db.sqlite3
├── manage.py
├── my_blog
│ ├── __init__.py
│ ├── __pycache__
│ │ ├── __init__.cpython-34.pyc
│ │ ├── settings.cpython-34.pyc
│ │ ├── urls.cpython-34.pyc
│ │ └── wsgi.cpython-34.pyc
│ ├── settings.py
│ ├── urls.py
│ └── wsgi.py
└── templates
在my_blog/my_blog/setting.py下设置templates的位置
TEMPLATE_DIRS = (os.path.join(BASE_DIR, 'templates').replace('\\','/'),)
意思是告知项目templates文件夹在项目根目录下

在templates文件夹中创建一个html文件， templates/test.html


[cpp] view plain copy

 
<!--在test.html文件夹下添加-->  
<!DOCTYPE html>  
<html>  
    <head>  
        <title>Just test template</title>  
        <style>  
            body {  
               background-color: red;  
            }  
            em {  
                color: LightSeaGreen;  
            }  
        </style>  
    </head>  
    <body>  
        <h1>Hello World!</h1>  
        <strong>{{ current_time }}</strong>  
    </body>  
</html>  



其中{{ current_time }}是Django Template中变量的表示方式
在article/view.py中添加一个函数逻辑
[cpp] view plain copy

 
from django.shortcuts import render  
from django.http import HttpResponse  
from article.models import Article  
from datetime import datetime  
# Create your views here.  
def home(request):  
    return HttpResponse("Hello World, Django")  
  
  
def detail(request, my_args):  
    post = Article.objects.all()[int(my_args)]  
    str = ("title = %s, category = %s, date_time = %s, content = %s"   
        % (post.title, post.category, post.date_time, post.content))  
    return HttpResponse(str)  
  
  
def test(request) :  
    return render(request, 'test.html', {'current_time': datetime.now()})  



render()函数中第一个参数是request 对象, 第二个参数是一个模板名称，第三个是一个字典类型的可选参数. 它将返回一个包含有给定模板根据给定的上下文渲染结果的 HttpResponse对象。
然后设置对应的url在my_blog/urls.py下
[cpp] view plain copy

 
from django.conf.urls import url  
from django.contrib import admin  
  
urlpatterns = [  
    url(r'^admin/', admin.site.urls),  
    url(r'^(?P<my_args>\d+)/$', 'article.views.detail', name='detail'),  
    url(r'^test/$', 'article.views.test'),  
]  





十二、使用Pure编写template

在templates文件夹下增加base.html，代码如下：


[cpp] view plain copy

 
<!doctype html>  
<html lang="en">  
<head>  
    <meta charset="utf-8">  
<meta name="viewport" content="width=device-width, initial-scale=1.0">  
<meta name="description" content="A layout example that shows off a blog page with a list of posts.">  
  
    <title>Jason's Blog</title>  
    <link rel="stylesheet" href="http://yui.yahooapis.com/pure/0.5.0/pure-min.css">  
    <link rel="stylesheet" href="http://yui.yahooapis.com/pure/0.5.0/grids-responsive-min.css">  
    <link rel="stylesheet" href="http://picturebag.qiniudn.com/blog.css">  
</head>  
<body>  
<div id="layout" class="pure-g">  
    <div class="sidebar pure-u-1 pure-u-md-1-4">  
        <div class="header">  
            <h1 class="brand-title">Jason's Blog Blog</h1>  
            <h2 class="brand-tagline">少言 - Jason's Memory</h2>  
            <nav class="nav">  
                <ul class="nav-list">  
                    <li class="nav-item">  
                        <a class="pure-button" href="http://blog.csdn.net/Jason's Blog">Github</a>  
                    </li>  
                    <li class="nav-item">  
                        <a class="pure-button" href="http://blog.csdn.net/Jason's Blog">Weibo</a></span>  
                    </li>  
                </ul>  
            </nav>  
        </div>  
    </div>  
  
    <div class="content pure-u-1 pure-u-md-3-4">  
        <div>  
            {% block content %}  
            {% endblock %}  
            <div class="footer">  
                <div class="pure-menu pure-menu-horizontal pure-menu-open">  
                    <ul>  
                        <li><a href="http://blog.csdn.net/xiaoquantouer">About Me</a></li></span>  
                        <li><a href="http://blog.csdn.net/xiaoquantouer">Twitter</a></li></span>  
                        <li><a href="http://blog.csdn.net/xiaoquantouer">GitHub</a></li></span>  
                    </ul>  
                </div>  
            </div>  
        </div>  
    </div>  
</div>  
  
</body>  
</html>  

上面这段html编写的页面是一个模板, 其中{% block content %} {% endblock %}字段用来被其他继承这个基类模板进行重写


继续在templates文件夹下添加home.html文件

[cpp] view plain copy

 
{% extends "base.html" %}  
  
{% block content %}  
<div class="posts">  
    {% for post in post_list %}  
        <section class="post">  
            <header class="post-header">  
                <h2 class="post-title">{{ post.title }}</h2>  
  
                    <p class="post-meta">  
                        Time:  <a class="post-author" href="#">{{ post.date_time }}</a> <a class="post-category post-category-js" href="#">{{ post.category }}</a>  
                    </p>  
            </header>  
  
                <div class="post-description">  
                    <p>  
                        {{ post.content }}  
                    </p>  
                </div>  
        </section>  
    {% endfor %}  
</div><!-- /.blog-post -->  
{% endblock %}  


其中
- {% for <element> in <list> %}与{% endfor %}成对存在, 这是template中提供的for循环tag
- {% if <elemtnt> %} {% else %} {% endif %}是template中提供的if语句tag
- template中还提供了一些过滤器


然后修改my_blog/article/view.py，并删除test.html


