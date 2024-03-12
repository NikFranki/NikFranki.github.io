---
layout:     post
title:      "Python 编程从入门到实践-chapter18"
date:       2024-03-10
subtitle:   ""
author:     "franki"
header-img: ""
catalog: true
tags:
    - Python
---

## Django 入门

Django 是最流行的 Python Web 框架，提供了一系列旨在帮助开发交互式网站的工具

### 建立项目

#### 指定规范

完整的规范要详细说明项目的目标，阐述项目的功能，讨论项目的外观和用户界面。这里不制定完整的项目规划，只列出一些明确的目标，以突出开发的重点。
> 我们要编写一个名为 “学习笔记” 的 Web 应用程序，让用户能够记录感兴趣的主题，并在学习每个主题的过程中添加日志条目。“学习笔记”的主页对这个网站进行描述，并邀请用户注册或登录，用户登录后，可以创建新主题、添加新条目以及阅读既有条目

#### 建立虚拟环境

虚拟环境是系统的一个位置，你可以在其中装包，并将这些包和其他 Python 包隔离开来。

为项目新建一个目录，将其命名为 my_site，再在终端中切换到这个目录，并执行如下命令创建一个虚拟环境

```bash
my_site$ python -m venv ll_env
```

注意如果你用的是 python3，记得命令中要使用 python3

#### 激活虚拟环境

```bash
my_site$ source ll_env/bin/activate
my_site(ll_env)$
```

![activate-venv](/images/posts/django/activate-venv.png)

要停止使用虚拟环境，可执行命令 deactivate:

```bash
my_site(ll_env)$ deactivate
my_site$
```

#### 安装 Django

```bash
my_site(ll_env)$ pip install --upgrade pip
my_site(ll_env)$ pip install django
```

安装包的时候，可以指定包的版本，例如：pip install django=4.1

#### 在 Django 中创建项目

```bash
my_site(ll_env)$ django-admin startproject my_site_project .
```

![alt text](/images/posts/django/startproject.png)

这个末尾（.）让新项目使用合适的目录结构，这样在开发完成后可轻松地将应用程序部署到服务器上

#### 创建数据库

```bash
my_site(ll_env)$ python manage.py migrate
```

![alt text](/images/posts/django/migrate.png)

修改数据库称为迁移数据库

执行之后，文件夹里面会多出一个 db.sqlite3。SQLite 是一种使用单个文件的数据库，是编写简单应用程序的理想选择，因为它让你不用太关注数据的管理问题

### 查看项目

```bash
my_site(ll_env)$ python manage.py runserver
```

![alt text](/images/posts/django/runserver.png)

### 创建应用程序

```bash
my_site(ll_env)$ python manage.py startapp my_sites
```

![alt text](/images/posts/django/startapp.png)

#### 定义模型

模型告诉 Django 如何处理应用程序中存储的数据。模型就是类，包含属性和方法。

主题的模型

```python
from django.db import models

class Topic(models.Model):
  """用户学习的主题"""
  text = models.CharField(max_length=200)
  date_added = models.DateTimeField(auto_now_add=True)

  def __str__(self):
    return self.text
```

最好告诉 Django，你希望它如何表示模型的实例。如果模型有__str__()方法，那么每当需要生成表示模型实例的输出时，Django 都会调用这个方法。它返回了 text 的值

#### 激活模型

打开 settings.json，将前面的应用程序添加到这个列表中

```python
# Application definition

INSTALLED_APPS = [
    # 我的应用程序
    "my_sites",

    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]
```

终端输入

```bash
my_site(ll_env)$ python manage.py makemigrations my_sites
```

![alt text](/images/posts/django/makemigrations.png)

命令 makemigrations 让 Django 确定如何修改数据库，使其能够存储与前面定义的新模型相关联的数据，生成一份 0001_initial.py 的迁移文件，这个文件将在数据库中为模型 Topic 创建一个表。

下面应用这种迁移，让 Django 替我们修改数据库

```bash
my_site(ll_env)$ python manage.py migrate
```

#### Django 管理网站

1. 创建超级用户

```bash
my_site(ll_env)$ python manage.py createsuperuser
```

```bash
$ python manage.py createsuperuser

Username (leave blank to use 'da.liu'): admin
Email address:    
Password: 
Password (again): 
This password is too short. It must contain at least 8 characters.
This password is too common.
This password is entirely numeric.
Superuser created successfully.
(ll_env) 
# da.liu @ C02FT5YSMD6M in ~/person/python-learning/my_site ll_env [17:16:55] 
```

这里的密码输入 123 了之后不会显示，直接回车即可

2. 向管理网站注册模型

Django 自动在管理网站中添加了一些模型，如 User 和 Group，如果我们要添加我们创建的模型，则必须手动注册

在 my_sites/admin.py 创建了一个名为 admin.py 的文件：

```python
from django.contrib import admin

from .models import Topic

admin.site.register(Topic)
```

访问 `http://localhost:8000/admin/`，输入刚刚创建用户名和密码，将会看到下面的内容

![alt text](/images/posts/django/admin.png)

3. 添加主题

向管理网站添加了 Topic 模型后，我们来添加 Chess 和 RockClimbing

#### 定义模型 Entry

每个条目都与特定的主题相关联，这种关系称为多对一关系，即多个条目可关联到同一个主题

```python
# --..snip---
class Entry(models.Model):
  """某个主题的具体知识"""
  topic = models.ForeignKey(Topic, on_delete=models.CASCADE)
  text = models.TextField()
  date_added = models.DateTimeField(auto_now_add=True)

  class Meta:
    verbose_name_plural = 'entries'

  def __str__(self):
    return f"{self.text[:50]}..."
```

ForeignKey（外键）是一个数据库术语，它指向数据库中的另一条记录，这里则是将每个条目关联到特定的主题。创建每个主题时，都为其分配一个键（ID）。

实参 on_delete=models.CASCADE 让 Django 在删除主题的同时删除所有与之相关联的条目，这称为级联删除（cascading delete）。

#### 迁移模型 Entry

```bash
my_site(ll_env)$ python manage.py makemigrations my_sites
my_site(ll_env)$ python manage.py migrate
```

#### 向管理网站注册 Entry

修改 my_sites/admin.py

```python
from django.contrib import admin

from .models import Topic, Entry

admin.site.register(Topic)
admin.site.register(Entry)
```

重新运行项目（python manage.py runserver），在 admin 页面 <http://localhost:8000/admin/>，分别在 Chess 和 RockClimbing 创建对应的 Entry。

#### Django shell

输入一些数据后，就可以通过交互式终端会以会话以编程的方式看这些数据了。这种会话方式叫做 `Django shell`，是测试项目和排除故障的理想之地。

```bash
python manage.py shell
Python 3.11.4 (main, Jul 25 2023, 17:08:53) [Clang 13.0.0 (clang-1300.0.29.30)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from my_sites.models import Topic
>>> Topic.objects.all()
<QuerySet [<Topic: Chess>, <Topic: RockClimbing>]>
>>> topics = Topic.objects.all()
>>> for topic in topics:
...     print(topic)
... 
Chess
RockClimbing
>>>
```

知道主题对象的 ID 后，就可以使用 Topic.objects.get() 方法获取该对象并查看其属性了。

```bash
>>> t = Topic.objects.get(id=1)
>>> t.text
'Chess'
>>> t.date_added
datetime.datetime(2024, 3, 10, 10, 2, 33, 695412, tzinfo=datetime.timezone.utc)
>>>
```

还可以查看与主题相关的条目。前面给模型 Entry 定义了属性 topic，这是一个 ForeignKey，将条目与主题关联起来。

```bash
>>> t.entry_set.all()
<QuerySet [<Entry: The opening is the first part of the game, roughly...>, <Entry: In the opening phase of the game, it's important t...>]>
>>>
```

### 创建网页：学习笔记主页

使用 Django 创建的网页的过程分为三个阶段：定义 URL，编写视图，以及编写模板。按什么顺序完成这三个阶段无关紧要。

URL 模式描述了 URL 的构成，让 Django 知道如何将浏览器的请求和网站 URL 匹配，以确定返回哪个网页

每个 URL 都被映射到特定的视图。视图函数获取并处理网页所需的数据。视图函数通常用模板来渲染网页，而模板定义的网页的总体结构。

#### 映射 URL

主页的 URL 是最重要的，它是用户用来访问项目的基础 URL。

打开项目文件夹 my_site_project 中的文件 urls.py

```python
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path("admin/", admin.site.urls),
]
```

urlpatterns 包含了应用程序中的 URL。admin.site.urls 定义了可在管理网站中请求的所有 URL

加入 my_sites 的 URL

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("my_sites.urls")),
]
```

导入了函数 include（），还添加了一行代码来包含 my_sites.urls 模块

```python
from django.urls import path

from . import views

app_name = 'my_sites'
urlpatterns = [
  path('', views.index, name='index')
]
```

导入函数 path，因为需要使用它将 URL 映射到视图。

然后导入 views 模块，其中的句点（.）让 Python 从当前 urls.py 模块所在的文件夹中导入 views

接收三个参数，第一个参数字符串，帮助 Django 正确地路由请求。将请求到的 URL 给一个视图，并为此搜索所有的 URL 模式，以找到与当前页面匹配的

path 的第二个实参指定了要调用 view.py 中的哪个函数。当请求的 URL 与前述正则表达式匹配时，Django 将调用 vies.py 中的 index() 函数

path 的第三个实参将这个 URL 模式的名称指定为 index，让我们能够在其他项目文件中轻松地引用它

每当需要提供这个主页的链接时，都是用这个名称，而不编写 URL

#### 编写视图

视图函数接受请求中的信息，准备好生成网页所需的数据，再将这些数据发送给浏览器。

在 my_sites 的 views 文件写下

```python
from django.shortcuts import render

# Create your views here.

def index(request):
  return render(request, 'my_sites/index.html')
```

render 函数只传了两个参数，一个是对象 request，另一个是模板名称

#### 编写模板

在 my_sites 目录中新建一个文件夹，并将其命名为 templates。在文件夹中再建一个文件夹叫做 my_sites。这样做是为了 Django 能够明确解读文件结构，在最里面的 my_sites 文件夹中新建一个文件 index.html，并输入以下内容

```html
<p>My Sites</p>

<p>Learning Log helps you keep truck of your learning, for any topic you're interested in.</p>
```

现在终端输入 python manage.py runserver，可以看到以下内容

![alt text](/images/posts/django/index-page.png)

### 创建其他网页

#### 模板继承

1. 父模板

创建一个名为 base.html

```html
<p>
  <a href="{% url 'my_sites:index' %}">My Site</a>
</p>

{% block content %}{% endblock content %}
```

模板标签是用花括号和百分号 {% %} 表示的，实质是一小段代码，生成要在网页中显示的信息。

这里的模板标签{% url 'my_site:index' %} 生成一个 URL，该 URL 与 my_sites/urls.py 中定义的名为 index 的 URL 模式匹配。

my_sites 是一个命名空间，而 index 该命名空间中的一个名称独特的 URL 模式。这个命名空间来自在文件 my_sites/urls.py 中赋给 app_name 的值。

最后一行插入了一对标签，这一块名为 content，是一个占位符，其中包含的信息由子模板指定

子模板并非必须定义父模板中的每一个快，因此在父模板中，可以使用任意多个快来预留空间，而子模板可根据需要定义相应的块

2. 子模板

重写 index.html，使其继承 base.html。

```html
{% extends 'my_sites/base.html' %}

{% block content %}
  <p>Learning Log helps you keep truck of your learning, for any topic you're interested in.</p>
{% endblock content %}
```

使用 extends 来指定需要继承的模板

#### 显示所有主题的页面

1. URL模式

定义显示所有主题的页面 URL，通常，使用一个简单的 URL 片段来指出显示的信息，这里使用 topics，因此， URL http://localhost:8000/topics 将返回显示所有主题的页面

进入 my_sites/urls.py，继续添加以下内容

```python
from django.urls import path

from . import views

app_name = 'my_sites'
urlpatterns = [
  path('', views.index, name='index'),
  path('topics/', views.topics, name='topics')
]
```

2. 视图

上面 urls.py 的 topics() 函数需要从数据库中获取一些数据，并将其交给模板，故需要在 views.py 中添加的代码为：

```python
from django.shortcuts import render

from .models import Topic

# Create your views here.

def index(request):
  return render(request, 'my_sites/index.html')

def topics(request):
  """显示所有的主题"""
  topics = Topic.objects.order_by('date_added')
  context = {'topics': topics}
  return render(request, 'my_sites/topics.html', context)
```

导入与所需数据相关联的模型，请求数据库，请求提供 Topic 对象，并根据属性 date_added 进行排序。返回的查询集被赋给 topics，创建一个上下文 context，它是一个字典，其中里面的键用来在模板中访问数据的名称，而值是发送给模板的数据，render() 函数的第三个参数正是传递了 context

3. 模板

在 templates mysites 文件夹里面新建一个 topics.html 的文件，内容为：

```html
{% extends 'my_sites/base.html' %}

{% block content %}
  <p>Topics</p>

  <ul>
    {% for topic in topics %}
      <li>{{ topic.text }}</li>
    {% empty %}
      <li>No topics have been added yet.</li>
    {% endfor %}
  </ul>

  <p>Learning Log helps you keep truck of your learning, for any topic you're interested in.</p>

{% endblock content %}
```

首先继承父模板，里面的 for 循环使用 {% for topic in topics %} do something {% endfor %} 一对标识，来进行列表的循环

接着在主页模板 index.html 中加入 topics 的链接跳转

```html
<p>
  <a href="{% url 'my_sites:index' %}">My Site</a>
  <a href="{% url 'my_sites:topics' %}">Topics</a>
</p>

{% block content %}{% endblock content %}
```

现在主页会出现 topics 的链接，点击它，会跳转到 topics 的页面，可以看到

![alt text](/images/posts/django/topics-page.png)
