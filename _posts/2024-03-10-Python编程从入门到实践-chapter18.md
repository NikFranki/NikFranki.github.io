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
