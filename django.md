# Django

## 简要介绍

Django - The web framework for perfectionists with deadlines.

> Django is a high-level Python web framework that encourages rapid development and clean, pragmatic design. Built by experienced developers, it takes care of much of the hassle of web development, so you can focus on writing your app without needing to reinvent the wheel. It’s free and open source.

Django 框架是一个高级 Python Web 框架. Django 使你能够快速开发并设计出简洁优雅的 Web 应用, 避免自己造轮子. Django 还包含了大量的社区拓展插件, 以满足各种应用场景的需求.

理论上我应该在这里放一张 Django 的官网截图和链接, 但是 Django 官网的 Django 可能挂了 (怎么回事呢):

[www.djangoproject.com](https://www.djangoproject.com/)

![Django 官网](./django-project-website-down.png)

## 项目架构

### 新建项目后的原始结构

在编写作业的 Repo 时, 我建了几个 commit. 在 `cbf610094cd18aef623f848460e40bc25d72ee02` 能看到项目刚刚建立时的目录结构.

为了新建一个 Django 项目, 我们需要使用 `django-admin` 命令 (装完 Django 就有):

```sh
django-admin startproject sast2024_django_hw
```

此时:

```sh
$ tree
.
├── manage.py
└── sast2024_django_hw
    ├── asgi.py
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

1 directory, 6 files
```

**`manage.py`** 是一个默认的命令行工具, 用于管理 Django 项目. 我们后面的乱七八糟的操作都是通过这个文件来进行的. 一般来说 **不需要改动这个文件**

**`sast2024_django_hw`** 是 Django **项目路径** 的名称, 与 `startproject` 指定的项目名称相同.

在项目路径下有 5 个文件;

`__init__.py` 是一个空文件, 用于标识该目录是一个 Python 包. 啥也没有, 不用管.

**`settings.py`** 是项目的 **配置文件**.

**`urls.py`** 是一个 **URL 配置文件**, 用于定义 URL 路由 (换句话说就是每一个 URL 应该由谁处理).

**`wsgi.py`** 是一个 WSGI (Web Server Gateway Interface) 应用程序, 用于 **处理同步请求**.

**`asgi.py`** 是一个 ASGI (Asynchronous Server Gateway Interface) 应用程序, 用于 **处理异步请求**.

**重要提示**: 如果要将 Django 应用用于生产环境, 请 **不要使用 `./manage.py runserver` 命令**, 请 **参考 [WSGI 部署方式](https://docs.djangoproject.com/en/5.0/howto/deployment/wsgi/) 或 [ASGI 部署方式](https://docs.djangoproject.com/en/5.0/howto/deployment/asgi/) 进行部署!!!**

再说一遍, 请 **不要使用 `./manage.py runserver` 命令**, 请 **参考 [WSGI 部署方式](https://docs.djangoproject.com/en/5.0/howto/deployment/wsgi/) 或 [ASGI 部署方式](https://docs.djangoproject.com/en/5.0/howto/deployment/asgi/) 进行部署**

### 新建应用后的结构

在创建项目之后, 可以使用 `./manage.py startapp app_name` 命令来创建一个应用. 查看 `68881b83dd439ee6bbae4bc73c593ab031bfc287` 可以看到在创建 `course` 应用之后的项目结构.

```sh
$ tree
.
├── course
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── manage.py
└── sast2024_django_hw
    ├── asgi.py
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

3 directories, 13 files
```

`startapp` 命令创建了一个名为 `course` 的文件夹 (同样是 Python Module).

在这个文件夹中, 有以下文件:

**`admin.py`** 是一个用于 **管理后台** 的文件, 用于定义管理后台的模型和视图. 可以不用, 不过不得不说这玩意确实挺好用.

**`apps.py`** 是一个用于 **定义应用** 的文件, 用于定义应用的名称和配置 (不过我到现在为止似乎还没有用到过这玩意).

**`migrations`** 是一个用于 **数据库迁移** 的文件夹, 用于定义数据库的变更和版本控制. 一般来说这个文件夹里面的东西都是自动生成的; 但是有时候如果你有特殊的需求, 你也可以手动创建一些文件以获得对数据库迁移的精细化的控制.

接下来的 3 个文件是常用且重要的文件:

**`models.py`** 是一个用于 **定义模型** 的文件, 用于定义数据库的表结构和数据类型.

**`views.py`** 是一个用于 **定义视图** 的文件, 用于定义视图函数和视图类.

**`tests.py`** 是一个用于 **定义单元测试** 的文件, 用于定义测试用例和测试数据.

而实际上, **Django 项目的定义是十分灵活的**. 比如, 只要你在应用文件夹 (相当于应用这个 module 内) 任意位置定义了类 `AnyModel` (比如在 admin.py 里面)

```python
class AnyModel(models.Model):
    pass
```

在使用 `./manage.py makemigrations` 命令时都能被识别到.

```sh
$ ./manage.py makemigrations
Migrations for 'course':
  course/migrations/0002_anymodel.py
    - Create model AnyModel
```

而在任意位置定义函数 `def my_view(request): pass` 后, 都能把它作为一个视图函数注册到 URL 路由中.

但是, 这 **并不代表着你应该到处注册**. 建议对一个较为复杂的项目, 把 `views.py` 替换成 `views/` 目录, 把各种视图函数分门别类放在子文件里面. 对 `models.py` 和 `tests.py` 也是如此.

创建应用之后, 不要忘了在 `settings.py` 中注册应用 (我差点忘了写这句话了):

```python
INSTALLED_APPS = [
    ...
    'course'
]
```

## Django 中的 MVC

我们认为, MVC 中的 M 对应 `Model`, V 对应 `Template`, C 对应 `View`.

![Django MVC](./django-mvc.png)

### Model 模型

模型, 也就是 Django 的数据库 ORM 框架.

> A model is the single, definitive source of information about your data. It contains the essential fields and behaviors of the data you’re storing. Generally, each model maps to a single database table.

Model 是后端的核心: **如何高效保存数据, 使得应用运行时的任意与数据库有关的操作的复杂度能控制在可以接受的范围内**

Django 支持 [多种数据库后端](https://docs.djangoproject.com/en/5.0/ref/databases/) (这不重要, 但也不一定不重要), 但 Django 做了大量的处理, 使得无论你用的是哪一种数据库, 你都能以同样的方式操作 Model, 由 Django 自动处理数据库的细节.

Django 的 Model 是一个 Python 类, 这个类继承自 `django.db.models.Model`, 并且这个类中的每一个属性都对应数据库表中的一个字段.

### Routing 路由

### View 视图

### Template 模板

由于是前后端分离的架构, 所以 Template 我们实际上用不到. 如果你想了解, 可以参考 [Django Template](https://docs.djangoproject.com/en/5.0/topics/templates/).
