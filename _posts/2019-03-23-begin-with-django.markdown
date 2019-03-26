---
layout:     post
title:      "初学django"
subtitle:   "官方教程的工作流"
date:       2019-03-23
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - django
---

作者跟随django的官方教程，完成第一个django项目。

本文将过程中的工作流进行记录，对具体功能不作详细解释。

# Part 1 HelloWorld

## 1 创建项目

```
$ python -m django --version
$ django-admin startproject mysite
$ python manage.py runserver
$ python manage.py startapp polls
```

```python
#polls/views.py

from django.http import HttpResponse
def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

```python
#polls/urls.py

from django.urls import path
from . import views
urlpatterns = [
    path('', views.index, name='index'),
]
```

```python
#mysite/urls.py

from django.contrib import admin
from django.urls import include, path
urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

```
$ python manage.py runserver
```

在浏览器中查看“ip:port/”。

# Part 2 使用数据库

## 2 安装数据库

```
$ python manage.py migrate
```

## 3 创建模型

```python
#polls/models.py

from django.db import models
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```

```python
#mysite/settings.py

INSTALLED_APPS = [
    'polls.apps.PollsConfig', # 新增此行

    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

```
$ python manage.py makemigrations polls
$ python manage.py sqlmigrate polls 0001
$ python manage.py migrate
```

## 4 通过命令行使用API

```python
$ python manage.py shell
>>> from polls.models import Choice, Question
>>> Question.objects.all()
>>> from django.utils import timezone
>>> q = Question(question_text="What's new?", pub_date=timezone.now())
>>> q.save()
>>> q.id
>>> q.question_text
>>> q.pub_date
>>> q.question_text = "What's up?"
>>> q.save()
>>> Question.objects.all()
```

```python
#polls/models.py

import datetime
from django.db import models
from django.utils import timezone
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    # 新增成员函数 \\
    def __str__(self):
        return self.question_text
    # 新增成员函数 \\
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    # 新增成员函数 \\
    def __str__(self):
        return self.choice_text
```

```python
$ python manage.py shell
>>> from polls.models import Choice, Question
>>> Question.objects.all()
>>> Question.objects.filter(id=1)
>>> Question.objects.filter(question_text__startswith='What')
>>> from django.utils import timezone
>>> current_year = timezone.now().year
>>> Question.objects.get(pub_date__year=current_year)
>>> Question.objects.get(id=2)
>>> Question.objects.get(pk=1)
>>> q = Question.objects.get(pk=1)
>>> q.was_published_recently()
>>> q = Question.objects.get(pk=1)
>>> q.choice_set.all()
>>> q.choice_set.create(choice_text='Not much', votes=0)
>>> q.choice_set.create(choice_text='The sky', votes=0)
>>> c = q.choice_set.create(choice_text='Just hacking again', votes=0)
>>> c.question
>>> q.choice_set.all()
>>> q.choice_set.count()
>>> Choice.objects.filter(question__pub_date__year=current_year)
>>> c = q.choice_set.filter(choice_text__startswith='Just hacking')
>>> c.delete()
>>> q.choice_set.count()
```

## 5 后台管理

```python
$ python manage.py createsuperuser
Username: admin
Email address: admin@example.com
Password: **********
Password (again): *********
```

```
$ python manage.py runserver
```

```python
#polls/admin.py

from django.contrib import admin
from .models import Question
admin.site.register(Question)
```
在浏览器中查看“/admin/”。

# Part 3 使用视图

## 6 制作更多视图

```python
#polls/views.py

from django.shortcuts import render
from django.http import HttpResponse
def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
# 以下为新增 \\
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)
def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)
def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

```python
#polls/urls.py

from django.urls import path
from . import views
urlpatterns = [
    path('', views.index, name='index'),
    # 以下为新增 \\
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

在浏览器中查看“/polls/34/”、“/polls/34/results/”、“/polls/34/vote/”。

## 7 让视图做事

```python
#polls/views.py

from django.shortcuts import render
from django.http import HttpResponse
from .models import Question # 新增 \\
# 修改了此函数 \\
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ', '.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)
def detail(request, question_id):
    return HttpResponse("You're looking at question %s." % question_id)
def results(request, question_id):
    response = "You're looking at the results of question %s."
    return HttpResponse(response % question_id)
def vote(request, question_id):
    return HttpResponse("You're voting on question %s." % question_id)
```

通过浏览器访问polls。

### 7.1 使用模板
```
$ mkdir polls/templates
$ mkdir polls/templates/polls
```


{% raw %}
```html
<!--polls/templates/polls/index.html-->
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```
{% endraw %}

```python
#polls/views.py

from django.shortcuts import render
from django.http import HttpResponse
from .models import Question 
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    # 以下为更新 \\
    template = loader.get_template('polls/index.html')
    context = {'latest_question_list': latest_question_list}
    return HttpResponse(template.render(context, request))
```

通过浏览器访问polls。

**reder的简写**

```python
#polls/views.py

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

## 8 引发404错误

```python
#polls/views.py

from django.http import Http404
from django.shortcuts import render
from .models import Question
def detail(request, question_id):
    try:
        question = Question.objects.get(pk=question_id)
    except Question.DoesNotExist:
        raise Http404("Question does not exist")
    return render(request, 'polls/detail.html', {'question': question})
```

{% raw %}
```html
<!--polls/templates/polls/detail.html-->
{{ question }}
```
{% endraw %}

通过浏览器访问polls/1/。
通过浏览器访问polls/3/。（404）

**技巧： 404的简写**

```python
#polls/views.py

from django.shortcuts import get_object_or_404, render
from .models import Question
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```

## 9 使用模板系统

{% raw %}
```html
<!--polls/templates/polls/detail.html-->
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }}</li>
{% endfor %}
</ul>
```
{% endraw %}

## 10 删除模板中的硬编码

将：

{% raw %}
```html
<!--polls/index.html-->
<li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
```
{% endraw %}

改为：
{% raw %}
```html
<!--polls/index.html-->
<li><a href="\{\% url 'detail' question.id \%\}">{{ question.question_text }}</a></li>
```
{% endraw %}

其中detail由如下的name进行定义：

```python
#polls.urls.python

path('<int:question_id>/', views.detail, name='detail'),
```

## 11 URL命名空间

```python
#polls/urls.py

from django.urls import path
from . import views
app_name = 'polls' # 新增

urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

相应的，index.html对其的引用方式，从：

{% raw %}
```html
<!--polls/templates/polls/index.html-->
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
```
{% endraw %}

改为：

{% raw %}
```html
<!--polls/templates/polls/index.html-->
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
```
{% endraw %}

# Part 4 使用表单

## 12 写一个表单

### 12.1 创建表单

{% raw %}
```html
<!--polls/templates/polls/detail.html-->
<h1>{{ question.question_text }}</h1>
{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```
{% endraw %}

**代码解释**

POST 数据： choice=id。

修改服务端数据时，使用 method="post"，防恶意访问。

forloop.counter：liquid的循环计数。

{% raw %}
{% csrf_token %}：模板标记，用于防止恶意伪造请求。
{% endraw %}

**缺失的选项**

完成以上表单以后，通过浏览器查看/polls/1/，一切正常，但是/polls/2/的选项按钮是空白。

这是因为，在“通过命令行使用API”一节，对第2个问卷没有创建选项。

使Choice对管理后台可见：

```python
# polls/admin.py

from django.contrib import admin
from .models import Question, Choice
admin.site.register(Question)
admin.site.register(Choice) #增加此行

```

对第2个Question添加Choice：

```python
$ python manage.py shell
>>> from polls.models import Choice, Question
>>> Question.objects.get(pk=2)
>>> q = Question.objects.get(pk=2)
>>> q.choice_set.all()
>>> q.choice_set.create(choice_text='Not much', votes=0)
>>> q.choice_set.create(choice_text='The sky', votes=0)
>>> q.choice_set.all()
```

### 12.2 处理表单

```python
# polls/views.py

# 新引入 HttpResponseRedirect、reverse、Choice

from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from .models import Choice, Question
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
# 更新处理表单之后的回显函数

def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
# 更新处理表单的函数

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        # 出错，返回问卷

        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        # 跳转，防止用户点击“返回”按钮导致重复提交

        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

{% raw %}
```html
<!--polls/templates/polls/results.html-->
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>
<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```
{% endraw %}

**解决并发**

以上函数存在问题，2个用户同时操作数据库时，会导致数据丢失。

把：

```python
# polls/views.py
selected_choice.votes += 1
```

改为：

```python
# polls/views.py
from django.db.models import F
selected_choice.votes = F('votes') + 1
```

## 13 小结

到目前为止，程序已经具备基本功能。对目前的文件做一下总结。

```bash
.
├── db.sqlite3
├── manage.py
├── mysite
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── polls
    ├── admin.py
    ├── apps.py
    ├── migrations
    │   └── 0001_initial.py
    ├── models.py
    ├── templates
    │   └── polls
    │       ├── detail.html
    │       ├── index.html
    │       └── results.html
    ├── tests.py
    ├── urls.py
    └── views.py
```

```python
# mysite/urls.py

from django.contrib import admin
from django.urls import include, path
urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/', admin.site.urls),
]
```

```python
# polls/admin.py

from django.contrib import admin
from .models import Question, Choice
admin.site.register(Question)
admin.site.register(Choice)
```

```python
# polls/apps.py

from django.apps import AppConfig
class PollsConfig(AppConfig):
    name = 'polls'
```

```python
# polls/modules

import datetime
from django.db import models
from django.utils import timezone
class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    def __str__(self):
        return self.question_text
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)
class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
    def __str__(self):
        return self.choice_text
```

```python
# polls/views.py

from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from .models import Choice, Question
from django.db.models import F
def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
def results(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/results.html', {'question': question})
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes = F('votes') + 1
        selected_choice.save()
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

```python
# polls/urls.py

from django.urls import path
from . import views
app_name = 'polls'
urlpatterns = [
    path('', views.index, name='index'),
    path('<int:question_id>/', views.detail, name='detail'),
    path('<int:question_id>/results/', views.results, name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

{% raw %}
```html
<!--polls/templates/polls/index.html-->
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```
{% endraw %}

{% raw %}
```html
<!--polls/templates/polls/detail.html-->
<h1>{{ question.question_text }}</h1>
{% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    <input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}">
    <label for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br>
{% endfor %}
<input type="submit" value="Vote">
</form>
```
{% endraw %}

{% raw %}
```html
<!--polls/templates/polls/results.html-->
<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
{% endfor %}
</ul>
<a href="{% url 'polls:detail' question.id %}">Vote again?</a>
```
{% endraw %}

## 14 使用通用视图


```python
# mysite/urls.py

from django.urls import path
from . import views
app_name = 'polls'
urlpatterns = [
    # 对函数的引用，改成对类的引用

    path('', views.IndexView.as_view(), name='index'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    # 这个不变

    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```

```python
# polls/views.py

from django.http import HttpResponse, HttpResponseRedirect
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from .models import Choice, Question
from django.db.models import F
from django.views import generic # 新增
# 3个函数改成3个类

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'
    def get_queryset(self):
        return Question.objects.order_by('-pub_date')[:5]
class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'
class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'
# 本函数保持不变

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes = F('votes') + 1
        selected_choice.save()
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```

# Part 5 测试

## 15 第一个测试

### 15.1 发现错误

```python
$ python manage.py shell
>>> import datetime
>>> from django.utils import timezone
>>> from polls.models import Question
>>> future_question = Question(pub_date=timezone.now() + datetime.timedelta(days=30))
>>> future_question.was_published_recently()
```

对于“未来的”，误判为“当前的”，这是个bug。

### 15.2 创建测试

```python
# polls/tests.py

import datetime
from django.test import TestCase
from django.utils import timezone
from .models import Question

class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        """对未来问卷返回False"""
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
```

### 15.3 运行测试

```
$ python manage.py test polls
```

阅读回显信息，定位问题所在。

### 15.4 解决问题

```python
# polls/models.py

# 修改此函数， 其余内容略

def was_published_recently(self):
    now = timezone.now()
    return now - datetime.timedelta(days=1) <= self.pub_date <= now
```

```
$ python manage.py test polls
```

### 15.5 更多测试

```python
# polls/tests.py

import datetime
from django.test import TestCase
from django.utils import timezone
from .models import Question

class QuestionModelTests(TestCase):
    def test_was_published_recently_with_future_question(self):
        """对未来问卷返回False"""
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertIs(future_question.was_published_recently(), False)
    # 新增内容

    def test_was_published_recently_with_old_question(self):
        """对1天前的问卷返回False"""
        time = timezone.now() - datetime.timedelta(days=1, seconds=1)
        old_question = Question(pub_date=time)
        self.assertIs(old_question.was_published_recently(), False)
    def test_was_published_recently_with_recent_question(self):
        """对当天问卷返回True"""
        time = timezone.now() - datetime.timedelta(hours=23, minutes=59, seconds=59)
        recent_question = Question(pub_date=time)
        self.assertIs(recent_question.was_published_recently(), True)
```

```
$ python manage.py test polls
```

## 16 测试视图

### 16.1 发现问题

```python
$ python manage.py shell
>>> from django.test.utils import setup_test_environment
>>> setup_test_environment()
>>> from django.test import Client
>>> client = Client()
>>> response = client.get('/')
Not Found: /
>>> response.status_code
404
>>> from django.urls import reverse
>>> response = client.get(reverse('polls:index'))
>>> response.status_code
200
>>> response.content
>>> response.context['latest_question_list']
```

测试显示，“未来”的问卷被显示。

### 16.2 解决问题

```python
# polls/views.py

# 其余部分维持不变

from django.utils import timezone # 新增

class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'
    def get_queryset(self):
        # 修改此句

        return Question.objects.filter( pub_date__lte=timezone.now() ).order_by('-pub_date')[:5]
```

### 16.3 重新测试

**创建测试文件**

```python
#polls/tests.py

# 仅显示新增内容
from django.urls import reverse
def create_question(question_text, days):
    """准备“未来”问卷"""
    time = timezone.now() + datetime.timedelta(days=days)
    return Question.objects.create(question_text=question_text, pub_date=time)
class QuestionIndexViewTests(TestCase):
    def test_no_questions(self):
        """测试空列表"""
        response = self.client.get(reverse('polls:index'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])
    def test_past_question(self):
        """过去的问卷，应当显示"""
        create_question(question_text="Past question.", days=-30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )
    def test_future_question(self):
        """未来的问卷，应当不显示"""
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertContains(response, "No polls are available.")
        self.assertQuerysetEqual(response.context['latest_question_list'], [])
    def test_future_question_and_past_question(self):
        """过去的和未来的一起测试，应只显示过去的"""
        create_question(question_text="Past question.", days=-30)
        create_question(question_text="Future question.", days=30)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question.>']
        )
    def test_two_past_questions(self):
        """多个问卷一起显示"""
        create_question(question_text="Past question 1.", days=-30)
        create_question(question_text="Past question 2.", days=-5)
        response = self.client.get(reverse('polls:index'))
        self.assertQuerysetEqual(
            response.context['latest_question_list'],
            ['<Question: Past question 2.>', '<Question: Past question 1.>']
        )
```

**执行测试**

```
$ python manage.py test polls
```

### 16.4 测试详情视图

为了避免“未来”问卷被错误访问。

```python
# polls/views.py

# 其余部分保持不变

class DetailView(generic.DetailView):
    ...
    def get_queryset(self):
        # 修改此句
        return Question.objects.filter(pub_date__lte=timezone.now())
```

```python
# polls/tests.py

# 仅显示新增部分

class QuestionDetailViewTests(TestCase):
    def test_future_question(self):
        """对未来问卷显示404"""
        future_question = create_question(question_text='Future question.', days=5)
        url = reverse('polls:detail', args=(future_question.id,))
        response = self.client.get(url)
        self.assertEqual(response.status_code, 404)

    def test_past_question(self):
        """对过去问卷予以显示"""
        past_question = create_question(question_text='Past Question.', days=-5)
        url = reverse('polls:detail', args=(past_question.id,))
        response = self.client.get(url)
        self.assertContains(response, past_question.question_text)
```

```
$ python manage.py test polls
```

# Part 6 自定义应用程序的外观

## 17 使用css

```
$ mkdir polls/static
$ mkdir polls/static/polls
```

```css
/* polls/static/polls/style.css */

li a {
    color: green;
}
```

{% raw %}
```html
<!--polls/templates/polls/index.html-->
{% load static %}
<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}">
```
{% endraw %}

```
$ python manage.py runserver
```

## 18 使用图像文件

```
$ mkdir polls/static/polls/images
```

在polls/static/polls/images下放一张图片，命名为background.png。

```css
/* polls/static/polls/style.css */
body {
    background: white url("images/background.png") no-repeat;
}
```

静态文件只使用相对路径。

# Part 7 自定义管理后台

## 19 自定义管理表单

```python
#polls/admin.py

from django.contrib import admin
from .models import Question
class QuestionAdmin(admin.ModelAdmin):
    fields = ['pub_date', 'question_text']
admin.site.register(Question, QuestionAdmin)
```

```python
#polls/admin.py

from django.contrib import admin
from .models import Question
class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date']}),
    ]
admin.site.register(Question, QuestionAdmin)
```

## 20 添加相关元素

```python
#polls/admin.py

from django.contrib import admin
from .models import Choice, Question
class ChoiceInline(admin.StackedInline):
    model = Choice
    extra = 3
class QuestionAdmin(admin.ModelAdmin):
    fieldsets = [
        (None,               {'fields': ['question_text']}),
        ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
    ]
    inlines = [ChoiceInline]
admin.site.register(Question, QuestionAdmin)
```

**使用表格显示**

把：

```python
#polls/admin.py

class ChoiceInline(admin.StackedInline):
```

改为：

```python
#polls/admin.py

class ChoiceInline(admin.TabularInline):
```

## 21 自定义管理员更改列表

### 21.1 在问卷列表显示更多信息

```python
#polls/admin.py

class QuestionAdmin(admin.ModelAdmin)::
    # 添加此行

    list_display = ('question_text', 'pub_date', 'was_published_recently')
```

### 21.2 m美化信息的显示方式

```python
# polls/models.py

class Question(models.Model):
    def was_published_recently(self):
        now = timezone.now()
        return now - datetime.timedelta(days=1) <= self.pub_date <= now
    # 增加以下三行

    was_published_recently.admin_order_field = 'pub_date'
    was_published_recently.boolean = True
    was_published_recently.short_description = 'Published recently?'
```

### 21.3 使用侧边栏

```python
#polls/admin.py

class QuestionAdmin(admin.ModelAdmin)::
    # 添加此行
    list_filter = ['pub_date']
```

### 21.4 使用搜索

```python
#polls/admin.py

class QuestionAdmin(admin.ModelAdmin)::
    # 添加此行
    search_fields = ['question_text']
```

## 22 自定义管理员外观

### 22.1 定制项目模板

```python
#mysite/settings.py

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')], # 修改此行

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

```
$ mkdir templates/admin
```

把django/contrib/admin/templates/admin/base_site.html 拷贝到以上目录。

**查看django路径**
```
$ python -c "import django; print(django.__path__)"
```

编辑该文件，把：

{% raw %}
```html
<!--ptemplates/admin/base_site.html-->
{% block branding %}
<h1 id="site-name"><a href="{% url 'admin:index' %}">{{ site_header|default:_('Mysite administration') }}</a></h1>
{% endblock %}
```
{% endraw %}

{% raw %}
```html
<!--templates/admin/base_site.html-->
{% block branding %}
<h1 id="site-name"><a href="{% url 'admin:index' %}">Polls Administration</a></h1>
{% endblock %}
```
{% endraw %}

### 22.2 定制应用程序模板

'DIRS'留空时，模板路径应为polls/templates/admin。

若使用polls/templates/polls/admin，则应使用'DIRS'定位到polls/templates/polls。

# Part 8 打包发布

## 23 准备工作

### 23.1 目前的文件结构

```
$ tree -I "__pycache__|db*"
```

```
.
├── manage.py
├── mysite
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── polls
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   ├── 0001_initial.py
│   │   └── __init__.py
│   ├── models.py
│   ├── static
│   │   └── polls
│   │       ├── images
│   │       │   └── background.png
│   │       └── style.css
│   ├── templates
│   │   └── polls
│   │       ├── detail.html
│   │       ├── index.html
│   │       └── results.html
│   ├── tests.py
│   ├── urls.py
│   └── views.py
└── templates
    └── admin
        └── base_site.html
```

### 23.2 安装需要的工具

```
$ sudo apt-get install pyton3-pip
```

```
$ sudo apt-get install python3-setuptools
```

## 24 打包应用

### 24.1 准备文件夹
```
$ mkdir django-polls /* polls的同级目录 */
```

### 24.2 复制文件

```
$ cp polls django-polls -R
```

### 24.3 准备README.rst

```
django-polls/README.rst

=====
Polls
=====

Polls is a simple Django app to conduct Web-based polls. For each
question, visitors can choose between a fixed number of answers.

Detailed documentation is in the "docs" directory.

Quick start
-----------

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    path('polls/', include('polls.urls')),

3. Run `python manage.py migrate` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```

### 24.4 准备LICENSE

```
django-polls/LICENSE

```

### 24.5 准备setup.py

```python
#django-polls/setup.py

import os
from setuptools import find_packages, setup
with open(os.path.join(os.path.dirname(__file__), 'README.rst')) as readme:
    README = readme.read()
# allow setup.py to be run from any path
os.chdir(os.path.normpath(os.path.join(os.path.abspath(__file__), os.pardir)))
setup(
    name='django-polls',
    version='0.1',
    packages=find_packages(),
    include_package_data=True,
    license='BSD License',  # example license
    description='A simple Django app to conduct Web-based polls.',
    long_description=README,
    url='https://www.example.com/',
    author='Your Name',
    author_email='yourname@example.com',
    classifiers=[
        'Environment :: Web Environment',
        'Framework :: Django',
        'Framework :: Django :: 3.7',  # replace "X.Y" as appropriate
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License',  # example license
        'Operating System :: OS Independent',
        'Programming Language :: Python',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: 3.6',
        'Topic :: Internet :: WWW/HTTP',
        'Topic :: Internet :: WWW/HTTP :: Dynamic Content',
    ],
)
```


### 24.6 准备MANIFEST.in

```
django-polls/MANIFEST.in

include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
recursive-include docs *
```

### 24.7 打包

```
$ python setup.py sdist
```

## 25 使用软件包

**安装**

```
$ pip install --user django-polls/dist/django-polls-0.1.tar.gz
```

**启动**

启动之前把用于开发的polls目录重命名，用来验证打包、安装是否成功。
```
$ python manage.py runserver
```

**卸载**

```
$ pip uninstall django-polls -y
```



