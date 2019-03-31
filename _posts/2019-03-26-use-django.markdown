---
layout:     post
title:      "django的用户认证和注册"
subtitle:   "从零开始的技能阶梯、学习曲线"
date:       2019-03-26
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - django
---

# 前言

本文是作者学习Django的过程中的记录。

本文以Django官方文档[1]为依据，参考了rmzenwu发表在github的开源作品[2]。

实际上，本文作者是先下载了文献[2]，直接读源代码，发现不能很好的理解。

读完官方文档，再看文献[2]，豁然开朗。

本文记录了前述基本知识，记录了从零基础开发django用户登录程序的全过程。

所谓零基础，html、css的基本知识背景还是要先具备的。

按照官方文档的指引做完练习，文件结构已经基本具备，本文从文献[2]借鉴的主要是模板文件，省去了自行编写的大量时间。

另外修正了文献[2]的一些缺陷。

文献[2]没有对注册email的有效性做判断，导致以下结果：

- 空email可以注册用户

- 相同的email可以注册多个用户

本文通过增加字段限制条件、编写验证器，避免了这些问题。

文献[2]采用了自行编写的eamil验证后端，提供通过email确定登录用户的能力，本文没有采纳这个功能，仍然使用用户名识别用户。

# 1 创建project

```
$ python -m django --version  /* 查看django的版本 */
$ django-admin startproject mysite /* 创建项目 */
$ cd mysite /* 进入项目路径 */
$ tree mysite /* 查看文件结构 */
```

```bash
.
├── manage.py
└── mysite
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

```
$ python manage.py runserver /* 启动测试环境 */
```

警告：不要执行`python manage.py migrate`。

虽然启动测试环境时有红色警告提示你这样做。

否则定制User模型将有困难，除非不打算定制User。

# 2 创建app

```
$ python manage.py startapp myapp /* 创建app */
$ tree /* 查看文件结构 */
```

```bash
.
├── db.sqlite3
├── manage.py
├── myapp
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
└── mysite
    ├── __init__.py
    ├── __pycache__
    │   ├── __init__.cpython-37.pyc
    │   ├── settings.cpython-37.pyc
    │   ├── urls.cpython-37.pyc
    │   └── wsgi.cpython-37.pyc
    ├── settings.py
    ├── urls.py
    └── wsgi.py

```

```
$ python manage.py runserver /* 启动测试环境 */
```

# 3 使用模型（定制用户模型）

如果不需要定制User，或者已经执行`python manage.py migrate`，此节跳过。

## 3.1 设置模型

```python
# myapp/models.py

from django.db import models
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    class Meta(AbstractUser.Meta):
        pass
```

```python
# mysite/setting.py

INSTALLED_APPS = [
    'myapp.apps.MyappConfig',
]

AUTH_USER_MODEL = 'myapp.User'
```

## 3.2 启用admin

```python
#myapp/admin.py

from django.contrib import admin
from .models import User
admin.site.register(User)
```

## 3.3 安装（迁移）数据库

```
$ python manage.py makemigrations myapp
$ python manage.py sqlmigrate myapp 0001
$ python manage.py migrate
```

## 3.4 创建超级用户

```python
$ python manage.py createsuperuser
Username: admin
Email address: admin@example.com
Password: **********
Password (again): *********
```

## 3.5 使用admin

```
$ python manage.py runserver
```

在浏览器中查看“127.0.0.1：8000/admin/”，登录超级用户，进入admin。


# 4 使用view

```
$ vim myapp/urls.py
```

```python
# myapp/urls.py

from django.urls import path
from . import views

app_name = 'myapp'

urlpatterns = [
    path('', views.index, name='index'),
]
```

```python
# myapp/views.py

from django.http import HttpResponse
def index(request):
    return HttpResponse("Hello, world. You're at myapp index.")
```

```python
# mysite/urls.py

from django.urls import include
urlpatterns = [
    path('myapp/', include('myapp.urls')),
]
```

```
$ python manage.py runserver
```

在浏览器中查看“127.0.0.1：8000/myapp/”。

# 5 使用template

```
$ mkdir myapp/templates
$ mkdir myapp/templates/myapp
$ vim myapp/templates/myapp/show.html
```

{% raw %}
```html
<!--myapp/templates/myapp/show.html-->
{{msg}}
```
{% endraw %}

```python
#myapp/views.py

from django.shortcuts import render
def use_template(request):
    return render(request, 'myapp/show.html', {'msg': "this is a template."})
```

```python
# myapp/urls.py

urlpatterns = [
    path('use_template', views.use_template, name='use_template'),
]
```

```
$ python manage.py runserver
```

在浏览器中查看“127.0.0.1:8000/myapp/use_template/”。

# 6 使用form

```python
# myapp/urls.py

urlpatterns = [
    path('use_form/', views.use_form, name='use_form'),
]
```

```python
# myapp/views.py

def use_form(request):
    return render(request, 'myapp/use_form.html')
```

```
$ vim myapp/templates/myapp/use_form.html
```

{% raw %}
```html
<!--myapp/templates/myapp/use_form.html-->
<form action="{% url 'myapp:use_form' %}" method="post">
    username<input name="username" /><br/>
    password<input name="password" /><br/>
    email<input name="email" /><br/>
    <button type="submit" >注册</button>
</form>
```
{% endraw %}

```
$ python manage.py runserver
```

在浏览器中查看“127.0.0.1:8000/myapp/use_form/”。

# 7 使用form class

```python
# myapp/urls.py

urlpatterns = [
    path('use_form_class/', views.use_form_class, name='use_form_class'),
]
```

```
$ vim myapp/forms.py
```

```python
# myapp/forms.py

from django import forms
class MyForm(forms.Form):
    subject = forms.CharField(max_length=100)
    message = forms.CharField(widget=forms.Textarea)
    sender = forms.EmailField()
```

```python
# myapp/views.py

from .forms import MyForm
def use_form_class(request):
    if request.method == 'POST':
        form = MyForm(request.POST)
        if form.is_valid():
            return redirect('/')
    else:
        form = MyForm()
    return render(request, 'myapp/use_form_class.html', {'form': form})
```

```
$ vim myapp/templates/myapp/use_form_class.html
```

{% raw %}
```html
<!--myapp/templates/myapp/use_form_class.html-->
<form action="{% url 'myapp:use_form_class' %}" method="post">
    {% csrf_token %}
    {% for field in form %}
        {{ field.label_tag }}<br/>
        {{ field }}<br/>
        {{ field.errors }}<br/>
    {% endfor %}
    <input type="submit" value="Submit">
</form>
```
{% endraw %}

```
$ python manage.py runserver
```

在浏览器中查看“127.0.0.1:8000/myapp/use_form_class/”。

# 8 使用session

```python
# myapp/urls.py

urlpatterns = [
    path('use_session/', views.use_session, name='use_session'),
]
```

```python
# myapp/views.py

def use_session(request):
    request.session['session_id'] = "session_content"
    content = request.session['session_id']
    return render(request, 'myapp/show.html', {'content': content})
```

```
$ python manage.py runserver
```

在浏览器中查看“127.0.0.1:8000/myapp/use_session/”。

# 9 发送邮件

```python
# myapp/urls.py

urlpatterns = [
    path('send_email/', views.send_email, name='send_email'),
]
```

```python
# mysite/setting.py

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_USE_TLS = False  
EMAIL_USE_SSL = True 
EMAIL_HOST = 'smtp.qq.com' 
EMAIL_PORT = 465 
EMAIL_HOST_USER = '342752420@qq.com'  
EMAIL_HOST_PASSWORD = '************'   # qq授权码
```

```python
# myapp/views.py

def send_email(request):
    from django.core.mail import send_mail  
    send_mail(
        'Subject here', 
        'Here is the message.', 
        '342752420@qq.com',
        ['342752420@qq.com'], 
        fail_silently=False
    )
    return render(request, 'myapp/show.html', {'msg': "邮件已发送"})
```

# 10 使用认证

## 10.1 背景知识

**安装（缺省已安装）**

```python
#mysite/setting.py

INSTALLED_APPS = [
    'django.contrib.auth',
]
```

**URLs**

```python
accounts/login/ [name='login']
accounts/logout/ [name='logout']
accounts/password_change/ [name='password_change']
accounts/password_change/done/ [name='password_change_done']
accounts/password_reset/ [name='password_reset']
accounts/password_reset/done/ [name='password_reset_done']
accounts/reset/<uidb64>/<token>/ [name='password_reset_confirm']
accounts/reset/done/ [name='password_reset_complete']
```

其中的命名url可以在其它视图、模板中直接引用，也可以通过urls.py文件统一引用。

**模板**

缺省的认证系统只包含URLs、views、forms，不含模板，用户需要自行准备模板。

视图与模板的对应关系如下：

```python
class LoginView | registration/login.html
class LogoutView | registration/logout.html
class PasswordChangeView | registration/password_change_form.html
class PasswordChangeDoneView | registration/password_change_done.html
class PasswordResetView | registration/password_reset_form.html
class PasswordResetDoneView | registration/password_reset_done.html
class PasswordResetConfirmView | registration/password_reset_confirm.html
class PasswordResetCompleteView | registration/password_reset_complete.html
```

## 10.2 模板的位置——项目

```python
# mysite/setting.py

TEMPLATES = [
    {
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # 模板文件路径

    },
]
```

```python
# mysite/urls.py

from django.urls import include
urlpatterns = [
    path('accounts/', include('django.contrib.auth.urls')), # 导入django自带的认证视图路径
]
# myapp/urls.py中相应项目注释掉

```

{% raw %}
```html
<!--mysite/templates/registration/login.html-->
<form class="form" action="{% url 'login' %}" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">登录</button>
    <input type="hidden" name="next" value="{{ next }}"/>
</form>
```
{% endraw %}

访问http://127.0.0.1:8000/accounts/login/，显示登录页面。

## 10.3 模板的位置——app

```python
# mysite/setting.py

TEMPLATES = [
    {
        'DIRS': [], # 模板文件路径

    },
]
```

```python
# myapp/urls.py

from django.urls import include
urlpatterns = [
    path('accounts/', include('django.contrib.auth.urls')), # 导入django自带的认证视图路径
]
# mysite/urls.py中相应项目注释掉

```

{% raw %}
```html
<!--myapp/templates/registration/login.html-->
<form class="form" action="{% url 'myapp:login' %}" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">登录</button>
    <input type="hidden" name="next" value="{{ next }}"/>
</form>
```
{% endraw %}

访问http://127.0.0.1:8000/myapp/accounts/login/，显示登录页面。

## 10.4 模板的位置——命名空间

```python
# mysite/setting.py

TEMPLATES = [
    {
        'DIRS': [], # 模板文件路径

    },
]
```

```python
# myapp/urls.py

from django.contrib.auth import views as auth_views
urlpatterns = [
    path(
        'accounts/login/', 
        auth_views.LoginView.as_view(template_name='myapp/registration/login.html'),
        name="login",
    ),
]
# mysite/urls.py中相应项目注释掉
# 将冲突的项目注释掉

```

{% raw %}
```html
<!--myapp/templates/myapp/registration/login.html-->
<form class="form" action="{% url 'myapp:login' %}" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">登录</button>
    <input type="hidden" name="next" value="{{ next }}"/>
</form>
```
{% endraw %}

访问http://127.0.0.1:8000/myapp/accounts/login/，显示登录页面。

# 11 用户注册

## 11.1 基本注册

```python
# myapp/urls.py

from . import register
urlpatterns = [
    path('register/', register.register, name="register"),
]
```

```
$ vim myapp/register.py
```

```python
# myapp/register.py

from django.shortcuts import render, redirect
from .forms import RegisterForm
from .models import User

def register(request):
    redirect_to = request.POST.get('next', request.GET.get('next', '')) # next是Django缺省认证系统要求的

    if request.method == 'POST': # 用户提交信息

        form = RegisterForm(request.POST) # 创建用户

        if form.is_valid(): # 数据合法

            form.save() # 保存用户

            if redirect_to:
                return redirect(redirect_to)
            else:
                return redirect('/')
    else: # 用户未提交数据

        form = RegisterForm() # 创建空表单
    
    return render(request, 'myapp/register/register.html', context={'form': form, 'next': redirect_to}) # 渲染表单
```

```python
# myapp/forms.py

from django.contrib.auth.forms import UserCreationForm
from .models import User
class RegisterForm(UserCreationForm):
    class Meta(UserCreationForm.Meta):
        model = User
        fields = ("username", "email")
```

```
$ mkdir myapp/templates/myapp/register
$ vim myapp/templates/myapp/register/register.html
```

{% raw %}
```html
<!--myapp/templates/myapp/register/register.html-->
<form class="form" action="{% url 'myapp:register' %}" method="post">
    {% csrf_token %}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">注册</button>
    <input type="hidden" name="next" value="{{ next }}"/>
</form>
```
{% endraw %}

```
$ python manage.py runserver
```

访问http://127.0.0.1:8000/myapp/register/，可以注册用户。

## 13.2 使用验证器

前节实现的注册功能，email字段允许留空，一个email可以注册多个账户。

本节对email的有效性进行验证，并拒绝重复注册。

```python
# myapp/forms.py

from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.core.validators import EmailValidator    (+)
from .validators import validate_email_exists    (+)

class RegisterForm(UserCreationForm):
    email = forms.CharField(max_length=50, required=True, validators=[EmailValidator, validate_email_exists])    (+)
    class Meta(UserCreationForm.Meta):
        model = User
        fields = ("username", "email")
```

```python
# myapp/validators.py

from django.core.exceptions import ValidationError
from django.utils.translation import gettext_lazy as _
from .models import User

def validate_email_exists(email):
    count = User.objects.filter(email=email).count()
    if count > 0 :
        raise ValidationError(
            _('%(email)s is exists.'),
            params={'email': email},
        )
```

# 14 实现完整的应用

前面各节从零开始描述了实现用户注册、登录程序的基本方法，本节在这些基础上制作完整的用户注册、登录程序。

## 14.1 文件结构

```
.
├── db.sqlite3
├── manage.py
├── myapp
│   ├── admin.py
│   ├── apps.py
│   ├── forms.py
│   ├── models.py
│   ├── register.py
│   ├── templates
│   │   ├── index.html
│   │   ├── register
│   │   │   └── register.html
│   │   └── registration
│   │       ├── login.html
│   │       ├── password_change_done.html
│   │       ├── password_change_form.html
│   │       ├── password_reset_complete.html
│   │       ├── password_reset_confirm.html
│   │       ├── password_reset_done.html
│   │       └── password_reset_form.html
│   ├── tests.py
│   ├── urls.py
│   ├── validators.py
│   └── views.py
└── mysite
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

## 14.2 文件内容

### 14.2.1 项目级别的文件

```python
# mysite/setting.py

INSTALLED_APPS = [
    'myapp.apps.MyappConfig',
]

# 国际化与本地化

LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
USE_I18N = True
USE_L10N = True
USE_TZ = True

# 用户认证

AUTH_USER_MODEL = 'myapp.User'

# email smtp

EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_USE_TLS = False  
EMAIL_USE_SSL = True 
EMAIL_HOST = 'smtp.qq.com' 
EMAIL_PORT = 465 
EMAIL_HOST_USER = '342752420@qq.com'  
EMAIL_HOST_PASSWORD = '********'
```

```python
# mysite/urls.py

from django.contrib import admin
from django.urls import path
from django.urls import include
urlpatterns = [
    path('admin/', admin.site.urls),
    path('auth/', include('myapp.urls')),
]
```

### 14.2.2 app级别的文件

```python
# myapp/admin.py

from django.contrib import admin
from .models import User
admin.site.register(User)
```

```python
# myapp/apps.py

from django.apps import AppConfig
class MyappConfig(AppConfig):
    name = 'myapp'
```

```python
# myapp/forms.py

from django import forms
from django.contrib.auth.forms import UserCreationForm
from .models import User
from django.core.validators import EmailValidator
from .validators import validate_email_exists

class RegisterForm(UserCreationForm):
    email = forms.CharField(max_length=50, required=True, validators=[EmailValidator, validate_email_exists])
    class Meta(UserCreationForm.Meta):
        model = User
        fields = ("username", "email")
```

```python
# myapp/models.py

from django.db import models
from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    class Meta(AbstractUser.Meta):
        pass
```

```python
# myapp/register.py

from django.shortcuts import render, redirect
from .forms import RegisterForm
from .models import User

def register(request):
    redirect_to = request.POST.get('next', request.GET.get('next', '')) # next是Django缺省认证系统要求的

    if request.method == 'POST': # 用户提交信息

        form = RegisterForm(request.POST) # 创建用户

        if form.is_valid(): # 数据合法

            form.save() # 保存用户

            if redirect_to:
                return redirect(redirect_to)
            else:
                return redirect('/')
    else: # 用户未提交数据

        form = RegisterForm() # 创建空表单
    
    return render(request, 'myapp/register/register.html', context={'form': form, 'next': redirect_to}) # 渲染表单
```

```python
# myapp/urls.py

from django.urls import path
from . import views
from django.urls import include
from django.contrib.auth import views as auth_views
from . import register

app_name = 'myapp'

urlpatterns = [
    path('', views.index, name="index"),    
    path('register/', register.register, name="register"),
    path('accounts/', include('django.contrib.auth.urls')),
]
```

```python
# myapp/validator.py

from django.core.exceptions import ValidationError
from django.utils.translation import gettext_lazy as _
from .models import User

def validate_email_exists(email):
    count = User.objects.filter(email=email).count()
    if count > 0 :
        raise ValidationError(
            _('%(email)s is exists.'),
            params={'email': email},
        )
```

```python
# myapp/views.py

from django.http import HttpResponse
from django.shortcuts import render
from django.contrib.auth import views as auth_views

def index(request):
    return render(request, 'myapp/index.html')
```
### 14.2.3 模板文件

**各个文件相同的html壳**

主要是设置css，可以替换成自己的css。

{% raw %}
```html
<!-- 各个文件相同的html壳 -->
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="ie=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <!-- 这一句各页不同 -->
    <title>首页</title>
    <link rel="stylesheet" href="https://unpkg.com/mobi.css/dist/mobi.min.css">
</head>
<body>
<div class="flex-center">
    <div class="container">
        <div>
            <!-- 各个文件的实质性内容在这里 -->
            <!-- 其余文件仅显示实质性内容 -->
        </div>
    </div>
</div>
</body>
</html>
```
{% endraw %}

**模板文件的实质性内容**

{% raw %}
```html
<!-- myapp/templates/index.html -->
<h3>权限管理</h3>
{% if user.is_authenticated %}
    <p>你已登录，欢迎你：<a href="#">{{ user.username }}</a></p>
    <button class="btn btn-default"><a href="{% url 'myapp:logout' %}?next={{ request.path }}">注销登录</a>
    </button>
    <button class="btn btn-default"><a href="{% url 'myapp:password_change' %}?next={{ request.path }}">修改密码</a>
    </button>
{% else %}
    <p>你还没有登录，请
        <button class="btn btn-default"><a href="{% url 'myapp:login' %}?next={{ request.path }}">登录</a>
        </button>
        或者
        <button class="btn btn-default"><a href="{% url 'myapp:register' %}?next={{ request.path }}">注册</a>
        </button>
    </p>
{% endif %}
```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/register/register.html -->
<h3>注册</h3>
<form class="form" action="{% url 'myapp:register' %}" method="post">
    {% csrf_token %}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">注册</button>
    <input type="hidden" name="next" value="{{ next }}"/>
</form>
<div class="flex-center top-gap text-small">
    <a href="{% url 'login' %}">已有账号登录</a>
</div>
```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/login.html -->
<h3>登录</h3>
<form class="form" action="{% url 'myapp:login' %}" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">登录</button>
    <input type="hidden" name="next" value="{{ next }}"/>
</form>
<div class="flex-left top-gap text-small">
    <div class="unit-2-3"><span>没有账号？<a href="{% url 'myapp:register' %}">立即注册</a></span></div>
    <div class="unit-1-3 flex-right"><span><a href="{% url 'myapp:password_reset' %}">忘记密码？</a></span></div>
</div>

```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/password_change_done.html -->
<h3>修改密码</h3>
<p>密码修改成功！</p>
```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/password_change_form.html -->
<h3>修改密码</h3>
<form class="form" action="{% url 'myapp:password_change' %}" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">确认修改</button>
</form>

```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/password_reset_complete.html -->
<h3>新密码设置成功</h3>
<p>你的口令己经设置。现在你可以继续进行登录。</p>
```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/password_reset_confirm.html -->
<h3>设置新密码</h3>
<form class="form" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">提交</button>
</form>

```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/password_reset_done.html -->
<h3>密码重置链接已经发送</h3>
<p>如果您输入的邮件地址所对应的账户存在，设置密码的提示已经发送邮件给您，您将很快收到。</p>
<p>如果你没有收到邮件, 请确保您所输入的地址是正确的, 并检查您的垃圾邮件文件夹.</p>
```
{% endraw %}

{% raw %}
```html
<!-- myapp/templates/registration/password_reset_form.html -->
<h3>重置密码</h3>
<form class="form" action="{% url 'myapp:password_reset' %}" method="post">
    {% csrf_token %}
    {{ form.non_field_errors }}
    {% for field in form %}
        {{ field.label_tag }}
        {{ field }}
        {{ field.errors }}
        {% if field.help_text %}
            <p class="help text-small text-muted">{{ field.help_text|safe }}</p>
        {% endif %}
    {% endfor %}
    <button type="submit" class="btn btn-primary btn-block">提交</button>
</form>
```
{% endraw %}

# 15 参考文献

- [1] Django.官方文档 https://docs.djangoproject.com/en/2.1/contents/

- [2] zmrenwu.github.django-auth-example https://github.com/xiefeisd/django-auth-example