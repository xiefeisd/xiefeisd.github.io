---
layout:     post
title:      " Django 2.1 官方文档翻译"
subtitle:   "3 使用django"
date:       2019-04-01
author:     "xiefeisd"
header-img: "img/post-bg.jpg"
tags:
    - django
---



# 1.  Django 文档

略

# 2. 开始

略

# 3. 使用  Django 

## 3.1. 安装  Django 

### 3.1.1. 安装 Python

### 3.1.2. 安装 Apache 和 mod_wsgi

如果您只是想体验Django，请跳到下一节。Django包含一个轻量级web服务器，您可以使用它进行测试，因此在准备将Django部署到生产环境中之前，不需要设置Apache。

如果您想在生产站点上使用Django，请使用带有 `mod_wsgi` 的Apache。`mod_wsgi`以两种模式之一运行:嵌入式模式或守护进程模式。在嵌入式模式下，`mod_wsgi`类似于`mod_perl`——它将Python嵌入Apache中，并在服务器启动时将Python代码加载到内存中。在Apache进程的整个生命周期中，代码都保存在内存中，这比其他服务器安排带来了显著的性能提升。在守护进程模式下，`mod_wsgi`生成一个处理请求的独立守护进程。守护进程可以作为与Web服务器不同的用户运行，这可能会提高安全性。可以在不重新启动整个Apache Web服务器的情况下重新启动守护进程，这可能会使刷新代码库变得更加无缝。请参考`mod_wsgi`文档，以确定哪种模式适合您的设置。确保已安装Apache并激活`mod_wsgi`模块。Django可以使用任何支持`mod_wsgi`的Apache版本。

有关安装`mod_wsgi`之后如何配置`mod_wsgi`的信息，请参阅如何将Django与`mod_wsgi`一起使用。

如果由于某种原因不能使用`mod_wsgi`，请不要担心:Django支持许多其他部署选项。一个是`uWSGI`;它在`nginx`上运行得很好。此外，Django遵循WSGI规范(PEP 3333)，该规范允许Django在各种服务器平台上运行。


### 3.1.3. 运行数据库
### 3.1.4. 安装  Django 
#### 3.1.4.1. 使用pip安装官方版
#### 3.1.4.2. 安装发行版
#### 3.1.4.3. 安装开发版

## 3.2. 模型与数据库

### 3.2.1. 模型

模型是关于数据的唯一、确定的信息来源。它包含要存储的数据的基本字段和行为。通常，每个模型映射到一个数据库表。

基础知识:

- 每个模型都是一个 Python 类，它是`django.db.models.Model`的子类。

- 模型的每个属性表示一个数据库字段。

- 基于此， Django 为您提供了一个自动生成（访问语句）的数据库访问API。

#### 3.2.1.1. 简单的例子

这个例子定义了一个`Person`类，它包含 `first_name` 和 `last_name` 属性：

```python
from django.db import models
class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

`first_name` 和 `last_name` 是模型的字段，每个字段被指定为一个类属性，每个属性映射到一个数据库列。

上述 `Person` 类创建这样的数据库表：

```sql
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

一些技术说明：

- 表的名称`myapp_person`自动派生自一些模型元数据，但可以重写。

- 自动添加`id`字段，但可以重写此行为。

- 本例中的`CREATE TABLE SQL`使用`PostgreSQL`语法进行格式化，实际使用的`SQL`取决于设置文件中指定的数据库后端。

#### 3.2.1.2. 使用模型

一旦定义了模型，就需要告诉 Django 您将使用这些模型。通过编辑`setting.py`文件并更改 `INSTALLED_APPS` 设置来添加包含 `model.py` 的模块名称，可以做到这一点。

例如，如果应用程序的模型位于模块 `myapp.model` 中，`INSTALLED_APPS` 应该是这样的:

```python
INSTALLED_APPS = [
    #...
    'myapp',
    #...
]
```

添加 `INSTALLED_APPS` 以后，别忘了执行 `manage.py migrate`，可以选择性的先执行 `manage.py makemigrations` 以生成迁移。

#### 3.2.1.3. 字段

模型中最重要的部分(也是惟一必须的部分)是它定义的数据库字段列表。

字段由类属性指定。注意不要选择与模型API冲突的字段名，如`clean`、`save`、`delete`。

示例：

```python
from django.db import models
class Musician(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    instrument = models.CharField(max_length=100)
class Album(models.Model):
    artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
    name = models.CharField(max_length=100)
    release_date = models.DateField()
    num_stars = models.IntegerField()
```

##### 3.2.1.3.1. 字段类型

模型中的每个字段都应该是适当 `Field` 类的实例。 Django 使用字段类类型来确定一些事情:

- 列类型，它告诉数据库要存储什么类型的数据(例如`INTEGER`、`VARCHAR`、`TEXT`)。

- 呈现表单字段时使用的默认`HTML`小部件(例如`<input type="text">， <select>`)。

- 最基本的验证需求，用于 Django 的管理和自动生成的表单。

Django 有几十种内置的字段类型，如果 Django 的内置字段不能满足需要，还可以编写自己的字段。

##### 3.2.1.3.2. 字段选项

每个字段接受一组特定于字段的参数，例如，`CharField`(及其子类)需要`max_length`参数，该参数指定用于存储数据的 `VARCHAR` 数据库字段的大小。

还有一组对所有字段类型都可用的通用参数。都是可选的。下面是最常用的一些:

###### 3.2.1.3.2.1. null 

若为 `True`， Django 将在数据库中将空值存储为 `NULL`，缺省为 `False`。

###### 3.2.1.3.2.2. blank

若为 `True`，该字段允许为 `blank`。缺省为 `False`。
```
注意，这不同于`null`。`null`与数据库相关，`blank`与验证相关。 
如果字段的 `blank=True`，表单验证允许传入空值。如果字段的 `blank=False`，该字段是必须的。
```

###### 3.2.1.3.2.3. choices

一个可迭代的二元组列表，作为该字段的可选列表。

如果给出了这个选项，那么默认的表单小部件将是一个选择框，而不是标准文本字段，并且将选择限制在给出的选项中。

`choices`列表如下所示：

```python
YEAR_IN_SCHOOL_CHOICES = (
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
    ('GR', 'Graduate'),
)
```

每个元组中的第一个元素是将存储在数据库中的值。第二个元素由字段的表单小部件显示。

给定一个模型实例，可以使用`get_FOO_display()`方法访问带有选项的字段的显示值。例如:

```python
from django.db import models
class Person(models.Model):
    SHIRT_SIZES = (
        ('S', 'Small'),
        ('M', 'Medium'),
        ('L', 'Large'),
    )
name = models.CharField(max_length=60)
shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)
```

```
>>> p = Person(name="Fred Flintstone", shirt_size="L")
>>> p.save()
>>> p.shirt_size
'L'
>>> p.get_shirt_size_display()
'Large'
```

###### 3.2.1.3.2.4. default

该字段的缺省值，可以是一个值或者可调用对象。如果是可调用对象，将在每次创建新实例时被调用。

###### 3.2.1.3.2.5. help_text

由表单小部件展示的帮助文本。它对文档很有用，即使该字段没有用在表单上。

###### 3.2.1.3.2.6. primary_key

若为`True`，该字段将是该模型的主键。

如果不将任何字段指定为 `primary_key=True`,  Django 将自动添加一个 `IntegerField` 来保存主键，所以您不需要在任何字段上设置`primary_key=True`，除非您想覆盖默认的主键行为。

主键字段是只读的。如果更改现有对象上的主键值并保存它，将在旧对象旁边创建一个新对象。例如：

```python
from django.db import models
class Fruit(models.Model):
    name = models.CharField(max_length=100, primary_key=True)
```

```
>>> fruit = Fruit.objects.create(name='Apple')
>>> fruit.name = 'Pear'
>>> fruit.save()
>>> Fruit.objects.values_list('name', flat=True)
<QuerySet ['Apple', 'Pear']>
```

###### 3.2.1.3.2.7. unique

若为`True`，则该字段的值在全表不允许重复。

以上是关于模型字段选项的简短介绍，详情请参考API。

##### 3.2.1.3.3. 自动主键字段

默认的， Django 给每个模型赋予下面的字段：

```python
id = models.AutoField(primary_key=True)
```

这是一个自增主键。

如果希望指定自定义主键，只需在其中一个字段上指定 `primary_key=True`。显式地设置了字段 `primary_key` 之后， Django 将不再自动添加 `id` 列。

每个模型都需要一个字段 `primary_key=True` (显式声明或自动添加)。

##### 3.2.1.3.4. 字段详名

除了 `ForeignKey`、 `ManyToManyField`、 `OneToOneField`，每个字段类型都有一个可选的首位参数：详名。如果不提供详名， Django 将使用字段属性名自动生成一个，把其中的下划线替换成空格。

此例中，详名是`person's first name`：

```python
first_name = models.CharField("person's first name", max_length=30)
```

此例中，详名是`first name`：

```python
first_name = models.CharField(max_length=30)
```

`ForeignKey`、 `ManyToManyField`、 `OneToOneField` 的第一个参数必须是一个类，因此使用 `verbose_name` 关键字参数：

```python
poll = models.ForeignKey(
    Poll,
    on_delete=models.CASCADE,
    verbose_name="the related poll",
)
sites = models.ManyToManyField(Site, verbose_name="list of sites")
place = models.OneToOneField(
    Place,
    on_delete=models.CASCADE,
    verbose_name="related place",
)
```

惯例是 `verbose_name` 的第一个字母不大写。 Django 将自动将第一个字母大写。

##### 3.2.1.3.5. 关系

显然，关系数据库的功能在于将表彼此关联。 Django 提供了定义三种最常见的数据库关系类型的方法:多对一、多对多、一对一。

###### 3.2.1.3.5.1. 多对一关系

使用 `django.db.models.ForeignKey` 定义多对一关系。用法和其它字段类型一样，将其包含为模型的类属性。

`ForeignKey` 需要一个首位参数：一个与之相关联的类。

比如，一个 `Manufacturer` 制造很多 `Car`，一个 `Car` 只被一个 `Manufacturer` 制造，需如下定义：

```python
from django.db import models
class Manufacturer(models.Model):
    # ...
    pass
class Car(models.Model):
    manufacturer = models.ForeignKey(Manufacturer, on_delete=models.CASCADE)
    # ...
```

你也可以定义递归关系（一个类拥有一个多对一关系到它自身），以及关联到尚未定义的类，详见API。

建议，但不是必需的，`ForeignKey` 字段的名称(上面例子中的 `Manufacturer`)使用模型的名称，小写。当然，您可以随意命名字段。例如:

```python
class Car(models.Model):
    company_that_makes_it = models.ForeignKey( Manufacturer, on_delete=models.CASCADE )
    # ...
```

`ForeignKey` 字段接受许多额外参数，详见API。这些选项有助于定义关系应该如何工作，都是可选的。

###### 3.2.1.3.5.2. 多对多关系

使用 `ManyToManyField` 定义多对多关系。用法和其它字段类型一样，将其包含为模型的类属性。

`ManyToManyField` 需要一个首位参数：一个与之相关联的类。

比如，一个 `Pizza` 有多个 `Topping`，一个 `Topping` 可以在多个 `Pizza` 上，需如下定义：

```python
from django.db import models
class Topping(models.Model):
    # ...
    pass
class Pizza(models.Model):
    # ...
    toppings = models.ManyToManyField(Topping)
```

对于 `ManyToManyField`，也可以定义递归关系（一个类拥有一个多对一关系到它自身），以及关联到尚未定义的类，详见API。

建议，但不是必需的，`ManyToManyField` 的名称(在上面的例子中是 `toppings`)是复数，用于描述一组相关的模型对象。

哪个模型具有 `ManyToManyField` 并不重要，但是应该只将它放在其中一个模型中，而不是同时放在两个模型中。

通常，`ManyToManyField` 实例应该放在将在同一个表单上编辑的对象中。在上面的例子中，`toppings` 在 `Pizza` 中 (而不是让 `Topping` 有一个 `ManyToManyField`)，因为一个 `Pizza` 有多个 `Topping` 比一个 `Topping` 在多个 `Pizza` 上更自然。按照上面的设置方式，`Pizza` 表单允许用户选择 `Topping`。

`ManyToManyField`字段也接受一些额外的参数，详见 API。

###### 3.2.1.3.5.3. 多对多关系中的额外字段

当你只处理简单的多对多关系时，比如混合和匹配披萨和配料，一个标准的 `ManyToManyField` 就足够了。然而，有时您可能需要将数据与两个模型之间的关系关联起来。

例如，考虑一个情况，应用程序跟踪音乐家所属的音乐组。一个人和他所属的组之间存在多对多的关系，所以可以使用 `ManyToManyField` 来表示这种关系。然而，您可能希望收集的成员身份，有很多细节，比如这个人加入这个组的日期。

对于这些情况， Django 允许您指定模型，用于管理多对多关系。然后可以在中间模型上添加额外的字段。中间模型与 `ManyToManyField` 关联，使用 `through` 参数指向充当中介的模型。对于我们的音乐家示例，代码应该是这样的:

```python
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=128)
    def __str__(self):
        return self.name

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, through='Membership')
    def __str__(self):
        return self.name

class Membership(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    date_joined = models.DateField()
    invite_reason = models.CharField(max_length=64)
```

设置中介模型时，将涉及显式地为多对多关系的模型指定外键。这个显式声明定义了这两个模型之间的关系。

中间模型有一些限制:

- 中间模型必须包含且仅包含一个指向源模型的外键(在该例中是 `Group`)，或者必须使用 `ManyToManyField.through_fields` 显式指定 Django 应该用于关系的外键。如果有多个外键，并且没有指定 `through_fields`，则会引发验证错误。类似的限制也适用于目标模型的外键(在该例中是 `Person`)。

- 对于通过中介模型与自身具有多对多关系的模型，一个模型具有两个外键是允许的，但它们将被视为多对多关系的两个(不同的)方面。但是，如果有两个以上的外键，还必须指定 `through_fields`，否则将引发验证错误。

- 在使用中介模型定义模型到自身的多对多关系时，必须使用`symmetrical=False`，参见API。

既然已经设置了 `ManyToManyField` 来使用中介模型(在本例中是 `Membership`)，那么就可以开始创建一些多对多关系了。可以通过创建中间模型的实例来实现这一点:

```bash
>>> ringo = Person.objects.create(name="Ringo Starr")
>>> paul = Person.objects.create(name="Paul McCartney")
>>> beatles = Group.objects.create(name="The Beatles")
>>> m1 = Membership(person=ringo, group=beatles,
... date_joined=date(1962, 8, 16),
... invite_reason="Needed a new drummer.")
>>> m1.save()
>>> beatles.members.all()
<QuerySet [<Person: Ringo Starr>]>
>>> ringo.group_set.all()
<QuerySet [<Group: The Beatles>]>
>>> m2 = Membership.objects.create(person=paul, group=beatles,
... date_joined=date(1960, 8, 1),
... invite_reason="Wanted to form a band.")
>>> beatles.members.all()
<QuerySet [<Person: Ringo Starr>, <Person: Paul McCartney>]>
```

与普通的多对多字段不同，不能使用`add()`、`create()`、`set()`来创建关系：

```bash
>>> # 以下语句将出错
>>> beatles.members.add(john)
>>> beatles.members.create(name="George Harrison")
>>> beatles.members.set([john, paul, ringo, george])
```

为什么？不能仅仅创建 `Person` 和 `Group` 之间的关系，需要指定关系所有的细节，由 `Membership` 模型所要求。简单的`add`、`create`和`assignment`调用没有提供指定这个额外细节的方法。因此，对于使用中间模型的多对多关系，它们是禁用的。创建这种类型关系的惟一方法是创建中间模型的实例。

出于类似的原因禁用了`remove()`方法。例如，如果中间模型定义的定制 `through` 表没有在(`model1`, `model2`)对上强制惟一性，那么`remove()`调用将不能提供足够的信息，说明应该删除哪个中间模型实例：

```python
>>> Membership.objects.create(person=ringo, group=beatles,
... date_joined=date(1968, 9, 4),
... invite_reason="You've been gone for a month and we miss you.")
>>> beatles.members.all()
<QuerySet [<Person: Ringo Starr>, <Person: Paul McCartney>, <Person: Ringo Starr>]>
>>> # 此句出错，因为不能确定删除哪个关系
>>> beatles.members.remove(ringo)
```

然而，`clear()` 方法可用于删除一个实例的所有多对多关系：

```python
>>> # Beatles乐队解散了
>>> beatles.members.clear()
>>> # 注意这将删除中间模型实例
>>> Membership.objects.all()
<QuerySet []>
```

一旦通过创建中间模型的实例建立了多对多关系，就可以发出查询。与普通的多对多关系一样，可以使用多对多模型的属性进行查询：

```python
# 查找拥有一个名为'Paul'的成员的所有 Group
>>> Group.objects.filter(members__name__startswith='Paul')
<QuerySet [<Group: The Beatles>]>
```

当使用一个中间模型时，也可以查询它的属性：

```python
# 查找 Beatles 乐队中 1961年1月1日之后加入的所有成员
>>> Person.objects.filter(
... group__name='The Beatles',
... membership__date_joined__gt=date(1961,1,1))
<QuerySet [<Person: Ringo Starr]>
```

如需要查阅会员资料，可直接查询 `Membership` 模型：

```python
>>> ringos_membership = Membership.objects.get(group=beatles, person=ringo)
>>> ringos_membership.date_joined
datetime.date(1962, 8, 16)
>>> ringos_membership.invite_reason
'Needed a new drummer.'
```

访问相同信息的另一种方法是从 `Person` 对象查询多对多反向关系:

```python
>>> ringos_membership = ringo.membership_set.get(group=beatles)
>>> ringos_membership.date_joined
datetime.date(1962, 8, 16)
>>> ringos_membership.invite_reason
'Needed a new drummer.'
```

###### 3.2.1.3.5.4. 一对一关系

使用 `OneToOneField` 定义一对一关系。用法和其它字段类型一样，将其包含为模型的类属性。

`OneToOneField` 需要一个首位参数：一个与之相关联的类。

当对象以某种方式“扩展”另一个对象时，这对于对象的主键非常有用。

例如，如果正在构建一个关于“地点”的数据库，那么将在数据库中构建非常标准的东西，比如地址、电话号码等。然后，如果想在这些位置之上构建一个“餐馆”数据库，可以让餐馆有一个指向地点的 `OneToOneField` 字段，而不是在餐馆模型中重复这些字段（因为餐馆“是”一个地点，事实上，要处理这个问题，通常会使用继承，它涉及到隐式的一对一关系）。

与使用外键一样，可以定义递归关系，并且可以对尚未定义的模型进行引用。

参见:

`OneToOneField` 字段还接受一个可选的 parent_link 参数。

`OneToOneField` 类自动成为模型上的主键，这不再正确（尽管如果愿意，您可以手动传入`primary_key`参数）。因此，现在可以在一个模型上有多个一对一类型的字段。

##### 3.2.1.3.6. 模型跨文件

将一个模型与另一个应用程序中的模型关联起来是完全可以的。为此，在定义模型的文件的顶部导入相关的模型。然后，只要在需要的地方引用之。例如：

```python
from django.db import models
from geography.models import ZipCode
class Restaurant(models.Model):
    # ...
    zip_code = models.ForeignKey(
        ZipCode,
        on_delete=models.SET_NULL,
        blank=True,
        null=True,
    )
```

##### 3.2.1.3.7. 字段名限制

Django 对模型字段名只有两个限制:

1. 字段名不能是 Python 保留字，因为这会导致 Python 语法错误。例如：

```python
class Example(models.Model):
pass = models.IntegerField() # 'pass' 是保留字
```

2. 由于 Django 的查询语法的工作方式，字段名在一行中不能包含多个下划线。例如：

```python
class Example(models.Model):
foo__bar = models.IntegerField() # 'foo__bar' 有两个下划线
```

不过，这些限制可以解决，因为字段名不一定要与数据库列名匹配。请参阅 API。

允许使用 SQL 保留字（比如`join`、`where`、`select`）作为模型字段名，因为 Django 在每个底层 SQL 查询中转义所有数据库表名和列名。

##### 3.2.1.3.8. 定制字段类型

如果现有的模型字段不能用于满足需要，或者希望利用一些不太常见的数据库列类型，则可以创建自己的 `field` 类。此处从略。

#### 3.2.1.4. 元选项

通过使用内部 `Meta` 类为模型提供元数据，如下所示：

```python
from django.db import models
class Ox(models.Model):
    horn_length = models.IntegerField()
    class Meta:
        ordering = ["horn_length"]
        verbose_name_plural = "oxen"
```

模型元数据是“任何不是字段的东西”，例如排序选项（`ordering`）、数据库表名（`db_table`）或人类可读的单复数名（`verbose_name`、`verbose_name_plural`）。

它们都不是必需的，并且向模型添加 `Meta` 类是可选的。

#### 3.2.1.5. 模型属性

模型最重要的属性是 `Manager`。它是向 Django 模型提供数据库查询操作的接口，用于从数据库检索实例。如果没有定义定制 `Manager`，则默认名称为 `objects` 。`Manager` 只能通过模型类访问，而不能通过模型实例访问。

#### 3.2.1.6. 模型方法

在模型上定制方法，以便向对象添加“行级”功能。`Manager` 方法旨在做“表范围”的事情，模型方法应该作用于特定的模型实例。

这是一种将业务逻辑保存在一个地方（模型）的有价值的技术。

例如，这个模型有几个自定义方法:

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    birth_date = models.DateField()

    def baby_boomer_status(self):
        "Returns the person's baby-boomer status."
        import datetime
        if self.birth_date < datetime.date(1945, 8, 1):
            return "Pre-boomer"
        elif self.birth_date < datetime.date(1965, 1, 1):
            return "Baby boomer"
        else:
            return "Post-boomer"

    @property
    def full_name(self):
        "Returns the person's full name."
        return '%s %s' % (self.first_name, self.last_name)
```

本例中的最后一个方法是属性。

较常需要重写的方法如下：

**`__str__()`**

一个 Python “魔法方法”，返回任何对象的字符串表示形式。这就是 Python 和 Django 在强制将一个模型实例显示为普通字符串时使用的方法。在交互式控制台或管理器中显示对象时，就会用到。

你总是想要重写这个方法，默认值根本没有多大帮助。

**get_absolute_url()**

这告诉 Django 如何计算对象的 URL。 Django 在它的管理界面中使用了这个方法，并且在任何时候它都需要为一个对象找到一个 URL。
任何具有唯一标识 URL 的对象都应该定义此方法。


##### 3.2.1.6.1. 重写预定义的模型方法

还有另外一组模型方法，它们封装了一系列您想要定制的数据库行为。特别是，您经常希望更改 `save()` 和 `delete()` 的工作方式。

您可以自由地覆盖这些方法（以及任何其他模型方法）来更改行为。

覆盖内置方法的一个经典用例是，如果希望在保存对象时发生某些事情。例如：

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def save(self, *args, **kwargs):
        do_something()
        super().save(*args, **kwargs) # 调用真正的 save() 方法
        do_something_else()
```

也可以避免保存：

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def save(self, *args, **kwargs):
        if self.name == "Yoko Ono's blog":
            return # 不保存
        else:
            super().save(*args, **kwargs) # 调用真正的 save() 方法
```

重要的是，要记得调用方法 `super().save(*args, **kwargs)`，确保对象保存到数据库中。如果忘记调用此方法，则不会发生默认行为，也不会触及数据库。

传递给模型方法的参数也很重要，即：`*args`、`**kwargs`。 Django 将不时地扩展内置模型方法的功能，添加新的参数。如果在方法定义中使用 `*args`、`**kwargs`，则可以确保代码在添加这些参数时自动支持它们。

> **重写的模型方法不会在批量操作上调用**
>
> 注意，当使用 `QuerySet` 或由于级联删除而大量删除对象时，不一定调用对象的 `delete()` 方法。要确保执行定制的删除逻辑，可以使用 `pre_delete` 和/或 `post_delete` 信号。
>
> 不幸的是，在批量创建或更新对象时没有解决方案，因为没有 `save()`、`pre_save`、`post_save`。



##### 3.2.1.6.2. 执行定制 SQL

另一种常见模式是在模型方法和模块级方法中编写自定义SQL语句。详见 API。

#### 3.2.1.7. 模型继承

Django 中的模型继承与 Python 中的常规类继承工作方式几乎完全相同，但是基类应该是 `django.db.models.Model` 的子类。

您必须做出的惟一决定是，您是希望父模型具有它们自己的模型（具有它们自己的数据库表），还是父模型只是公共信息的持有者，这些公共信息只通过子模型可见。

Django 中有三种可能的继承模式。

1. 若只想使用父类来保存不需要为每个子模型键入的信息。这个类永远不会单独使用，此时使用抽象基类。

2. 如果要子类化一个现有模型（可能完全来自另一个应用程序），并且希望每个模型都有自己的数据库表，可以采用多表继承。

3. 如果只想修改模型的 python 级行为，而不以任何方式更改 `models` 字段，可以使用代理模型。

##### 3.2.1.7.1. 抽象基类

当您希望将一些公共信息放入许多其他模型中时，抽象基类非常有用。编写基类并将 `abstract=True` 放在 `Meta` 类中，则这个模型将不用于创建任何数据库表。相反，当它被用作其他模型的基类时，它的字段将被添加到子类的字段中。

例如：

```python
from django.db import models

class CommonInfo(models.Model):
    name = models.CharField(max_length=100)
    age = models.PositiveIntegerField()
    class Meta:
        abstract = True
class Student(CommonInfo):
    home_group = models.CharField(max_length=5)
```

`Student` 模型将有三个字段：`name`、`age`、`home_group`。

`CommonInfo` 模型不能作为普通的 Django 模型使用，因为它是一个抽象基类。它不生成数据库表或具有管理器，并且不能直接实例化或保存。

继承自抽象基类的字段可以用另一个字段或值覆盖，也可以用 `None` 删除。

有许多用途，需要这种类型的模型继承。它提供了一种在 Python 级别提取公共信息的方法，同时仍然在数据库级别为每个子模型创建一个数据库表。

###### 3.2.1.7.1.1. 元继承

当创建抽象基类时， Django 将基类中声明的任何 `Meta` 内部类作为属性提供。如果一个子类没有声明它自己的 `Meta` 类，它将继承父类的 `Meta` 类。如果子类想要扩展父类的 `Meta` 类，可以子类化它。例如：

```python
from django.db import models

class CommonInfo(models.Model):
    # ...
    class Meta:
        abstract = True
        ordering = ['name']
class Student(CommonInfo):
    # ...
    class Meta(CommonInfo.Meta):
        db_table = 'student_info'
```

Django 对抽象基类的 `Meta` 类做了一个调整：在安装 `Meta` 属性之前，它设置 `abstract=False`。这意味着抽象基类的子类本身不会自动成为抽象类。当然，您可以创建一个抽象基类，它继承自另一个抽象基类。您只需要记住每次都显式地设置 `abstract=True`。

有些属性不适合包含在抽象基类的 `Meta` 类中。例如，包含 `db_table` 将意味着所有子类（不指定它们自己的 `Meta` 数据）将使用相同的数据库表，这几乎肯定不是您想要的。

###### 3.2.1.7.1.2. 小心 related_name 和 related_query_name

如果在 `ForeignKey` 或 `ManyToManyField` 上使用 `related_name` 或 `related_query_name`，则必须始终为该字段指定惟一的反向名称和查询名称。这通常会在抽象基类中引起问题，因为该类上的字段包含在每个子类中，每次属性的值都完全相同（包括`related_name`和`related_query_name`）。

要解决这个问题，当您仅在抽象基类中使用 `related_name` 或 `related_query_name` 时，值的一部分应该包含`'%(app_label)s'`和`'%(class)s'`。

- `'%(class)s'`替换为字段中使用的子类的小写名称。

- `'%(app_label)s'`替换为包含子类的应用程序的小写名称。

每个已安装的应用程序名称必须是惟一的，每个应用程序中的模型类名称也必须是惟一的，因此最终得到的名称将是不同的。

例如，给定一个应用程序 `common/models.py`：

```python
from django.db import models

class Base(models.Model):

    m2m = models.ManyToManyField(
        OtherModel,
        related_name="%(app_label)s_%(class)s_related",
        related_query_name="%(app_label)s_%(class)ss",
    )

    class Meta:
        abstract = True

class ChildA(Base):
    pass

class ChildB(Base):
    pass
```

以及另一个 rare/models.py：

```python
from common.models import Base

class ChildB(Base):
    pass
```

`common.ChildA.m2m` 字段的反向名称将是 `common_childa_related`，反向查询名将是 `common_childas`。

`common.ChildB.m2m` 字段的反向名称将是 `common_childb_related`，反向查询名将是 `common_childbs`。

最终，`common.ChildB.m2m` 字段的反向名称将是 `rare_childb_related`，反向查询名将是 `rare_childbs`。

取决于如何使用 `'%(class)s'` 和 `'%(app_label)s'` 来构造相关名称或相关查询名称，但是如果忘记使用它， Django 将在您执行系统检查(或运行迁移)时引发错误。

如果没有在抽象基类中为字段指定 `related_name` 属性，则默认的反向名称将是子类的名称，后跟`_set`，就像直接在子类上声明字段一样。例如，在上面的代码中，如果省略了 `related_name` 属性，那么 `m2m` 字段的反向名称在 `ChildA` 中是 `childa_set`，在 `ChildB` 中是`childb_set`。

##### 3.2.1.7.2. 多表继承

Django 支持的第二种模型继承类型是，层次结构中的每个模型都是一个单独的模型。每个模型对应于它自己的数据库表，可以单独查询和创建。继承关系在子模型和它的每个父模型之间引入了链接(通过一个自动创建的一对一字段)。例如：

```python
from django.db import models

class Place(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=80)

class Restaurant(Place):
    serves_hot_dogs = models.BooleanField(default=False)
    serves_pizza = models.BooleanField(default=False)
```

`Place` 的所有字段也将在 `Restaurant` 中可用，尽管数据将驻留在不同的数据库表中。所以这两种都是可能的：

```bash
>>> Place.objects.filter(name="Bob's Cafe")
>>> Restaurant.objects.filter(name="Bob's Cafe")
```

如果有一个地点同时也是一个餐厅，可以使用模型名称的小写版本，从 `Place` 对象到 `Restaurant` 对象：

```bash
>>> p = Place.objects.get(id=12)
# p 是个地点，将有个子类是餐厅
>>> p.restaurant
<Restaurant: ...>
```

但是，如果上面例子中的 `p` 不是一个餐厅（它是直接作为 `Place` 对象创建的，或者是其他类的父类），则引用 `p.restaurant` 会引发 `Restaurant.DoesNotExist` 例外。

自动创建的从 `Restaurant` 到 `Place` 的 `OneToOneField`，看起来是这样的：

```python
place_ptr = models.OneToOneField(
    Place, on_delete=models.CASCADE,
    parent_link=True,
)
```

可以通过在 `Restaurant` 上用 `parent_link=True` 声明自己的 `OneToOneField` 来覆盖该字段。

###### 3.2.1.7.2.1. Meta与多表继承

在多表继承的情况下，子类从父类的 `Meta` 继承是没有意义的。

所有 `Meta` 选项都已应用于父类，再次应用它们通常只会导致矛盾的行为（这与抽象基类的情况相反，基类本身并不存在）。

因此子模型不能访问其父模型的 `Meta` 类。但是，在少数有限的情况下，子元素从父元素继承行为，如果子元素没有指定 `order` 属性或 `get_latest_by` 属性，那么它将从父元素继承这些属性。

如果父节点有顺序，而您不希望子节点有任何自然顺序，则可以显式禁用它：

```python
class ChildModel(ParentModel):
    # ...
    class Meta:
        # 移除父类的排序效果
        ordering = []
```

###### 3.2.1.7.2.2. 继承与反向关系

因为多表继承使用一个隐式的 `OneToOneField` 来链接子表和父表，所以可以从父表向下移动到子表，如上面的示例所示。

但是，这将使用默认 `related_name` 值用于 `ForeignKey` 和 `ManyToManyField` 关系。

如果将这些关系类型放在父模型的子类上，则必须在每个这样的字段上指定 `related_name` 属性。否则， Django 将会引发一个验证错误。

例如，再次使用上面的 `Place` 类，创建另一个带有 `ManyToManyField` 的子类：

```python
class Supplier(Place):
    customers = models.ManyToManyField(Place)
```

结果出错：

```bash
Reverse query name for 'Supplier.customers' clashes with reverse query
name for 'Supplier.place_ptr'.
HINT: Add or change a related_name argument to the definition for
'Supplier.customers' or 'Supplier.place_ptr'.
```

添加如下字段即可解决这个错误：

```python
related_name = models.ManyToManyField(Place, related_name='provider')
```

###### 3.2.1.7.2.3. 指定父链接字段

如前所述， Django 将自动创建一个 `OneToOneField`，将子类链接回任何非抽象的父模型。如果希望控制链接回父类的属性的名称，可以创建自己的 `OneToOneField` 并设置 `parent_link=True`，以指示该字段是返回父类的链接。

##### 3.2.1.7.3. 代理模型

当使用多表继承时，将为模型的每个子类创建一个新的数据库表。这通常是所需的行为，因为子类需要一个位置来存储基类中不存在的任何其他数据字段。然而，有时您只想更改模型的 Python 行为，可能是更改默认的管理器，或者添加一个新方法。

这就是代理模型继承的作用：为原始模型创建代理。您可以创建、删除和更新代理模型的实例，所有数据都将像使用原始（非代理）模型一样保存。不同之处在于，您可以更改诸如代理中的默认模型排序或默认管理器之类的内容，而无需更改原始的排序。

代理模型像普通模型一样声明。通过将 `Meta` 类的 `proxy` 属性设置为 `True`，可以告诉 Django 它是一个代理模型。

例如，假设您想要向 `Person` 模型添加一个方法。你可以这样做:

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

class MyPerson(Person):
    class Meta:
        proxy = True
        def do_something(self):
            # ...
            pass
```

`MyPerson` 类与它的父类 `Person` 操作在同一个数据库表上。特别是，任何新的 `Person` 实例也可以通过 `MyPerson` 访问，反之亦然：

```bash
>>> p = Person.objects.create(first_name="foobar")
>>> MyPerson.objects.get(first_name="foobar")
<MyPerson: foobar>
```

还可以使用代理模型在模型上定义不同的默认顺序。您可能不总是想要对 `Person` 模型进行排序，在使用代理时，需要按照 `last_name` 属性进行排序。这很简单：

```python
class OrderedPerson(Person):
    class Meta:
        ordering = ["last_name"]
        proxy = True
```

现在普通的 `Person` 查询将是无序的，`OrderedPerson` 查询将按 `last_name` 排序。

代理模型以与常规模型相同的方式继承元属性。

###### 3.2.1.7.3.1. 只返回要求的请求集

无论何时查询 `Person` 对象， Django 都不可能返回 MyPerson 对象。

代理对象的全部意义在于，依赖于原始 `Person` 的代码将原始代码，而您自己的代码可以使用包含的扩展（没有其他代码依赖于这些扩展）。它不是用你自己创造的东西来取代 `Person` 模型。

###### 3.2.1.7.3.2. 基类限制

代理模型必须从一个非抽象模型类继承。不能从多个非抽象模型继承，因为代理模型不提供不同数据库表中的行之间的任何连接。

代理模型可以继承任意数量的抽象模型类，前提是它们不定义任何模型字段。

代理模型还可以继承任意数量的共享公共非抽象父类的代理模型。

###### 3.2.1.7.3.3. 代理模型管理器

如果您没有在代理模型上指定任何模型管理器，它将从其模型父类继承这些管理器。如果您在代理模型上定义一个管理器，它将成为缺省值，尽管在父类上定义的任何管理器仍然可用。

继续上面的例子，当你像这样查询 `Person` 模型时，你可以改变默认的管理器：

```python
from django.db import models

class NewManager(models.Manager):
    # ...
    pass

class MyPerson(Person):
    objects = NewManager()
    class Meta:
        proxy = True
```

如果您想在不替换现有默认值的情况下向代理添加一个新管理器，可以：创建一个包含新管理器的基类，并在主基类之后继承该基类：

```python
# 为管理器创建抽象基类
class ExtraManagers(models.Model):
    secondary = NewManager()
    class Meta:
        abstract = True

class MyPerson(Person, ExtraManagers):
    class Meta:
        proxy = True
```

可能不需要经常这样做，但是，这是可能的。

###### 3.2.1.7.3.4. 代理继承与非托管模型的差别

代理模型继承看起来，与在模型的 `Meta` 类上使用 `managed` 属性创建非托管模型很类似。

小心设置 `Meta.db_table` 可以创建一个非托管模型，现有模型的影子，并向其添加 Python 方法。然而，这将是非常重复和脆弱的，因为如果做任何更改，需要保持同步的两个副本。

另一方面，代理模型的行为与它们所代理的模型完全相同。它们总是与父模型保持同步，因为它们直接继承父模型的字段和管理器。

一般规则是:

1. 如果镜像一个现有的模型或数据库表，并且不想要所有原始的数据库表列，那么使用 `Meta.managed=False`。该选项通常用于建模不受 Django 控制的数据库视图和表。

2. 如果只想更改模型的 Python 行为，但要保持与原始字段相同的所有字段，请使用 `Meta.proxy=True`。以便在保存数据时，代理模型是原始模型存储结构的精确副本。

##### 3.2.1.7.4. 多继承

就像 Python 的子类化一样， Django 模型也可以从多个父模型继承。请记住，应用常规的 Python 名称解析规则。出现特定名称（例如`Meta`）的第一个基类将是所使用的基类，例如，这意味着如果多个父类包含 `Meta` 类，那么将只使用第一个，而忽略其它。

一般来说，你不需要从多个父类那里继承。在主要用例中，这对于`mix-in`类很有用，向继承 `mix-in` 的每个类添加特定的额外字段或方法。尽量保持继承层次结构的简单和直观，这样您就不必费力找出特定信息的来源。

注意，从具有共同的 `id` 主键字段的多个模型继承会引发错误。要正确使用多重继承，可以在基本模型中使用显式的 `AutoField`：

```python
class Article(models.Model):
    article_id = models.AutoField(primary_key=True)
    ...

class Book(models.Model):
    book_id = models.AutoField(primary_key=True)
    ...

class BookReview(Book, Article):
    pass
```

或者使用一个公共祖先来保存 `AutoField`。这需要使用一个显式的 `OneToOneField` 从每个父模型指向公共祖先，以避免自动生成和继承的字段之间的冲突:

```python
class Piece(models.Model):
    pass

class Article(Piece):
    article_piece = models.OneToOneField(Piece, on_delete=models.CASCADE, parent_link=True)
    ...

class Book(Piece):
    book_piece = models.OneToOneField(Piece, on_delete=models.CASCADE, parent_link=True)
    ...

class BookReview(Book, Article):
    pass
```

##### 3.2.1.7.5. 不允许“隐藏”字段名

在常规 Python 类继承中，允许子类覆盖父类的任何属性。在 Django 中，模型字段通常不允许这样做。如果非抽象模型基类有一个名为 `author` 的字段，则不能在继承自该基类的任何类中创建另一个模型字段或定义一个名为 `author` 的属性。

此限制不适用于从抽象模型继承的模型字段。这些字段可以被另一个字段或值覆盖，或者通过设置 `field_name = None` 删除。

> 警告:模型管理器是从抽象基类继承的。重写由继承管理器引用的继承字段可能会导致细微的错误。参见API。

> 注意:有些字段在模型上定义了额外的属性，例如，一个外键定义了一个附加在字段名后面的额外属性`_id`，以及在外部模型上定义了 `related_name 和 related_query_name`。
>
> 除非定义这些额外属性的字段被更改或删除，以不再定义这些额外属性，否则无法覆盖这些额外属性。

覆盖父模型中的字段会导致一些困难，比如初始化新实例（在 `Model.__init__` 中指定初始化哪个字段）和序列化。这些特性是普通 Python 类继承不需要以完全相同的方式处理的，所以 Django 模型继承和 Python 类继承之间的还是有区别的。

此限制仅适用于作为字段实例的属性。普通 Python属性可以任意重写。它还仅适用于 Python 所看到的属性的名称，如果手动指定数据库列名，则可以在多表继承的子模型和祖先模型中显示相同的列名（它们是两个不同数据库表中的列）。

如果您覆盖任何祖先模型中的任何模型字段， Django 将引发一个 `FieldError`。

#### 3.2.1.8. 在包中组织模型

`manage.py startapp`命令创建一个包含`models.py`文件的应用程序结构。如果您有许多模型，将它们组织到单独的文件中可能会很有用。

为此，创建一个 `models` 包。删除`models.py`并创建一个`myapp/models/`目录，其中包含一个`_init_ .py`文件和用来存储模型的文件。必须在`_init_ .py`文件中导入模型。

例如，如果您在 `models` 目录中有 `organic.py`和`synthetic.py`：

```python
from .organic import Person
from .synthetic import Robot
```

显式地导入每个模型，而不是使用`from .models import *`，这样做的好处是不会混淆名称空间，使代码更具可读性，并使代码分析工具保持有用。

### 3.2.2. 进行查询

一旦创建了数据模型， Django 就会自动提供一个数据库抽象API，让您可以创建、检索、更新和删除对象。

在本指南中(以及参考资料中)，我们将引用以下模型，这些模型包含一个 `Weblog` 应用程序：

```python
from django.db import models

class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()
    def __str__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()
    def __str__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog, on_delete=models.CASCADE)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField()
    n_pingbacks = models.IntegerField()
    rating = models.IntegerField()
    def __str__(self):
        return self.headline
```

#### 3.2.2.1. 创建对象

为了在 Python 对象中表示数据库表数据， Django 使用了一个直观的系统：一个模型类表示一个数据库表，该类的一个实例表示数据库表中的一个特定记录。

要创建对象，请使用模型类的关键字参数实例化它，然后调用`save()`将其保存到数据库。

假设模型位于文件`mysite/blog/models.py`中，举个例子：

```bash
>>> from blog.models import Blog
>>> b = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')
>>> b.save()
```

这将在幕后执行`INSERT SQL`语句。 Django 在显式调用`save()`之前不会访问数据库。

`save()`方法没有返回值。

参见:

`save()`接受另外一些高级选项。参见API。

要在单个步骤中创建和保存对象，请使用`create()`方法。

#### 3.2.2.2. 把修改保存到对象

要保存对数据库中已有对象的更改，请使用`save()`。

给定一个已经保存到数据库中的 `Blog` 的实例 `b5`，此例更改它的名称并更新它在数据库中的记录：

```bash
>>> b5.name = 'New name'
>>> b5.save()
```

这将在幕后执行`UPDATE SQL`语句。 Django 在显式调用`save()`之前不会访问数据库。

**保存 ForeignKey 与 ManyToManyField 字段**

更新一个 `ForeignKey` 字段的工作方式与保存一个普通字段完全相同，只需将一个正确类型的对象分配给相关字段。

这个例子用 `Entry` 的实例 `entry` 更新 `blog` 属性，假设 `Entry` 的适当实例和 `blog` 已经保存到数据库中（因此我们可以在下面检索它们）：

```bash
>>> from blog.models import Blog, Entry
>>> entry = Entry.objects.get(pk=1)
>>> cheese_blog = Blog.objects.get(name="Cheddar Talk")
>>> entry.blog = cheese_blog
>>> entry.save()
```

更新 `ManyToManyField` 字段有点不同，使用字段上的 `add()` 方法向关系添加一条记录。这个例子将 `Author` 实例 `joe` 添加到 `entry` 对象：

```bash
>>> from blog.models import Author
>>> joe = Author.objects.create(name="Joe")
>>> entry.authors.add(joe)
```

要一次性向 `ManyToManyField` 添加多条记录，需要在 `add()` 调用中包含多个参数，如下所示：

```bash
>>> john = Author.objects.create(name="John")
>>> paul = Author.objects.create(name="Paul")
>>> george = Author.objects.create(name="George")
>>> ringo = Author.objects.create(name="Ringo")
>>> entry.authors.add(john, paul, george, ringo)
```

如果您试图分配或添加错误类型的对象， Django 会发出警告。

#### 3.2.2.3. 检索对象

要从数据库检索对象，请通过模型类上的 `Manager` 构造 `QuerySet`。

`QuerySet` 表示数据库中的对象集合。它可以有0个、1个或多个过滤器。过滤器根据给定的参数缩小查询结果的范围。在SQL术语中，`QuerySet`等同于 `SELECT` 语句，过滤器是一个限制子句，例如 `WHERE` 或 `LIMIT`。

通过使用模型的管理器，您可以得到一个`QuerySet`。每个模型至少有一个管理器，默认情况下称为 `objects`。通过 `model` 类直接访问，如下：

```bash
>>> Blog.objects
<django.db.models.manager.Manager object at ...>
>>> b = Blog(name='Foo', tagline='Bar')
>>> b.objects
Traceback:
...
AttributeError: "Manager isn't accessible via Blog instances."
```

注意:管理器只能通过模型类访问，而不是从模型实例访问，以强制“表级”操作和“记录级”操作之间的分离。

管理器是模型查询集的主要来源。例如，`Blog.objects.all()`返回一个`QuerySet`，其中包含数据库中的所有`Blog`对象。

##### 3.2.2.3.1. 检索所有对象

从表中检索对象的最简单方法是获取所有对象。要做到这一点，对一个管理器使用`all()`方法:

```bash
>>> all_entries = Entry.objects.all()
```

`all()`方法返回数据库中所有对象的`QuerySet`。

##### 3.2.2.3.2. 使用过滤器检索指定对象

`all()` 返回的 `QuerySet` 描述数据库表中的所有对象。不过，通常只需要选择完整对象集的子集。

要创建这样一个子集，您需要细化初始`QuerySet`，添加筛选条件。优化`QuerySet`最常见的两种方法是:

- `filter(**kwargs)`返回一个新的QuerySet，其中包含匹配给定查找参数的对象。

- `exclude(**kwargs)`返回一个新的QuerySet，其中包含与给定查找参数不匹配的对象。

查找参数(`**kwargs`)应该采用下面字段查找中描述的格式。

例如，要获取2006年的博客条目查询集，可以使用`filter()`，如下所示:

```bash
Entry.objects.filter(pub_date__year=2006)
```

对于默认的`manager`类，它等同于:

```bash
Entry.objects.all().filter(pub_date__year=2006)
```

###### 3.2.2.3.2.1. 过滤器链

过滤`QuerySet`的结果本身就是一个`QuerySet`，因此可以将过滤链接在一起。例如:

```bash
>>> Entry.objects.filter(
... headline__startswith='What'
... ).exclude(
... pub_date__gte=datetime.date.today()
... ).filter(
... pub_date__gte=datetime.date(2005, 1, 30)
... )
```

这将获取数据库中所有条目的初始`QuerySet`，添加一个过滤器，然后是一个排除，然后是另一个过滤器。最后的结果是一个`QuerySet`，其中包含从2005年1月30日到现在发布的所有条目，标题以`What`开头。

###### 3.2.2.3.2.2. 查询集的独一性

每次查询`QuerySet`时，都将得到一个新的`QuerySet`，而这个`QuerySet`绝不绑定到前面的`QuerySet`。每个查询都创建一个单独的、不同的`QuerySet`，该`QuerySet`可以存储、使用和重用。

例如：

```bash
>>> q1 = Entry.objects.filter(headline__startswith="What")
>>> q2 = q1.exclude(pub_date__gte=datetime.date.today())
>>> q3 = q1.filter(pub_date__gte=datetime.date.today())
```

这三个查询集是独立的。

第一个是一个基本QuerySet，其中包含以`What`开头的标题的所有条目。

第二个是第一个的子集，带有一个附加的条件，它排除了`pub_date`为今天或将来的记录。

第三个是第一个的子集，还有一个附加条件，它只选择`pub_date`为今天或将来的记录。

初始`QuerySet` (`q1`)不受查询过程的影响。

###### 3.2.2.3.2.3. 查询集是惰性的

`QuerySet`是惰性的，创建`QuerySet`的行为不涉及任何数据库活动。 Django 在`QuerySet`被`evaluated`之前实际上不会运行查询。看看这个例子:

```bash
>>> q = Entry.objects.filter(headline__startswith="What")
>>> q = q.filter(pub_date__lte=datetime.date.today())
>>> q = q.exclude(body_text__icontains="food")
>>> print(q)
```

虽然这看起来像三个数据库命中，但实际上它只命中数据库一次，在最后一行(`print(q)`)。通常，在您“请求”查询集之前，查询集的结果不会从数据库中获取。

##### 3.2.2.3.3. 使用 get() 检索单个对象

`filter()`总是会给您一个`QuerySet`，即使只有一个对象与查询匹配，在本例中，它将是一个包含单个元素的`QuerySet`。

如果你知道只有一个对象匹配你的查询，你可以使用`get()`方法直接返回对象:

```bash
>>> one_entry = Entry.objects.get(pk=1)
```

您可以使用任何查询表达式带上`get()`，就像使用`filter()`一样，同样，请参见下面的字段查找。

注意，使用`get()`和使用`filter()[0]`有区别的。如果没有匹配查询的结果，`get()`将引发 `DoesNotExist` 异常。这个异常是正在执行查询的模型类的一个属性,因此在上面的代码中，如果没有主键为1的 `Entry` 对象， Django 将引发`Entry.DoesNotExist`。

类似地，如果有多个项目匹配`get()`查询， Django 将发出抱怨。在本例中，它将引发 `MultipleObjectsReturned`，这也是模型类本身的一个属性。

##### 3.2.2.3.4. 其它查询集方法

大多数时候，当您需要从数据库中查找对象时，您将使用`all()`、`get()`、`filter()`、`exclude()`。

有关`QuerySet`方法的完整列表，参见API。

##### 3.2.2.3.5. 限制查询集

使用 Python 数组切片语法的一个子集来将查询集限制为一定数量的结果。这相当于 SQL 的 `LIMIT` 和 `OFFSET` 子句。

例如，它返回前5个对象：

```bash
>>> Entry.objects.all()[:5]
```

返回第6-10个对象：

```bash
>>> Entry.objects.all()[5:10]
```

不支持负索引（比如`Entry.objects.all()[-1]`）。

通常，切片`QuerySet`会返回一个新的`QuerySet`，它不计算查询。一个例外是，如果使用 Python 切片语法的`step`参数。例如，这将实际执行查询，以便返回前10个查询的每个查询的第二个对象的列表：

```bash
>>> Entry.objects.all()[:10:2]
```

由于切片`queryset`工作方式的模糊性，禁止对其进行进一步过滤或排序。

要检索单个对象而不是列表（例如`SELECT foo FROM bar LIMIT 1`），请使用简单的索引而不是切片。例如，这将在按标题的字母顺序排列条目之后返回数据库中的第一个条目：

```bash
>>> Entry.objects.order_by('headline')[0]
```

这大致相当于:

```bash
>>> Entry.objects.order_by('headline')[0:1].get()
```

但是，请注意，如果没有对象匹配给定的标准，那么第一个方法将引发`IndexError`，第二个方法将引发`DoesNotExist`。

##### 3.2.2.3.6. Field lookups

字段查找是指定`SQL WHERE`子句的内容的方式。它们被指定为`QuerySet`方法`filter()`、`exclude()`、`get()`的关键字参数。

基本查找关键字参数采用表单`field__lookuptype=value`。(使用双下划线)。

例如:

```bash
>>> Entry.objects.filter(pub_date__lte='2006-01-01')
```

将(大致)转换为以下SQL:

```sql
SELECT * FROM blog_entry WHERE pub_date <= '2006-01-01';
```

**这是怎样做到的** Python能够定义接受任意 `name-value`参数的函数，这些参数的名称和值在运行时计算。有关更多信息，参见API。

查找中指定的字段必须是模型字段的名称。但是有一个例外，对于外键，您可以指定以`_id`为后缀的字段名。在这种情况下，`value`参数应该包含外部模型的主键的原始值。例如:

```bash
>>> Entry.objects.filter(blog_id=4)
```

如果传递无效的关键字参数，则查找函数将引发`TypeError`。

数据库API支持大约24种查找类型。下面是一些比较常见的查找:

**exact** 准确

```bash
>>> Entry.objects.get(headline__exact="Cat bites dog")
```

生成 SQL 如下:

```sql
SELECT ... WHERE headline = 'Cat bites dog';
```

如果不提供查找类型（也就是说，如果关键字参数不包含双下划线），则假定查找类型是精确的。

例如，下面两句话是等价的:
```bash
>>> Blog.objects.get(id__exact=14) # 显式表单
```

```bash
>>> Blog.objects.get(id=14) # 隐式__exact
```

这是为了方便，因为精确查找是常见的情况。

**iexact** 不区分大小写

```bash
>>> Blog.objects.get(name__iexact="beatles blog")
```

将匹配 `Beatles Blog`、 `beatles blog`，甚至 `BeAtlES blOG`。

**contains** 包含

```bash
Entry.objects.get(headline__contains='Lennon')
```

大致等同于：

```sql
SELECT ... WHERE headline LIKE '%Lennon%';
```

注意，这将匹配标题`Today Lennon honored`，而非`today lennon honored`。

还有一个不区分大小写的版本`icontains`。

**startswith** **endswith**

分别搜索开始值、结束值，不区分大小写的版本为 `istartswith`、 `iendswith`。

完整用法见API。

##### 3.2.2.3.7. 跨越关系的查找

Django 提供了一种强大而直观的方法来“跟踪”查询中的关系，在幕后自动地处理SQL连接。要跨越关系，只需跨模型使用相关字段的字段名，
用双下划线分隔，直到您到达您想要的字段。

这个例子检索了具有名为`Beatles Blog`的`Blog`的所有`Entry`：

```bash
>>> Entry.objects.filter(blog__name='Beatles Blog')
```

这个跨度可以是你想要的深度。

它也可以逆向进行。要引用“反向”关系，只需使用模型的小写名称。

这个例子检索了所有的`Blog`对象，其中至少有一个`Entry`的标题包含`Lennon`：

```bash
>>> Blog.objects.filter(entry__headline__contains='Lennon')
```

如果您正在跨多个关系进行筛选，而其中一个中间模型没有满足筛选条件的值， Django 将把它当作一个空的（所有值都为`NULL`）但是有效的对象。这意味着不会产生错误。例如，在这个过滤器中:

```bash
Blog.objects.filter(entry__authors__name='Lennon')
```

(如果有相关的`Author`模型)，如果没有与条目关联的`Author`，则会将其视为没有附加名称，而不是由于缺少`Author`而引发错误。通常这正是你想要发生的。唯一可能让人混淆的情况是使用`isnull`。因此:

```bash
Blog.objects.filter(entry__authors__name__isnull=True)
```

将返回作者名为空的博客对象，以及条目名为空的博客对象。如果你不想要后面这些对象，你可以这样写:

```bash
Blog.objects.filter(entry__authors__isnull=False, entry__authors__name__isnull=True)
```

###### 3.2.2.3.7.1. 跨多值关系

当您基于`ManyToManyField`或反向 `ForeignKey` 过滤对象时，您可能会对两种不同类型的过滤器感兴趣。

考虑 `Blog/Entry` 关系（一对多）：

我们可能有兴趣找到这样的博客，它的标题中含有`Lennon`条目，又在2008年发表。或者，我们可能想要找到这样的博客，它的标题中有`Lennon`条目，也有一个于2008年发表的条目。因为有多个条目与一个博客相关联，所以这两个查询都是可能的，并且在某些情况下是有意义的。

同样的情况，例如，如果一个条目有 `ManyToManyField`关系名为`tags`，我们可能想要找到链接到名为`music`和`bands`的标签的条目，或者我们可能想要一个条目，包含名为`music`的标签，状态为`public`。

为了处理这两种情况， Django 使用了一种一致的方法来处理`filter()`调用。同时应用单个`filter()`中的所有内容来过滤出匹配所有这些需求的项。连续的`filter()`调用进一步限制了对象集，但是对于多值关系，它们适用于与主模型链接的任何对象，不一定是前面`filter()`调用选择的对象。

这听起来可能有点令人困惑，所以希望有一个例子可以澄清这一点。要选择标题中同时包含`Lennon`和2008年出版的所有博客(同一篇文章满足两种条件)，我们可以这样写:

```bash
Blog.objects.filter(entry__headline__contains='Lennon', entry__pub_date__year=2008)
```

要选择所有标题中包含`Lennon`条目、也有一个条目于2008年发表的博客，我们可以这样写:

```bash
Blog.objects.filter(entry__headline__contains='Lennon').filter(entry__pub_date__year=2008)
```

假设只有一个博客同时包含`Lennon`的条目，和来自`2008`的条目，但是来自`2008`的条目中不包含`Lennon`。第一个查询不会返回任何blog，但是第二个查询将返回该blog。

在第二个示例中，第一个过滤器将`queryset`限制为所有链接到标题中有`Lennon`条目的博客。第二个过滤器将博客集进一步限制为那些也链接到2008年发布的条目的博客集。第二个筛选器选择的条目可能与第一个筛选器中的条目相同，也可能不相同。我们使用每个`filter`语句过滤`Blog`项，而不是`Entry`项。

注意：如上所述，对于跨越多值关系的查询，`filter()`的行为对于`exclude()`没有等效地实现。相反，单个`exclude()`调用中的条件不一定引用相同的项。

例如，下面的查询将排除标题中既有`Lennon`条目又有2008年发布的条目的博客:

```bash
Blog.objects.exclude(
    entry__headline__contains='Lennon',
    entry__pub_date__year=2008,
)
```

但是，与使用`filter()`时的行为不同，这不会根据同时满足这两种条件的条目来限制`blog`。为了做到这一点，也就是说，要选择所有不包含`Lennon`在2008年发表的作品的博客，你需要做两个查询:

```bash
Blog.objects.exclude(
    entry__in=Entry.objects.filter(
        headline__contains='Lennon',
        pub_date__year=2008,
    ),
)
```

##### 3.2.2.3.8. 过滤可以引用模型字段

在目前给出的示例中，我们已经构建了过滤器，它将模型字段的值与常量进行比较。但是，如果您想将一个模型字段的值与同一模型上的另一个字段的值进行比较，该怎么办呢？

Django 提供了`F`表达式来允许这种比较。`F()`的实例充当查询中模型字段的引用。然后可以在查询过滤器中使用这些引用来比较同一模型实例上两个不同字段的值。

例如，要查找所有评论多于`pingback`的博客条目列表，我们构造一个`F()`对象来引用pingback计数，并在查询中使用该`F()`对象:

```bash
>>> from django.db.models import F
>>> Entry.objects.filter(n_comments__gt=F('n_pingbacks'))
```

Django 支持对`F()`对象、常量和其他`F()`对象使用加法、减法、乘法、除法、模数和幂运算。要找到所有评论超过pingbacks两倍的博客条目，我们修改查询:

```bash
>>> Entry.objects.filter(n_comments__gt=F('n_pingbacks') * 2)
```

若要查找条目评分小于 `pingback` 和 `comment`之和的所有条目，我们将发出查询:

```bash
>>> Entry.objects.filter(rating__lt=F('n_comments') + F('n_pingbacks'))
```

还可以使用双下划线符号来跨`F()`对象中的关系。带有双下划线的`F()`对象将引入访问相关对象所需的任何连接。例如，要检索所有作者姓名与博客名称相同的条目，我们可以发出以下查询:

```bash
>>> Entry.objects.filter(authors__name=F('blog__name'))
```

对于`date`和`date/time`字段，可以添加或减去一个`timedelta`对象。以下将返回所有在发表后3天内修改过的条目:

```bash
>>> from datetime import timedelta
>>> Entry.objects.filter(mod_date__gt=F('pub_date') + timedelta(days=3))
```

对象支持`.bitand()`、`.bitor()`、`.bitrightshift()`和`.bitleftshift()`的逐位操作。例如:

```bash
>>> F('somefield').bitand(16)
```

##### 3.2.2.3.9. pk 快捷查询

为了方便起见， Django 提供了一个`pk`查找快捷方式，它代表“主键”。

在示例`Blog`模型中，主键是`id`字段，因此这三个语句是等价的:

```bash
>>> Blog.objects.get(id__exact=14) # Explicit form
>>> Blog.objects.get(id=14) # __exact is implied
>>> Blog.objects.get(pk=14) # pk implies id__exact
```

`pk`的使用并不局限于精确查询，任何查询词都可以与pk结合，对模型的主键执行查询:

```bash
# Get blogs entries with id 1, 4 and 7
>>> Blog.objects.filter(pk__in=[1,4,7])
# Get all blog entries with id > 14
>>> Blog.objects.filter(pk__gt=14)
```

`pk`查找也可以跨连接工作。例如，这三个语句是等价的:

```bash
>>> Entry.objects.filter(blog__id__exact=3) # Explicit form
>>> Entry.objects.filter(blog__id=3) # __exact is implied
>>> Entry.objects.filter(blog__pk=3) # __pk implies __id__exact
```

##### 3.2.2.3.10. 在LIKE语句中转义百分号和下划线

等同于`LIKE SQL`语句(`iexact`、`contains`、`icon`、`startswith`、`istartswith`、`endswith`和`iendswith`)的字段查找将自动转义LIKE语句中使用的两个特殊字符：百分号和下划线。(在LIKE语句中，百分号表示多字符通配符，下划线表示单字符通配符。)

这意味着事情应该直观地工作，所以抽象不会泄漏。例如，要检索包含百分号的所有条目，只需将百分号和任何其他字符一样使用:

```bash
>>> Entry.objects.filter(headline__contains='%')
```

Django 为您处理引用，生成的SQL将如下所示:

```sql
SELECT ... WHERE headline LIKE '%\%%';
```

下划线也是一样。百分比符号和下划线都为您透明地处理。

##### 3.2.2.3.11. 缓存与查询集

每个`QuerySet`都包含一个缓存，以最小化数据库访问。理解它的工作原理将使您能够编写最高效的代码。

在新创建的`QuerySet`中，缓存是空的。第一次计算`QuerySet`时(因此会发生数据库查询)， Django 将查询结果保存在`QuerySet`的缓存中，并返回显式请求的结果(例如，如果`QuerySet`被迭代，则返回下一个元素)。`QuerySet`的后续计算重用缓存的结果。

请记住这种缓存行为，因为如果不正确地使用查询集，它可能会影响您。例如，下面将创建两个查询集，对它们求值，然后丢弃它们:

```bash
>>> print([e.headline for e in Entry.objects.all()])
>>> print([e.pub_date for e in Entry.objects.all()])
```

这意味着相同的数据库查询将执行两次，使数据库负载加倍。此外，这两个列表可能不包含相同的数据库记录，因为在两个请求之间可能在瞬间添加或删除了一个条目。

为了避免这个问题，只需保存`QuerySet`并重用它：

```bash
>>> queryset = Entry.objects.all()
>>> print([p.headline for p in queryset]) # 实际计算查询集
>>> print([p.pub_date for p in queryset]) # 重用查询集的缓存
```

###### 3.2.2.3.11.1. 有时查询集不缓存

查询集并不总是缓存它们的结果。当只计算`queryset`的一部分时，会检查缓存，但是如果没有填充缓存，则不会缓存后续查询返回的项。具体来说，这意味着使用数组切片或索引限制`queryset`不会填充缓存。

例如，在`queryset`对象中反复获取某个索引，每次都会查询数据库：

```bash
>>> queryset = Entry.objects.all()
>>> print(queryset[5]) # 查询数据库
>>> print(queryset[5]) # 再次查询数据库
```

但是，如果整个`queryset`已经被计算过，那么缓存将被检查:

```bash
>>> queryset = Entry.objects.all()
>>> [entry for entry in queryset] # 查询数据库
>>> print(queryset[5]) # 使用缓存
>>> print(queryset[5]) # 使用缓存
```

下面是一些其他操作的例子，这些操作将导致整个`queryset`被计算，从而填充缓存：

```bash
>>> [entry for entry in queryset]
>>> bool(queryset)
>>> entry in queryset
>>> list(queryset)
```

>注意：简单地打印`queryset`将不会填充缓存。这是因为调用`__repr__()`只返回整个`queryset`的一部分。

##### 3.2.2.3.12. 使用 Q 对象的复杂查询

关键字参数查询，比如在`in filter()`中，是`AND`在一起的。如果需要执行更复杂的查询（例如，带有`OR`语句），可以使用`Q`对象。
`Q`对象（`django.db.models.Q`）是用来封装关键字参数集合的对象。这些关键字参数在前文的“字段查找”中指定。

例如，这个`Q`对象封装了一个`LIKE`查询：

```python
from django.db.models import Q
Q(question__startswith='What')
```

可以使用`&`和`|`操作符组合`Q`对象。当一个操作符用于两个`Q`对象时，它会产生一个新的`Q`对象。
例如，这个语句生成一个`Q`对象，它表示两个`question_startswith`查询中的`OR`：

```python
Q(question__startswith='Who') | Q(question__startswith='What')
```

这相当于下面的SQL WHERE子句：

```sql
WHERE question LIKE 'Who%' OR question LIKE 'What%'
```

您可以通过将`Q`对象与`&`和`|`操作符组合起来并使用括号分组来组合任意复杂度的语句。此外，`Q`对象可以使用`~`运算符进行否定，允许将普通查询和否定(`NOT`)查询组合在一起进行组合查找：

```python
Q(question__startswith='Who') | ~Q(pub_date__year=2005)
```

每个接受关键字参数的查找函数（例如`filter()`、`exclude()`、`get()`）也可以作为位置(非命名)参数传递一个或多个`Q`对象。如果您为一个查找函数提供多个`Q`对象参数，那么这些参数将同时被`AND`。例如：

```python
Poll.objects.get(
    Q(question__startswith='Who'),
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
)
```

大致翻译成SQL：

```sql
SELECT * from polls WHERE question LIKE 'Who%' AND (pub_date = '2005-05-02' OR pub_date = '2005-05-06')
```

查找函数可以混合使用`Q`对象和关键字参数。提供给查找函数的所有参数(无论是关键字参数还是Q对象)都是`AND`在一起的。但是，如果提供了一个`Q`对象，它必须在任何关键字参数的定义之前。例如：

```python
Poll.objects.get(
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6)),
    question__startswith='Who',
)
```

将是一个有效的查询，相当于前面的示例。但是：

```python
# INVALID QUERY
Poll.objects.get(
    question__startswith='Who',
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))
)
```

是无效的。


##### 3.2.2.3.13. 比较对象

要比较两个模型实例，只需使用标准 Python 比较操作符，双等号：`==`。在幕后，比较两个模型的主键值。

使用上面的条目示例，下面两个语句是等价的：

```bash
>>> some_entry == other_entry
>>> some_entry.id == other_entry.id
```

如果模型的主键不称为`id`，则没有问题。比较总是使用主键，不管它叫什么。例如，如果一个模型的主键字段名为`name`，这两个语句是等价的：

```bash
>>> some_obj == other_obj
>>> some_obj.name == other_obj.name
```

##### 3.2.2.3.14. 删除对象

方便地将`delete`方法命名为`delete()`。该方法立即删除对象，并返回被删除的对象数量和每个对象类型的删除数量的字典。例子:

```bash
>>> e.delete()
(1, {'weblog.Entry': 1})
```

您还可以批量删除对象。每个`QuerySet`都有一个`delete()`方法，该方法删除`QuerySet`的所有成员。

例如，这将删除所有具有`pub_date`年份为2005的条目对象:

```bash
>>> Entry.objects.filter(pub_date__year=2005).delete()
(5, {'webapp.Entry': 5})
```

请记住，这将在任何可能的情况下完全在SQL中执行，因此在此过程中不必调用单个对象实例的`delete()`方法。如果你提供一个自定义的模型类的`delete()`方法,希望确保它被调用时,您将需要“手动”删除该模型的实例(例如,通过遍历`QuerySet`和单独每个对象上调用`delete()`),而不是使用`QuerySet`批量`delete()`方法。

当 Django 删除一个对象时，默认情况下它模拟`DELETE`级联上的SQL约束的行为——换句话说，任何具有指向要删除对象的外键的对象都将随之删除。例如:

```bash
b = Blog.objects.get(pk=1)
# 这将删除该 Blog 及其全部 Entry 对象
b.delete()
```

这个级联行为可以通过外键的`on_delete`参数定制。

请注意，`delete()`是惟一没有在管理器本身上公开的`QuerySet`方法。这是一种安全机制，可以防止意外请求`Entry.objects.delete()`和删除所有条目。

如果你想删除所有对象，那么你必须显式地请求一个完整的查询集:

```bash
Entry.objects.all().delete()
```

##### 3.2.2.3.15. 拷贝模型实例

虽然没有用于复制模型实例的内置方法，但是可以通过复制所有字段值轻松创建新实例。在最简单的情况下，可以将`pk`设为`None`。以我们的`blog`为例：

```bash
blog = Blog(name='My blog', tagline='Blogging is easy')
blog.save() # blog.pk == 1
blog.pk = None
blog.save() # blog.pk == 2
```

如果使用继承，事情会变得更加复杂。考虑博客的一个子类：

```bash
class ThemeBlog(Blog):
theme = models.CharField(max_length=200)
django_blog = ThemeBlog(name='Django', tagline='Django is easy', theme='python')
django_blog.save() # django_blog.pk == 3
```

由于继承的工作原理，必须将`pk`和`id`都设置为`None`:

```bash
django_blog.pk = None
django_blog.id = None
django_blog.save() # django_blog.pk == 4
```

这个过程不会复制不属于模型数据库表的关系。例如，`Entry`有一个`ManyToManyField`指向`Author`。复制一个条目后，必须为新条目设置多对多关系:

```bash
entry = Entry.objects.all()[0] # some previous entry
old_authors = entry.authors.all()
entry.pk = None
entry.save()
entry.authors.set(old_authors)
```

对于`OneToOneField`，必须复制相关对象并将其分配给新对象的字段，以避免违反一对一的惟一约束。例如，假设条目已经复制如下:

```bash
detail = EntryDetail.objects.all()[0]
detail.pk = None
detail.entry = entry
detail.save()
```

##### 3.2.2.3.16. 一次更新多个对象

有时，您希望为`QuerySet`中的所有对象将字段设置为特定值。您可以使用`update()`方法来实现这一点。例如:

```bash
# Update all the headlines with pub_date in 2007.
Entry.objects.filter(pub_date__year=2007).update(headline='Everything is the same')
```

您只能使用此方法设置非关系字段和`ForeignKey`字段。若要更新非关系字段，请将新值作为常量提供。要更新`ForeignKey`字段，请将新值设置为要指向的新模型实例。例如:

```bash
>>> b = Blog.objects.get(pk=1)
# Change every Entry so that it belongs to this Blog.
>>> Entry.objects.all().update(blog=b)
```

`update()`方法立即应用，并返回与查询匹配的行数（如果某些行已经具有新值，则可能不等于更新的行数）。更新`QuerySet`的唯一限制是它只能访问一个数据库表：模型的主表。您可以基于相关字段进行筛选，但是您只能更新模型主表中的列。例子：

```bash
>>> b = Blog.objects.get(pk=1)
# Update all the headlines belonging to this Blog.
>>> Entry.objects.select_related().filter(blog=b).update(headline='Everything is the
˓→same')
```

注意`update()`方法被直接转换为SQL语句。这是一个用于直接更新的批量操作。它不会在模型上运行任何`save()`方法，也不会发出`pre_save`或`post_save`信号(这是调用`save()`的结果)，也不会支持`auto_now`字段选项。如果希望将每一项保存在`QuerySet`中，并确保在每个实例上调用`save()`方法，则不需要任何特殊函数来处理它。只需循环它们并调用`save()`:

```bash
for item in my_queryset:
    item.save()
```

对`update`的调用还可以使用`F`表达式根据模型中另一个字段的值更新一个字段。

这对于基于计数器当前值递增计数器特别有用。例如，要增加博客中每个条目的`pingback`计数:

```bash
>>> Entry.objects.all().update(n_pingbacks=F('n_pingbacks') + 1)
```

但是，与`filter`和`exclude`子句中的`F()`对象不同，在更新中使用`F()`对象时不能引入连接——只能引用被更新模型的本地字段。如果您试图引入一个与`F()`对象的连接，将会引发一个`FieldError`:

```bash
# This will raise a FieldError
>>> Entry.objects.update(headline=F('blog__name'))
```

##### 3.2.2.3.17. 相关对象

当您在模型中定义关系时（比如`ForeignKey`、`OneToOneField`、`ManyToManyField`）该模型的实例将有一个方便的 API 来访问相关对象。

例如，使用前述模型，`Entry`对象`e`可以通过访问`Blog`属性`e.Blog`来获得其关联的`Blog`对象。(在幕后，此功能由 Python 描述符实现。这对你来说并不重要，但我们在这里为好奇的人指出来。)

Django 还为关系的“另一边”创建 API 访问器——从相关模型到定义关系的模型的链接。例如，`Blog`对象`b`可以通过`entry_set`属性`b.entry_set.all()`访问所有相关`Entry`对象的列表。

###### 3.2.2.3.17.1. 一对多关系

###### 3.2.2.3.17.2. 前向

如果一个模型有一个外键，那么该模型的实例将可以通过模型的一个简单属性访问相关(外)对象。

```bash
>>> e = Entry.objects.get(id=2)
>>> e.blog # Returns the related Blog object.
```

您可以通过外键属性获取和设置。正如您所期望的，直到调用`save()`，外键的更改才会保存到数据库中。例子:

```bash
>>> e = Entry.objects.get(id=2)
>>> e.blog = some_blog
>>> e.save()
```

如果一个`ForeignKey`字段有`null=True`设置(即，它允许空值)，您可以分配`None`来删除关系。例子:

```bash
>>> e = Entry.objects.get(id=2)
>>> e.blog = None
>>> e.save() # "UPDATE blog_entry SET blog_id = NULL ...;"
```

第一次正向访问相关对象时缓存对一对多关系，后续对同一对象实例上的外键的访问基于缓存。例子:

```bash
>>> e = Entry.objects.get(id=2)
>>> print(e.blog) # 命中数据库以检索
>>> print(e.blog) # 不命中数据库，使用缓存
```

注意，`select_related()`、`QuerySet`方法递归地预先填充了所有一对多关系的缓存。例子:

```bash
>>> e = Entry.objects.select_related().get(id=2)
>>> print(e.blog) # 不命中数据库，使用缓存
>>> print(e.blog) # 不命中数据库，使用缓存
```

###### 3.2.2.3.17.3. 后向

如果一个模型有一个`ForeignKey`，那么外键模型的实例访问`Manager`将返回第一个模型的所有实例。默认情况下，这个管理器名为`FOO_set`，其中`FOO`是源模型名，小写。该管理器返回`queryset`，可以按照上面“检索对象”一节的描述对其进行筛选和操作。

```bash
>>> b = Blog.objects.get(id=1)
>>> b.entry_set.all() # Returns all Entry objects related to Blog.
# b.entry_set is a Manager that returns QuerySets.
>>> b.entry_set.filter(headline__contains='Lennon')
>>> b.entry_set.count()
```

您可以通过在`ForeignKey`定义中设置`related_name`参数来覆盖`FOO_set`名称。例如，如果`Entry`模型更改为`blog = ForeignKey(Blog, on_delete=models.CASCADE,related_name='entries')`，上面的示例代码如下:

```bash
>>> b = Blog.objects.get(id=1)
>>> b.entries.all() # Returns all Entry objects related to Blog.
# b.entries is a Manager that returns QuerySets.
>>> b.entries.filter(headline__contains='Lennon')
>>> b.entries.count()
```

###### 3.2.2.3.17.4. 使用定制反向管理器

默认情况下，用于反向关系的`RelatedManager`是该模型的默认管理器的子类。如果你想为一个给定的查询指定一个不同的管理器，你可以使用以下语法:

```python
from django.db import models

class Entry(models.Model):
    #...
    objects = models.Manager() # Default Manager
    entries = EntryManager() # Custom Manager
    
b = Blog.objects.get(id=1)
b.entry_set(manager='entries').all()
```

如果`EntryManager`在它的`get_queryset()`方法中执行缺省过滤器，该过滤器将应用于`all()`调用。

当然，指定自定义反向管理器还允许您调用它的自定义方法:

```bash
b.entry_set(manager='entries').is_published()
```

###### 3.2.2.3.17.5. 处理相关对象的其他方法

除了上面“检索对象”中定义的`QuerySet`方法之外，外键管理器还有用于处理相关对象集的其他方法。下面是它们的概要，完整的细节见API。

**`add(obj1, obj2, ...)`** 将指定的模型对象添加到相关的对象集中。

**`create(**kwargs)`** 创建一个新对象，保存它并将其放入相关的对象集中。返回新创建的对象。

**`remove(obj1, obj2, ...)`** 从相关对象集中移除指定的模型对象。

**`clear()`** 从相关对象集中移除所有对象。

**`set(objs)`** 替换相关对象集。

要分配相关集合的成员，请在对象迭代器上使用`set()`方法。例如，如果`e1`和`e2`是`Entry`实例:

```bash
b = Blog.objects.get(id=1)
b.entry_set.set([e1, e2])
```

如果`clear()`方法可用，任何已存在的对象将被从`entry_set`删除，在所有对象（一个`list`）被添加到`iterable`之前。如果`clear()`方法不可用,`iterable`中的所有对象将被添加，不会删除任何现有的元素。

本节中描述的每个“反向”操作都会对数据库产生直接影响。每一次添加、创建和删除都会立即自动保存到数据库中。

###### 3.2.2.3.17.6. 多对多关系

多对多关系的两端都可以获得对另一端的自动 API 访问。该 API 的工作原理类似于上面的“向后”一对多关系。

一个不同之处在于属性命名：定义`ManyToManyField`的模型使用该字段本身的属性名，而“反向”模型使用原始模型的小写模型名，加上`_set`(就像反向一对多关系一样)。

一个例子使这个更容易理解:

```bash
e = Entry.objects.get(id=3)
e.authors.all() # 返回此Entry的所有Author对象
e.authors.count()
e.authors.filter(name__contains='John')

a = Author.objects.get(id=5)
a.entry_set.all() # 返回此Author的所有Entry对象
```

像`ForeignKey`一样，`ManyToManyField`可以指定`related_name`。上例中，如果`Entry`中的`ManyToManyField`有指定的`related_name='entries'`，则每个`Author`实例将有一个`entries`属性，而非`entry_set`。

与一对多关系的另一个区别是，除了模型实例之外，多对多关系上的`add()`、`set()`和`remove()`方法还接受主键值。例如，如果`e1`和`e2`是`Entry`实例，那么这些`set()`调用的工作原理是相同的:

```bash
a = Author.objects.get(id=5)
a.entry_set.set([e1, e2])
a.entry_set.set([e1.pk, e2.pk])
```

###### 3.2.2.3.17.7. 一对一关系

一对一关系非常类似于多对一关系。如果您在模型上定义了一个`OneToOneField`，那么该模型的实例将可以通过模型的一个简单属性访问相关对象。

```python
class EntryDetail(models.Model):
    entry = models.OneToOneField(Entry, on_delete=models.CASCADE)
    details = models.TextField()

ed = EntryDetail.objects.get(id=2)
ed.entry # Returns the related Entry object.
```

不同之处在于“反向”查询。一对一关系中的相关模型也可以访问管理器对象，但该管理器表示单个对象，而不是对象的集合:

```bash
e = Entry.objects.get(id=2)
e.entrydetail # returns the related EntryDetail object
```

如果没有为这个关系分配对象，Django 将引发一个`DoesNotExist`异常。

实例可以像分配正向关系一样分配给反向关系:

```bash
e.entrydetail = ed
```

###### 3.2.2.3.17.8. 向后关系是如何实现的

其他对象关系映射器要求您在两侧定义关系。Django 开发人员认为这违反了DRY（Don’t Repeat Yourself）原则，所以 Django 只要求您在一端定义关系。

但是，考虑到模型类在加载其他模型类之前不知道哪些模型类与它相关，这怎么可能呢？

答案就在`app registry`中。Django 启动时，它导入`INSTALLED_APPS`中列出的每个应用程序，然后导入每个应用程序中的`models`模块。每当创建一个新的模型类时，Django 都会向任何相关的模型添加向后关系。如果还没有导入相关模型，Django 将跟踪这些关系，并在最终导入相关模型时添加它们。

因此，在`INSTALLED_APPS`中列出的应用程序中定义所使用的所有模型尤为重要。否则，反向关系可能无法正常工作。

###### 3.2.2.3.17.9. 查询相关对象

相关对象的查询遵循与普通值字段的查询相同的规则。在为查询指定要匹配的值时，可以使用对象实例本身，也可以使用对象的主键值。

例如，如果您有一个`id=5`的`Blog`对象`b`，那么以下三个查询将是相同的：

```bash
Entry.objects.filter(blog=b) # Query using object instance
Entry.objects.filter(blog=b.id) # Query using id from instance
Entry.objects.filter(blog=5) # Query using id directly
```

##### 3.2.2.3.18. 回到原始 SQL

如果您发现自己需要编写一个对于 Django 的数据库映射程序来说太复杂而无法处理的 SQL 查询，那么您可以重新使用手工编写 SQL 。Django 有两个用于编写原始 SQL 查询的选项，参见《执行原始SQL》。

最后，需要注意的是 Django 数据库层只是数据库的一个接口。您可以通过其他工具、编程语言或数据库框架访问数据库，您的数据库没有任何特定于 django 的东西。

### 3.2.3. 聚合

前节描述了使用 Django 查询进行创建、检索、更新和删除单个对象的方法。但是，有时需要检索通过汇总或聚合对象集合派生的值。本节描述了使用 Django 查询生成和返回聚合值的方法。

本节将参考以下模型。这些模型用于跟踪一系列在线书店的库存:

```python
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)
    age = models.IntegerField()

class Publisher(models.Model):
    name = models.CharField(max_length=300)

class Book(models.Model):
    name = models.CharField(max_length=300)
    pages = models.IntegerField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    rating = models.FloatField()
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher, on_delete=models.CASCADE)
    pubdate = models.DateField()

class Store(models.Model):
    name = models.CharField(max_length=300)
    books = models.ManyToManyField(Book)
```

#### 3.2.3.1. 备忘单

这里是如何做常见的聚合查询:

```bash
# 书籍总数
>>> Book.objects.count()
2452

# 书籍总数， publisher=BaloneyPress
>>> Book.objects.filter(publisher__name='BaloneyPress').count()
73

# 所有书籍的平均价格。
>>> from django.db.models import Avg
>>> Book.objects.all().aggregate(Avg('price'))
{'price__avg': 34.35}

# Max price across all books.
>>> from django.db.models import Max
>>> Book.objects.all().aggregate(Max('price'))
{'price__max': Decimal('81.20')}

# 价格最高的书和所有书的平均价格之间的差异。
>>> from django.db.models import FloatField
>>> Book.objects.aggregate(
... price_diff=Max('price', output_field=FloatField()) - Avg('price'))
{'price_diff': 46.85}

# 以下所有查询都涉及穿越 Book<->Publisher 后向外键关系

# 每个出版者都有一个"num_books"属性作为图书计数
>>> from django.db.models import Count
>>> pubs = Publisher.objects.annotate(num_books=Count('book'))
>>> pubs
<QuerySet [<Publisher: BaloneyPress>, <Publisher: SalamiPress>, ...]>
>>> pubs[0].num_books
73

# 每个出版商，都有一个独立的图书计数，其评分在5分以上和5分以下
>>> from django.db.models import Q
>>> above_5 = Count('book', filter=Q(book__rating__gt=5))
>>> below_5 = Count('book', filter=Q(book__rating__lte=5))
>>> pubs = Publisher.objects.annotate(below_5=below_5).annotate(above_5=above_5)
>>> pubs[0].above_5
23
>>> pubs[0].below_5
12

# 排名前五的出版商，按图书数量排序。
>>> pubs = Publisher.objects.annotate(num_books=Count('book')).order_by('-num_books
˓→')[:5]
>>> pubs[0].num_books
1323
```

#### 3.2.3.2. 在查询集上生成聚合

Django 提供了两种生成聚合的方法。第一种方法是在整个 QuerySet 上生成汇总值。例如，假设您想计算所有可供销售的图书的平均价格。Django 的查询语法提供了一种描述所有书籍集的方法:

```bash
>>> Book.objects.all()
```

我们需要的是一种方法来计算属于这个`QuerySet`的对象上的汇总值。这是通过在`QuerySet`中添加一个`aggregate()`子句来实现的:

```bash
>>> from django.db.models import Avg
>>> Book.objects.all().aggregate(Avg('price'))
{'price__avg': 34.35}
```

在本例中，`all()`是多余的，因此可以简化为:

```bash
>>> Book.objects.aggregate(Avg('price'))
{'price__avg': 34.35}
```

aggregate()子句的参数描述了我们想要计算的聚合值——在本例中，是`Book`模型上`price`字段的平均值。

`aggregate()`是`QuerySet`的一个终端子句，当调用它时，返回一个“名称-值”对字典。名称是聚合值的标识符，该值是计算得到的聚合。该名称由字段名和聚合函数名自动生成。如果您想手动指定聚合值的名称，可以在指定聚合子句时提供该名称:

```bash
>>> Book.objects.aggregate(average_price=Avg('price'))
{'average_price': 34.35}
```

如果希望生成多个聚合，只需向`aggregate()`子句添加另一个参数。所以，如果我们还想知道所有书籍的最高和最低价格，我们会发出查询:

```bash
>>> from django.db.models import Avg, Max, Min
>>> Book.objects.aggregate(Avg('price'), Max('price'), Min('price'))
{'price__avg': 34.35, 'price__max': Decimal('81.20'), 'price__min': Decimal('12.99')}
```

#### 3.2.3.3. 为查询集中的每一项生成聚合值

生成摘要值的第二种方法是为`QuerySet`中的每个对象生成独立的摘要。

例如，如果您正在检索图书列表，您可能想知道有多少作者为每本书贡献了内容。每一本书都与作者有多对多的关系，我们想为`QuerySet`中的每本书总结这种关系。

可以使用`annotate()`子句生成每个对象的摘要。当指定了`annotate()`子句时，`QuerySet`中的每个对象都将使用指定的值进行注释。

这些注释的语法与用于`aggregate()`子句的语法相同。每个`annotate()`的参数都描述要计算的聚合。例如，用作者的数量来标注书籍:

```bash
# 建立查询
>>> from django.db.models import Count
>>> q = Book.objects.annotate(Count('authors'))

# 第一个对象
>>> q[0]
<Book: The Definitive Guide to Django>
>>> q[0].authors__count
2

# 第二个对象
>>> q[1]
<Book: Practical Django Projects>
>>> q[1].authors__count
1
```

与`aggregate()`一样，`annotate()`的名称自动派生自聚合函数的名称和被聚合字段的名称。您可以通过在指定`annotate()`时提供别名来覆盖此默认名称:

```bash
>>> q = Book.objects.annotate(num_authors=Count('authors'))
>>> q[0].num_authors
2
>>> q[1].num_authors
1
```

与`aggregate()`不同，`annotate()`不是终端子句，`annotate()`子句的输出是个`QuerySet`，该`QuerySet`可以被其它任意选项修改，比如`filter()`、`order_by()`，甚至其它调用`annotate()`（的子句）。

##### 3.2.3.3.1. 结合多个聚合

将多个聚合与`annotate()`组合将`产生错误的结果`，因为连接被子查询代替:

```bash
>>> book = Book.objects.first()
>>> book.authors.count()
2
>>> book.store_set.count()
3
>>> q = Book.objects.annotate(Count('authors'), Count('store'))
>>> q[0].authors__count
6
>>> q[0].store__count
6
```

对于大多数聚合，没有办法避免这个问题，但是，`Count`聚合有一个`distinct`参数，可能会有所帮助:

```bash
>>> q = Book.objects.annotate(Count('authors', distinct=True), Count('store', distinct=True))
>>> q[0].authors__count
2
>>> q[0].store__count
3
```

> 如果有疑问，检查SQL查询!
>
> 为了了解查询中发生了什么，请考虑检查`QuerySet`的`query`属性。

#### 3.2.3.4. 连接与聚合

到目前为止，我们已经处理了属于被查询模型的字段的聚合。然而，有时您想要聚合的值将属于与您正在查询的模型相关的模型。

当指定要在聚合函数中聚合的字段时，Django 将允许您使用与在过滤器中引用相关字段时相同的双下划线表示法。Django 将处理检索和聚合相关值所需的任何表连接。

例如，要查找每个书店提供的图书的价格范围，可以使用注释（`annotations`）:

```bash
>>> from django.db.models import Max, Min
>>> Store.objects.annotate(min_price=Min('books__price'), max_price=Max('books__price'))
```

这告诉 Django 检索商店模型，（通过多对多关系）与图书模型连接，并在图书模型的`price`字段上聚合，以生成最小值和最大值。

同样的规则也适用于`aggregate()`子句。如果你想知道在任何一家书店出售的任何一本书的最低和最高价格，你可以使用以下组合:

```bash
>>> Store.objects.aggregate(min_price=Min('books__price'), max_price=Max('books__price'))
```

连接链可以根据您的需要深入。例如，要提取任何一本书最年轻作者的年龄，您可以发出以下查询:

```bash
>>> Store.objects.aggregate(youngest_age=Min('books__authors__age'))
```

##### 3.2.3.4.1. 跟随向后关系

与跨越关系的查找类似，模型或与您正在查询的模型相关的模型字段上的聚合和注释可以包括遍历“反向”关系。这里也使用了相关模型的小写名称和双下划线。

例如，我们可以要求所有出版商，用他们各自的图书总库存计数器进行注释(注意我们如何使用`book`指定`Publisher -> Book`反向外键跳跃):

```bash
>>> from django.db.models import Avg, Count, Min, Sum
>>> Publisher.objects.annotate(Count('book'))
```

(结果`QuerySet`中的每个发布者都有一个名为`book_count`的额外属性。)

我们还可以向每一位出版商检索其中一本最古老的书:

```bash
>>> Publisher.objects.aggregate(oldest_pubdate=Min('book__pubdate'))
```

(生成的字典将有一个名为`oldest_pubdate`的键。如果没有指定这样的别名，它将是相当长的`book_pubdate_min`。)

这不仅适用于外键。它也适用于多对多关系。例如，我们可以要求每个作者，考虑到作者(合作)所写的所有书籍，用总页数来注释(注意我们如何使用`book`来指定`Author -> Book`反向多对多跳跃):

```bash
>>> Author.objects.annotate(total_pages=Sum('book__pages'))
```

(结果`QuerySet`中的每个作者都有一个名为`total_pages`的额外属性。如果没有指定这样的别名，那么它将是相当长的`book_pages_sum`。)

或要求我们存档的所有作者所写书籍的平均评分:

```bash
>>> Author.objects.aggregate(average_rating=Avg('book__rating'))
```

(生成的字典将有一个名为`average_rating`的键。如果没有指定这样的别名，它将是相当长的`book_rating_avg`。)

#### 3.2.3.5. 聚合和其它 QuerySet 子句
##### 3.2.3.5.1. filter() 与 exclude()

聚合还可以用于过滤器。应用于普通模型字段的任何`filter()`或`exclude()`对用于聚合的对象都会有相同的约束效果。

当与`annotate()`子句一起使用时，过滤器的作用是约束为其计算注释的对象。例如，您可以使用以下查询生成以`Django`开头的所有图书的带注释列表:

```bash
>>> from django.db.models import Avg, Count
>>> Book.objects.filter(name__startswith="Django").annotate(num_authors=Count('authors'))
```

当与`aggregate()`子句一起使用时，过滤器的作用是约束计算聚合的对象。例如，您可以使用以下查询生成标题以`Django`开头的所有图书的平均价格:

```bash
>>> Book.objects.filter(name__startswith="Django").aggregate(Avg('price'))
```

###### 3.2.3.5.1.1. 在注释（`annotations`）上过滤

还可以过滤带注释的值。注释的别名可以在`filter()`和`exclude()`子句中使用，方法与任何其他模型字段相同。

例如，要生成包含多个作者的图书列表，可以发出以下查询:

```bash
>>> Book.objects.annotate(num_authors=Count('authors')).filter(num_authors__gt=1)
```

该查询生成一个带注释的结果集，然后根据该注释生成一个过滤器。

如果需要两个带有单独过滤器的注释，可以对任何聚合使用`filter`参数。例如，生成一个拥有高评价书籍数量的作者列表:

```bash
>>> highly_rated = Count('books', filter=Q(books__rating__gte=7))
>>> Author.objects.annotate(num_books=Count('books'), highly_rated_books=highly_rated)
```

结果集中的每个`Author`将拥有`num_books`与`highly_rated_books`属性。

> **在`filter`和`QuerySet.filter()`之间进行选择**
>
> 避免在单个注释或聚合中使用`filter`参数。使用`QuerySet.filter()`来排除行更有效。聚合的`filter`参数仅在对具有不同条件的相同关系使用两个或多个聚合时才有用。

`filter`参数被添加到聚合中。

###### 3.2.3.5.1.2. `annotate()`与`filter()`子句的顺序

在开发同时包含`annotate()`、`filter()`子句的复杂查询时，要特别注意子句应用于`QuerySet`的顺序。

当将`annotate()`子句应用于查询时，将根据查询的状态计算注释，直到请求注释为止。这实际上意味着`filter()`和`annotate()`不是交替操作。

假定:

- 出版商A有两本书的评级分别为4和5。

- 出版商B有两本书的评级分别为1和4。

- 出版商C有一本书的评级为1。

下面是一个计数汇总的例子:

```bash
>>> a, b = Publisher.objects.annotate(num_books=Count('book', distinct=True)).filter(book__rating__gt=3.0)
>>> a, a.num_books
(<Publisher: A>, 2)
>>> b, b.num_books
(<Publisher: B>, 2)

>>> a, b = Publisher.objects.filter(book__rating__gt=3.0).annotate(num_books=Count('book'))
>>> a, a.num_books
(<Publisher: A>, 2)
>>> b, b.num_books
(<Publisher: B>, 1)
```

这两个查询都返回一个出版商列表，其中至少有一本书的评级超过3.0，因此不包括出版商C。

在第一个查询中，注释在过滤器之前，所以过滤器对注释没有影响。`distinct=True`是避免查询错误所必需的。

第二个查询计算每个出版商的评分超过3.0的图书数量。过滤器位于注释之前，因此过滤器在计算注释时约束考虑的对象。

下面是`Avg`聚合的另一个例子:

```bash
>>> a, b = Publisher.objects.annotate(avg_rating=Avg('book__rating')).filter(book__rating__gt=3.0)
>>> a, a.avg_rating
(<Publisher: A>, 4.5) # (5+4)/2
>>> b, b.avg_rating
(<Publisher: B>, 2.5) # (1+4)/2

>>> a, b = Publisher.objects.filter(book__rating__gt=3.0).annotate(avg_rating=Avg('book__rating'))
>>> a, a.avg_rating
(<Publisher: A>, 4.5) # (5+4)/2
>>> b, b.avg_rating
(<Publisher: B>, 4.0) # 4/1 (评级为1的被排除)
```

第一个查询要求对至少有一本评级超过3.0的图书的出版商的所有图书的平均评级。第二个查询要求出版商图书的平均评分，仅针对那些评分超过3.0的图书。

很难凭直觉知道 ORM 将如何将复杂的查询集转换成 SQL 查询，因此当有疑问时，使用`str(queryset.query)`检查 SQL 并编写大量测试。

##### 3.2.3.5.2. `order_by()`

注释可以用作排序的基础。当您定义`order_by()`子句时，您提供的聚合可以引用查询中作为`annotate()`子句的一部分定义的任何别名。

例如，要按对本书有贡献的作者的数量订购一组查询书籍，可以使用以下查询:

```bash
>>> Book.objects.annotate(num_authors=Count('authors')).order_by('num_authors')
```

##### 3.2.3.5.3. `values()`

通常，注释是基于每个对象生成的：一个带注释的`QuerySet`将为原始`QuerySet`中的每个对象返回一个结果。但是，当使用`values()`子句约束结果集中返回的列时，计算注释的方法略有不同。不是为原始`QuerySet`中的每个结果返回带注释的结果，而是根据`values()`子句中指定的字段的惟一组合对原始结果进行分组。然后为每个唯一组提供一个注释，注释在组的所有成员上计算。

例如，考虑一个`author`查询，它试图找出每个作者所写书籍的平均评分:

```bash
>>> Author.objects.annotate(average_rating=Avg('book__rating'))
```

这将为数据库中的每个作者返回一个结果，并使用它们的平均图书评级进行注释。

但是，如果使用`values()`子句，结果会略有不同:

```bash
>>> Author.objects.values('name').annotate(average_rating=Avg('book__rating'))
```

在本例中，作者将按名称进行分组，因此您将只得到每个惟一作者名称的注释结果。这意味着如果您有两个同名的作者，他们的结果将在查询的输出中合并为一个结果，平均值将计算为两位作者所写书籍的平均值。

###### 3.2.3.5.3.1. `annotate()`与`values()`子句的顺序

与`filter()`子句一样，将`annotate()`和`values()`子句应用于查询的顺序也很重要。如果`values()`子句位于`annotate()`之前，则将使用`values()`子句描述的分组计算注释。

但是，如果`annotate()`子句位于`values()`子句之前，则会在整个查询集中生成注释。

例如，如果我们颠倒前面例子中的`values()`和`annotate()`子句的顺序:

```bash
>>> Author.objects.annotate(average_rating=Avg('book__rating')).values('name','average_rating')
```

这将为每个作者生成一个惟一的结果。但是，在输出数据中只返回作者的名字和`average_rating`注释。

您还应该注意，`average_rating`已经显式地包含在要返回的值列表中。这是必需的，因为`values()`和`annotate()`子句的顺序不同。

如果`values()`子句位于`annotate()`子句之前，则任何注释都会自动添加到结果集中。但是，如果`values()`子句应用于`annotate()`子句之后，则需要显式地包含聚合列。

###### 3.2.3.5.3.2. 按缺省顺序或`order_by()`进行迭代

在`queryset`的`order_by()`部分中提到的字段（或者在模型的默认顺序中使用的字段）在选择输出数据时使用，即使在`values()`调用中没有指定它们。这些额外的字段用于将`like`结果分组在一起，它们可以使其他相同的结果行看起来是分开的。这一点在计算时尤其明显。

举个例子，假设你有这样一个模型:

```python
from django.db import models

class Item(models.Model):
name = models.CharField(max_length=10)
data = models.IntegerField()
class Meta:
ordering = ["name"]
```

这里的重要部分是`name`字段的默认顺序。如果你想计算每个不同的数据值出现的次数，你可以这样做:

```bash
# 警告:不太正确!
Item.objects.values("data").annotate(Count("id"))
```

它将根据`Item`对象的公共`data`值对其进行分组，然后对每个组计算`id`值的数量。只是它不会完全奏效。默认的按`name`排序也将在分组中发挥作用，因此该查询将按不同的`(data, name)`对进行分组，这不是您想要的。相反，您应该构造这个`queryset`:

```bash
Item.objects.values("data").annotate(Count("id")).order_by()
```

清除查询中的任何顺序。您还可以按`data`排序，而不会产生任何有害影响，因为它已经在查询中发挥了作用。

这种行为和`distinct()`的一般规则是一样的：通常你不想额外列在结果中发挥作用。因此清除命令,或至少确保它是仅限于你在`values()`调用中选择的领域。

> 注意：您可能会合理地询问 Django 为什么不为您删除无关的列。主要原因是与`distinct()`和其他地方的一致性：Django 从来没有删除您指定的排序约束（我们不能更改那些其他方法的行为，因为这会违反我们的 API 稳定策略）。

##### 3.2.3.5.4. 聚合注释

您还可以根据注释的结果生成聚合。定义`aggregate()`子句时，提供的聚合可以引用查询中作为`annotate()`子句的一部分定义的任何别名。

例如，如果您想计算每本书的平均作者数量，那么您首先使用作者数量对图书集进行注释，然后聚合该作者数量，并引用`annotation`字段:

```bash
>>> from django.db.models import Avg, Count
>>> Book.objects.annotate(num_authors=Count('authors')).aggregate(Avg('num_authors'))
{'num_authors__avg': 1.66}
```

### 3.2.4. 查找

web应用程序的一个常见任务是使用用户输入在数据库中搜索一些数据。在一个简单的例子中，这可以是按类别过滤对象列表。更复杂的用例可能需要使用加权、分类、突出显示、多种语言等等进行搜索。本文档解释了一些可能的用例和您可以使用的工具。

我们将引用在《进行查询》中使用的相同模型。

#### 3.2.4.1. 用例

##### 3.2.4.1.1. 标准文本查询

基于文本的字段有一个简单匹配操作的选择。例如，您可能希望允许查找这样的作者:

```bash
>>> Author.objects.filter(name__contains='Terry')
[<Author: Terry Gilliam>, <Author: Terry Jones>]
```

这是一个非常脆弱的解决方案，因为它要求用户知道作者姓名的确切子字符串。更好的方法可能是不区分大小写的匹配（`icon`），但这只是稍微好一点。

##### 3.2.4.1.2.数据库高级比较功能

如果您正在使用 PostgreSQL，Django 提供了一系列特定于数据库的工具，允许您使用更复杂的查询选项。其他数据库有不同的工具选择，可能通过插件或用户定义的函数。Django 目前不包含对它们的任何支持。我们将使用 PostgreSQL 中的一些示例来演示数据库可能具有的功能。

> **在其他数据库中搜索**
>
> 所有由 `django.contrib.postgres` 提供的搜索工具完全构建在公共 API （如自定义查找和数据库函数）之上。根据您的数据库，您应该能够构造查询来允许类似的 API。

在上面的例子中，我们确定不区分大小写的查找将更有用。在处理非英语名字时，进一步的改进是使用不重读的比较:

```bash
>>> Author.objects.filter(name__unaccent__icontains='Helen')
[<Author: Helen Mirren>, <Author: Helena Bonham Carter>, <Author: Hélène Joy>]
```

当我们对名称的不同拼写进行匹配时，这会有另一个问题。在这种情况下，我们有一个不对称`though - a`搜索，`Helen`会匹配`Helen`或`Hélène`，但不是相反。另一个选项是使用`trigram_similar`比较，它比较字母序列。

例如：

```bash
>>> Author.objects.filter(name__unaccent__lower__trigram_similar='Hélène')
[<Author: Helen Mirren>, <Author: Hélène Joy>]
```

现在我们有一个不同的问题——`Helena Bonham Carter`这个更长的名字并没有出现，因为它要长得多。三元组搜索考虑三个字母的所有组合，并比较在搜索和源字符串中出现的字母数量。对于较长的名称，源字符串中出现了更多的组合，因此不再认为它是紧密匹配的。

比较函数的正确选择取决于您的特定数据集，例如使用的语言和正在搜索的文本类型。我们看到的所有示例都是在短字符串上，用户很可能在短字符串上输入与源数据相近的内容(通过不同的定义)。

##### 3.2.4.1.3. 基于文件的查找

当您开始考虑大的文本块时，简单的数据库操作是一种过于简单的方法。虽然上面的示例可以看作是对字符串的操作，但是全文搜索查看的是实际的单词。

根据所使用的系统，可能会使用以下一些想法:

- 忽略“stop words”，比如“a”，“the”，“and”。

- 词干化，使“pony”和“ponies”被认为是相似的。

- 根据不同的标准对单词进行加权，比如它们在文本中出现的频率，或者字段的重要性，比如它们出现的标题或关键词。

使用搜索软件有很多选择，其中最突出的是`Elastic`和`Solr`。这些都是完全基于文档的搜索解决方案。要将它们与 Django 模型中的数据一起使用，需要一个层将数据转换为文本文档，包括对数据库`id`的反向引用。当使用该引擎的搜索返回某个文档时，您可以在数据库中查找它。有各种各样的第三方库被设计来帮助这个过程。

###### 3.2.4.1.3.1. PostgreSQL 支持

PostgreSQL 内置了自己的全文搜索实现。虽然不像其他一些搜索引擎那样强大，但是它的优势在于它位于数据库中，因此可以很容易地与其他关系查询(如分类)组合在一起。

`django.contrib.postgres`模块提供了一些帮助程序来执行这些查询。例如，一个简单的查询可能是选择所有提到`cheese`的博客条目:

```bash
>>> Entry.objects.filter(body_text__search='cheese')
[<Entry: Cheese on Toast recipes>, <Entry: Pizza recipes>]
```

你也可以对字段和相关模型的组合进行筛选:

```bash
>>> Entry.objects.annotate(
... search=SearchVector('blog__tagline', 'body_text'),
... ).filter(search='cheese')
[
<Entry: Cheese on Toast recipes>,
<Entry: Pizza Recipes>,
<Entry: Dairy farming in Argentina>,
]
```

### 3.2.5. 管理器

#### 3.2.5.1. 类管理器

`Manager`是向 Django 模型提供数据库查询操作的接口。Django 应用程序中的每个模型至少有一个管理器。

管理器类的工作方式记录在查询中，本文档特别涉及定制管理器行为的模型选项。

##### 3.2.5.1.1. 管理器名称

默认情况下，Django 向每个 Django 模型类添加一个名为`objects`的`Manager`。但是，如果您想使用对象作为字段名，或者您想为管理器使用对象以外的名称，您可以根据每个模型重命名它。要为给定类重命名管理器，请在该模型上定义`model.Manager()`类型的类属性。例如:

```python
from django.db import models

class Person(models.Model):
    #...
    people = models.Manager()
```

使用这个示例模型，`Person.objects`将生成`AttributeError`异常，但是`Person.people.all()`将提供`Person`所有对象的列表。

##### 3.2.5.1.2. 定制管理器

通过扩展基本`Manager`类并在模型中实例化自定义`Manager`，可以在特定模型中使用自定义`Manager`。

您可能希望自定义`Manager`有两个原因：添加额外的管理器方法 和/或 修改管理器返回的初始`QuerySet`。

###### 3.2.5.1.2.1. 添加额外的管理器方法

添加额外的管理器方法是向模型添加“表级”功能的首选方法。

（对于“行级”功能，即，作用于模型对象单个实例的函数，使用模型方法，而不是自定义管理器方法。）

自定义管理器方法可以返回您想要的任何内容。它不必返回`QuerySet`。

例如，这个自定义管理器提供了一个`with_counts()`方法，该方法返回所有`OpinionPoll`对象的列表，每个对象都有一个额外的`num_responses`属性，该属性是聚合查询的结果:

```python
from django.db import models

class PollManager(models.Manager):
    def with_counts(self):
        from django.db import connection
        with connection.cursor() as cursor:
            cursor.execute("""
                SELECT p.id, p.question, p.poll_date, COUNT(*)
                FROM polls_opinionpoll p, polls_response r
                WHERE p.id = r.poll_id
                GROUP BY p.id, p.question, p.poll_date
                ORDER BY p.poll_date DESC""")
            result_list = []
            for row in cursor.fetchall():
                p = self.model(id=row[0], question=row[1], poll_date=row[2])
                p.num_responses = row[3]
                result_list.append(p)
        return result_list

class OpinionPoll(models.Model):
    question = models.CharField(max_length=200)
    poll_date = models.DateField()
    objects = PollManager()

class Response(models.Model):
    poll = models.ForeignKey(OpinionPoll, on_delete=models.CASCADE)
    person_name = models.CharField(max_length=50)
    response = models.TextField()
```

在本例中，您将使用`OpinionPoll.object .with_counts()`返回具有`num_responses`属性的`OpinionPoll`对象列表。

关于这个例子需要注意的另一件事是，`Manager`方法可以访问`self.model`获取它们所附加到的模型类。

###### 3.2.5.1.2.2. 修改管理器的初始查询集

`Manager`的基本`QuerySet`返回系统中的所有对象。例如，使用这个模型:

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
```

语句`Book.objects.all()`将返回数据库中的所有书籍。

通过重写`Manager.get_queryset()`方法，可以覆盖管理器的基本`QuerySet`。

例如，下面的模型有两个管理器——一个返回所有对象，另一个只返回`Roald Dahl`的书:

```python
# 首先，定义Manager子类
class DahlBookManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(author='Roald Dahl')

# 然后，显式地将它挂接到Book模型中
class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    objects = models.Manager() # 缺省manager
    dahl_objects = DahlBookManager() # 自定义manager
```

使用这个示例模型，`Book.objects.all()`将返回数据库中的所有图书，但是`Book.dahl_objects.all()`只返回由`Roald Dahl`编写的图书。

当然，因为`get_queryset()`返回一个`QuerySet`对象，所以您可以在它上面使用`filter()`、`exclude()`和所有其他`QuerySet`方法。所以这些声明都是合法的:

```bash
Book.dahl_objects.all()
Book.dahl_objects.filter(title='Matilda')
Book.dahl_objects.count()
```

这个例子还指出了另一个有趣的技术:在同一个模型上使用多个管理器。您可以将任意多的`Manager()`实例附加到模型上。这是为您的模型定义`filters`的简单方法。

例如：

```python
class AuthorManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(role='A')

class EditorManager(models.Manager):
    def get_queryset(self):
        return super().get_queryset().filter(role='E')

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    role = models.CharField(max_length=1, choices=(('A', _('Author')), ('E', _('Editor'))))
    people = models.Manager()
    authors = AuthorManager()
    editors = EditorManager()
```

这个示例允许您请求`Person.authors.all()`、`Person.editors.all()`和`Person.people.all()`，生成可预测的结果。

###### 3.2.5.1.2.3. 缺省管理器

**`Model._default_manager`**

如果使用自定义`Manager`对象，请注意 Django 遇到的第一个`Manager`（按照它们在模型中定义的顺序）具有特殊的状态。

Django 将类中定义的第一个`Manager`解释为`default Manager`，Django 的几个部分（包括`dumpdata`）将只针对该模型使用该管理器。因此，在选择默认管理器时最好谨慎，以免出现覆盖`get_queryset()`导致无法检索希望使用的对象的情况。

您可以使用`Meta.default_manager_name`指定自定义默认管理器。

如果您正在编写一些必须处理未知模型的代码，例如，在实现通用视图的第三方应用程序中，使用这个管理器(或`_base_manager`)，而不是假设模型有一个`objects`管理器。


###### 3.2.5.1.2.4. 基础管理器

**`Model._base_manager`**

*使用管理器进行相关对象查询*

默认情况下，访问相关对象（例如`choice.question`）时，Django 使用`Model._base_manager`类的实例，而非相关对象的`_default_manager`。这是因为 Django 需要能够检索相关对象，即使它会被缺省管理器过滤掉（因此是不可访问的）。

如果普通的基本管理器类（`Django.db.models.manager`）不适合您的环境，您可以通过设置`Meta.base_manager_name`来告诉 Django 使用哪个类。

在查询相关模型时不使用基本管理器。例如，如果教程中的`Question`模型有一个`deleted`字段和一个使用`deleted=True`过滤实例的基本管理器，那么像`Choice.objects.filter(question__name__startswith='What')`这样的查询集将包含与已删除问题相关的选项。

*不要过滤掉此类管理器子类中的任何结果*

此管理器用于访问与其他模型相关的对象。在这些情况下，Django 必须能够看到它正在获取的模型的所有对象，以便能够检索引用的任何内容。

如果覆盖`get_queryset()`方法并过滤掉任何行，Django 将返回不正确的结果。不要这样做。筛选`get_queryset()`结果的管理器不适合用作基本管理器。


###### 3.2.5.1.2.5. 从管理器调用定制选择集方法

虽然大多数来自标准`QuerySet`的方法都可以直接从`Manager`访问，但在定制`QuerySet`上定义的额外方法，只有在管理器上实现它时，才可以:

```python
class PersonQuerySet(models.QuerySet):
    def authors(self):
        return self.filter(role='A')

    def editors(self):
        return self.filter(role='E')

class PersonManager(models.Manager):
    def get_queryset(self):
        return PersonQuerySet(self.model, using=self._db)

    def authors(self):
        return self.get_queryset().authors()

    def editors(self):
        return self.get_queryset().editors()

class Person(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    role = models.CharField(max_length=1, choices=(('A', _('Author')), ('E', _('Editor'))))
    people = PersonManager()
```

此例允许你通过`Person.people`直接调用`authors()`、`editors()`。

###### 3.2.5.1.2.6. 创建带有选择集方法的管理器

为了替代以上在`QuerySet`和`Manager`中复制方法的方案，`QuerySet.as_manager()`可以用于创建一个`Manager`实例，带有定制`QuerySet`方法的拷贝。

```python
class Person(models.Model):
    ...
    people = PersonQuerySet.as_manager()
```

由`QuerySet.as_manager()`创建的`Manager`实例实际上与前面示例中的`PersonManager`相同。

并不是每个`QuerySet`方法在`Manager`级别都有意义，例如，我们故意阻止将`QuerySet.delete()`方法复制到`Manager`类。

方法按照以下规则复制:

- 默认情况下复制公共方法。

- 默认情况下不会复制私有方法(以下划线开头)。

- 将`queryset_only`属性设置为`False`的方法总是被复制。

- 将`queryset_only`属性设置为`True`的方法永远不会被复制。

例如:

```python
class CustomQuerySet(models.QuerySet):
    # 在Manager、QuerySet均可用
    def public_method(self):
        return

    # 仅于QuerySet可用
    def _private_method(self):
        return

    # 仅于QuerySet可用
    def opted_out_public_method(self):
        return
    opted_out_public_method.queryset_only = True

    # 在Manager、QuerySet均可用
    def _opted_in_private_method(self):
        return
    _opted_in_private_method.queryset_only = False
```

*from_queryset()*

classmethod from_queryset(queryset_class)

对于高级用法，您可能需要一个自定义`Manager`和一个自定义`QuerySet`。您可以通过调用`Manager.from_queryset()`来实现这一点，该函数返回基本管理器的子类和自定义`QuerySet`方法的副本：

```python
class BaseManager(models.Manager):
    def manager_only_method(self):
        return

class CustomQuerySet(models.QuerySet):
    def manager_and_queryset_method(self):
        return

class MyModel(models.Model):
    objects = BaseManager.from_queryset(CustomQuerySet)()
```

您也可以将生成的类存储到一个变量中:

```python
CustomManager = BaseManager.from_queryset(CustomQuerySet)

class MyModel(models.Model):
    objects = CustomManager()
```

###### 3.2.5.1.2.7. 定制管理器与模型继承

Django 是如何处理自定义管理器和模型继承的:

1. 基类中的管理器总是由子类继承，使用 Python 的常规名称解析顺序（子类上的名称覆盖所有其他类；然后是第一个父类的名称，依此类推）。

2. 如果没有在模型 和/或 其父模型上声明管理器，Django 将自动创建对象管理器。

3. 类上的默认管理器是 使用`Meta.default_manager_name`选择的管理器，或者是模型上声明的第一个管理器，或者是第一个父模型的默认管理器。

如果您希望通过抽象基类在一组模型上安装自定义管理器集合，但仍然自定义默认管理器，那么这些规则提供了必要的灵活性。例如，假设您有这样一个基类:

```python
class AbstractBase(models.Model):
    # ...
    objects = CustomManager()

    class Meta:
        abstract = True
```

如果你直接在子类中使用这个，如果你在基类中没有声明管理器，`objects`将是默认的管理器:

```python
class ChildA(AbstractBase):
    # ...
    # 这个类的默认管理器是CustomManager。
    pass
```

如果你想从`AbstractBase`继承，但是提供一个不同的默认管理器，你可以在子类上提供默认管理器:

```python
class ChildB(AbstractBase):
    # ...
    # 显示缺省管理器
    default_manager = OtherManager()
```

这里，`default_manager`是默认值。对象管理器仍然可用，因为它是继承的。它只是没有用作默认值。

最后，对于这个示例，假设您希望向子类添加额外的管理器，但仍然使用`AbstractBase`的默认值。不能在子类中直接添加新管理器，因为这会覆盖默认值，而且还必须显式地包含抽象基类中的所有管理器。解决方案是将额外的管理器放在另一个基类中，并在默认值之后将其引入继承层次结构:

```python
class ExtraManager(models.Model):
    extra_manager = OtherManager()
    class Meta:
        abstract = True

class ChildC(AbstractBase, ExtraManager):
    # ...
    # 缺省管理器是CustomManager
    # 其它管理器通过 "extra_manager" 属性仍然可用
    pass
```

注意，虽然可以在抽象模型上定制管理器，但是不能使用抽象模型调用任何方法。那就是:

```bash
ClassA.objects.do_something()
```

是非法的。而：

```bash
AbstractBase.objects.do_something()
```

将引发异常。这是因为管理器旨在封装用于管理对象集合的逻辑。

由于您不能拥有抽象对象的集合，因此管理它们没有任何意义。

如果您有应用于抽象模型的函数，您应该将该函数放在抽象模型的`staticmethod`或`classmethod`中。

###### 3.2.5.1.2.8. Implementation concerns

无论您向自定义管理器添加什么功能，都必须能够创建管理器实例的浅拷贝；即，以下代码必须工作:

```bash
>>> import copy
>>> manager = MyManager()
>>> my_copy = copy.copy(manager)
```

Django 在某些查询期间生成管理器对象的浅拷贝，如果无法复制管理器，这些查询将失败。

对于大多数自定义管理器来说，这不是问题。如果您只是向管理器添加简单的方法，则不太可能无意中使管理器实例不可复制。但是，如果您正在覆盖管理器对象的`__getattr__`或其他控制对象状态的私有方法，则应该确保不会影响管理器被复制的能力。

### 3.2.6. 执行原始 SQL 请求

Django 提供两个途径执行原始SQL：

- 使用`Manager.raw()`执行原始查询并返回模型实例。

- 或者您可以完全避免模型层并直接执行自定义SQL。

> **在使用原始 SQL 之前研究 ORM**
>
> Django ORM 提供了许多工具，可以在不编写原始SQL的情况下表达查询。例如:
> - QuerySet API非常广泛。
> - 您可以使用许多内置的数据库函数进行注释和聚合。除此之外，你还可以创造自定义查询表达式。
> - 在使用原始SQL之前，先研究ORM。在`django-users`或`#django IRC`频道上询问ORM是否支持你的用例。

> 警告:在编写原始SQL时应该非常小心。每次使用它时，都应该正确地转义用户可以通过使用`params`控制的任何参数，以防止SQL注入攻击。请阅读更多关于SQL注入保护的内容。

#### 3.2.6.1. Performing raw queries
##### 3.2.6.1.1. Mapping query fields to model fields
##### 3.2.6.1.2. Index lookups
##### 3.2.6.1.3. Deferring model fields
##### 3.2.6.1.4. Adding annotations
##### 3.2.6.1.5. Passing parameters into raw()
#### 3.2.6.2. Executing custom SQL directly
##### 3.2.6.2.1. Connections and cursors
###### 3.2.6.2.1.1. Calling stored procedures

### 3.2.7. 数据库事务

Django提供了几种控制数据库事务管理方式的方法。

#### 3.2.7.1. 管理数据库事务
##### 3.2.7.1.1.  Django的缺省事务行为

Django 的默认行为是在自动提交模式下运行。除非事务是活动的，否则每个查询都会立即提交到数据库。详见下文。

Django 自动使用事务或保存点来保证需要多个查询的ORM操作的完整性，尤其是`delete()`和`update()`查询。

出于性能原因，Django 的`TestCase`类还将每个测试封装在一个事务中。

##### 3.2.7.1.2. 将事务绑定到HTTP请求
##### 3.2.7.1.3. 显式地控制事务
#### 3.2.7.2. 自动提交
##### 3.2.7.2.1. 为何使用自动提交
##### 3.2.7.2.2. 禁用事务管理
#### 3.2.7.3. 提交后执行动作
##### 3.2.7.3.1. Savepoints
##### 3.2.7.3.2. Order of execution
##### 3.2.7.3.3. Exception handling
##### 3.2.7.3.4. Timing of execution
##### 3.2.7.3.5. Use in tests
##### 3.2.7.3.6. Why no rollback hook?
#### 3.2.7.4. 低等 APIs
##### 3.2.7.4.1. Autocommit
##### 3.2.7.4.2. Transactions
##### 3.2.7.4.3. Savepoints
#### 3.2.7.5. 特定数据库事项
##### 3.2.7.5.1. Savepoints in SQLite
##### 3.2.7.5.2. Transactions in MySQL
##### 3.2.7.5.3. Handling exceptions within PostgreSQL transactions
###### 3.2.7.5.3.1. Transaction rollback
###### 3.2.7.5.3.2. Savepoint rollback



### 3.2.8. 多数据库
#### 3.2.8.1. Defining your databases
#### 3.2.8.2. Synchronizing your databases
##### 3.2.8.2.1. Using other management commands
#### 3.2.8.3. Automatic database routing
##### 3.2.8.3.1. Database routers
###### 3.2.8.3.1.1. Hints
##### 3.2.8.3.2. Using routers
##### 3.2.8.3.3. An example
#### 3.2.8.4. Manually selecting a database
##### 3.2.8.4.1. Manually selecting a database for a QuerySet
##### 3.2.8.4.2. Selecting a database for save()
###### 3.2.8.4.2.1. Moving an object from one database to another
##### 3.2.8.4.3. Selecting a database to delete from
##### 3.2.8.4.4. Using managers with multiple databases
###### 3.2.8.4.4.1. Using get_queryset() with multiple databases
#### 3.2.8.5. Exposing multiple databases in  Django 's admin interface
#### 3.2.8.6. Using raw cursors with multiple databases
#### 3.2.8.7. Limitations of multiple databases
##### 3.2.8.7.1. Cross-database relations
##### 3.2.8.7.2. Behavior of contrib apps



### 3.2.9. 表空间
#### 3.2.9.1. Declaring tablespaces for tables
#### 3.2.9.2. Declaring tablespaces for indexes
#### 3.2.9.3. An example
#### 3.2.9.4. Database support



### 3.2.10. 数据库访问优化
#### 3.2.10.1. Profile first
#### 3.2.10.2. Use standard DB optimization techniques
#### 3.2.10.3. Understand QuerySets
##### 3.2.10.3.1. Understand QuerySet evaluation
##### 3.2.10.3.2. Understand cached attributes
##### 3.2.10.3.3. Use the with template tag
##### 3.2.10.3.4. Use iterator()
##### 3.2.10.3.5. Use explain()
#### 3.2.10.4. Do database work in the database rather than in Python
##### 3.2.10.4.1. Use RawSQL
##### 3.2.10.4.2. Use raw SQL
#### 3.2.10.5. Retrieve individual objects using a unique, indexed column
#### 3.2.10.6. Retrieve everything at once if you know you will need it
##### 3.2.10.6.1. Use QuerySet.select_related() and prefetch_related()
#### 3.2.10.7. Don't retrieve things you don't need
##### 3.2.10.7.1. Use QuerySet.values() and values_list()
##### 3.2.10.7.2. Use QuerySet.defer() and only()
##### 3.2.10.7.3. Use QuerySet.count()
##### 3.2.10.7.4. Use QuerySet.exists()
##### 3.2.10.7.5. Don't overuse count() and exists()
##### 3.2.10.7.6. Use QuerySet.update() and delete()
##### 3.2.10.7.7. Use foreign key values directly
##### 3.2.10.7.8. Don't order results if you don't care
#### 3.2.10.8. Insert in bulk



### 3.2.11. 数据库工具
#### 3.2.11.1. connection.execute_wrapper()
### 3.2.12. 模型关系 API 使用示例
#### 3.2.12.1. Many-to-many relationships
#### 3.2.12.2. Many-to-one relationships
#### 3.2.12.3. One-to-one relationships



## 3.3. 处理 HTTP 请求
### 3.3.1. URL dispatcher 
#### 3.3.1.1. Overview
#### 3.3.1.2. How Django processes a request
#### 3.3.1.3. Example
#### 3.3.1.4. Path converters
#### 3.3.1.5. Registering custom path converters
#### 3.3.1.6. Using regular expressions
##### 3.3.1.6.1. Using unnamed regular expression groups
##### 3.3.1.6.2. Nested arguments
#### 3.3.1.7. What the URLconf searches against
#### 3.3.1.8. Specifying defaults for view arguments
#### 3.3.1.9. Performance
#### 3.3.1.10. Syntax of the urlpatterns variable
#### 3.3.1.11. Error handling
#### 3.3.1.12. Including other URLconfs
##### 3.3.1.12.1. Captured parameters
#### 3.3.1.13. Passing extra options to view functions
##### 3.3.1.13.1. Passing extra options to include()
#### 3.3.1.14. Reverse resolution of URLs
##### 3.3.1.14.1. Examples
#### 3.3.1.15. Naming URL patterns
#### 3.3.1.16. URL namespaces
##### 3.3.1.16.1. Introduction
##### 3.3.1.16.2. Reversing namespaced URLs
###### 3.3.1.16.2.1. Example
##### 3.3.1.16.3. URL namespaces and included URLconfs
### 3.3.2. Writing views 
#### 3.3.2.1. A simple view
#### 3.3.2.2. Mapping URLs to views
#### 3.3.2.3. Returning errors
##### 3.3.2.3.1. The Http404 exception
#### 3.3.2.4. Customizing error views
##### 3.3.2.4.1. Testing custom error views
### 3.3.3. View decorators 
#### 3.3.3.1. Allowed HTTP methods
#### 3.3.3.2. Conditional view processing
#### 3.3.3.3. GZip compression
#### 3.3.3.4. Vary headers
#### 3.3.3.5. Caching
### 3.3.4. File upload 
#### 3.3.4.1. Basic file uploads
##### 3.3.4.1.1. Handling uploaded files with a model
##### 3.3.4.1.2. Uploading multiple files
#### 3.3.4.2. Upload Handlers
##### 3.3.4.2.1. Where uploaded data is stored
##### 3.3.4.2.2. Changing upload handler behavior
##### 3.3.4.2.3. Modifying upload handlers on the fly
### 3.3.5. Django shortcut functions
#### 3.3.5.1. render()
##### 3.3.5.1.1. Required arguments
##### 3.3.5.1.2. Optional arguments
##### 3.3.5.1.3. Example
#### 3.3.5.2. render_to_response()
#### 3.3.5.3. redirect()
##### 3.3.5.3.1. Examples
#### 3.3.5.4. get_object_or_404()
##### 3.3.5.4.1. Required arguments
##### 3.3.5.4.2. Example
#### 3.3.5.5. get_list_or_404()
##### 3.3.5.5.1. Required arguments
##### 3.3.5.5.2. Example
### 3.3.6. Generic views

### 3.3.7. 中间件

中间件是挂接到 Django 请求/响应处理的框架。它是一个轻量级、低级的“插件”系统，用于全局修改 Django 的输入或输出。

每个中间件组件都负责执行一些特定的功能。例如，Django 包含一个中间件组件`AuthenticationMiddleware`，它使用会话将用户与请求关联起来。

本文档解释了中间件如何工作，如何激活中间件，以及如何编写自己的中间件。

Django 附带了一些内置的中间件，您可以立即使用它们。

#### 3.3.7.1. 写自己的中间件

中间件工厂是可调用的，它接受`get_response`调用并返回一个中间件。中间件是可调用的，它接受请求并返回响应，就像视图一样。

中间件可以写成这样的函数:

```python
def simple_middleware(get_response):
    # 一次性设置与初始化

    def middleware(request):
        # 在调用视图(以及稍后的中间件)之前，为每个请求执行代码。

        response = get_response(request)

        # 调用视图后，为每个请求/响应执行的代码。

        return response

    return middleware
```

或者它可以写成一个实例可调用的类，如下所示:

```python
class SimpleMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        # 一次性设置与初始化

    def __call__(self, request):
        # 在调用视图(以及稍后的中间件)之前，为每个请求执行代码。

        response = self.get_response(request)
        
        # 调用视图后，为每个请求/响应执行的代码。
        return response
```

Django 提供的`get_response`调用可能是实际的视图（如果这是最后列出的中间件），也可能是链中的下一个中间件。当前中间件不需要知道或关心它到底是什么，只需要知道它代表接下来的内容。

上面的代码稍微简化了一些——对于链中的最后一个中间件，`get_response`调用不是实际的视图，而是处理程序中的包装器方法，它负责应用`view middleware`，使用适当的`URL`参数调用视图，以及应用`template-response`和`exception`中间件。

中间件可以位于 Python 路径的任何位置。

##### 3.3.7.1.1. `__init__(get_response)`

中间件工厂必须接受`get_response`参数。您还可以为中间件初始化一些全局状态。请记住以下几点注意事项:

- Django只使用`get_response`参数初始化您的中间件，所以您不能将`__init__()`定义为需要任何其他参数。

- 与每个请求调用一次的方法不同，当Web服务器启动时，只调用一次。

##### 3.3.7.1.2. 将中间件标记为未使用

有时在启动时确定是否应该使用中间件是很有用的。在这些情况下，中间件的`__init__()`方法可能引发`MiddlewareNotUsed`。然后 Django 将从中间件进程中删除该中间件，当`DEBUG`为`True`时并向`django.request`日志记录一条调试消息。

#### 3.3.7.2. 激活中间件

要激活中间件件，请将其添加到 Django `setting`中的中间件列表中。

在`MIDDLEWARE`中，每个中间件组件都由一个字符串表示：到中间件工厂的类或函数名的完整Python路径。例如，这是`django-admin startproject`创建的默认值:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

Django 安装不需要任何中间件——如果您愿意，中间件可以是空的——但是强烈建议您至少使用`common`中间件。

`MIDDLEWARE`中的顺序很重要，因为一个中间件可以依赖于其他中间件。例如，`authentication`中间件将经过身份验证的用户存储在会话中，因此，它必须在`SessionMiddleware`之后运行。有关 Django 中间件类排序的一些常见提示，请参阅 API 中间件排序。

#### 3.3.7.3. 中间件顺序和分层

在请求阶段，在调用视图之前，Django 按`MIDDLEWARE`中定义的顺序自顶向下应用中间件。

您可以把它看作是一个洋葱：每个中间件类都是一个“层”，它封装视图，视图位于洋葱的核心。如果请求通过洋葱的所有层（每个层都调用`get_response`将请求传递到下一层），一直传递到核心视图，那么在返回的过程中，响应将通过每一层（以相反的顺序）。

如果其中一层决定短路并返回响应，而不调用它的`get_response`，则该层(包括视图)中的任何洋葱层都不会看到请求或响应。响应将只通过请求传入的相同层返回。

#### 3.3.7.4. 其它中间件钩子

除了前面描述的基本请求/响应中间件模式之外，您还可以向基于类的中间件添加其他三种特殊方法:

##### 3.3.7.4.1. process_view()

*process_view(request, view_func, view_args, view_kwargs)*

`request`是一个`HttpRequest`对象。`view_func`是 Django 即将使用的 Python 函数。`view_args`是将传递给视图的位置参数列表，`view_kwargs`是将传递给视图的关键字参数字典。

`view_args`和`view_kwargs`都不包含第一个视图参数(`request`)。

`process_view()`，它是在 Django 调用视图之前调用的。

它应该返回`None`或`HttpResponse`对象。如果它返回`None`，Django 将继续处理这个请求，执行任何其他`process_view()`中间件，然后执行适当的视图。如果它返回`HttpResponse`对象，Django 就不会调用适当的视图，它将把响应中间件应用到`HttpResponse`并返回结果。

注意：在视图运行之前或`process_view()`中访问`request.POST`，将阻止任何在中间件之后运行的能够`修改请求的上载处理器`的视图，通常应该避免这样做。

可以将`CsrfViewMiddleware`类视为异常，因为它提供了`csrf_exempt()`和`csrf_protect()`装饰器，这些装饰器允许视图显式地控制 CSRF 验证应该在什么时候发生。

##### 3.3.7.4.2. process_exception()

*process_exception(request, exception)*

`request`是一个`HttpRequest`对象。`exception`是视图函数引发的异常对象。

Django 在视图引发异常时调用`process_exception()`。`process_exception()`应该返回`None`或`HttpResponse`对象。如果返回`HttpResponse`对象，将应用模板响应和响应中间件，并将结果响应返回给浏览器。否则，将启动缺省异常处理。

同样，中间件在响应阶段以相反的顺序运行，其中包括`process_exception`。如果异常中间件返回响应，则根本不会调用该中间件上面的中间件类的`process_exception`方法。

##### 3.3.7.4.3. process_template_response()

*process_template_response(request, response)*

`request`是一个`HttpRequest`对象。`response`是 Django 视图或中间件返回的`TemplateResponse`对象(或等效对象)。

`process_template_response()`在视图执行完成后被调用，如果响应实例具有`render()`方法，则在视图执行完成后调用该对象（或等效对象），指示它是一个`TemplateResponse`或等效对象。

它必须返回一个实现`render`方法的响应对象。它可以通过改变`response.template_name`和`response.context_data`来改变给定的`response`，或者它可以创建并返回一个全新的`TemplateResponse`或等效的对象。

您不需要显式地呈现响应——一旦调用了所有模板响应中间件，响应将自动呈现。

中间件在响应阶段以相反的顺序运行，其中包括`process_template_response()`。

#### 3.3.7.5. 处理流响应

与`HttpResponse`不同，`StreamingHttpResponse`没有内容属性。因此，中间件不能再假设所有响应都具有内容属性。如果他们需要访问内容，他们必须测试流响应并相应地调整他们的行为:

```python
if response.streaming:
    response.streaming_content = wrap_streaming_content(response.streaming_content)
else:
    response.content = alter_content(response.content)
```

> 注意：`streaming_content`应该被认为太大而不能保存在内存中。响应中间件可以将它封装在一个新的生成器中，但不能使用它。包装通常实现如下:
> 
> ```python
> def wrap_streaming_content(content):
>   for chunk in content:
>       yield alter_content(chunk)
> ```

#### 3.3.7.6. 异常处理

Django 自动将视图或中间件引发的异常转换为带有错误状态代码的适当 HTTP 响应。某些异常转换为`4xx`状态码，而未知异常转换为`500`状态码。

这种转换发生在每个中间件之前和之后(您可以把它看作是洋葱的每一层之间的薄膜)，因此每个中间件都可以依赖于从`get_response`调用中获得某种 HTTP 响应。

中间件不需要担心如何在`try/except`中包装对`get_response`的调用，以及如何处理后续中间件或视图可能引发的异常。例如，即使链中的下一个中间件引发`Http404`异常，您的中间件也不会看到该异常。相反，它将获得一个`status_code`为`404`的`HttpResponse`对象。

#### 3.3.7.7. 升级 Django 1.10 之前格式的中间件

略

### 3.3.8. 使用会话

Django 完全支持匿名会话。会话框架允许您在每个站点访问者的基础上存储和检索任意数据。它将数据存储在服务器端，并对 cookie 的发送和接收进行抽象。cookie 包含一个会话 ID ——而不是数据本身（除非使用基于cookie的后端）。

#### 3.3.8.1. 启用会话

会话是通过中间件实现的。

要启用会话功能，请执行以下操作:

- 编辑中间件设置，确保它包含`django.contrib.sessions.middleware.SessionMiddleware`。由`django-admin startproject`创建的默认`settings.py`激活了会话中间件。

如果您不想使用会话，您还可以从`MIDDLEWARE`中删除`SessionMiddleware`，从`INSTALLED_APPS.`中删除`django.contrib.sessions`。这将为您节省一点开销。

#### 3.3.8.2. 配置会话引擎

默认情况下，Django 将会话存储在数据库中（使用模型`django.contrib.sessions.models.Session`）。虽然这很方便，但是在某些设置中，在其他地方存储会话数据更快，因此 Django 可以配置为在文件系统或缓存中存储会话数据。

##### 3.3.8.2.1. 使用基于数据库的会话

如果希望使用数据库支持的会话，需要添加`django.contrib.sessions`到`INSTALLED_APPS`设置。

配置好安装之后，运行`manage.py migrate`来安装存储会话数据的单个数据库表。

##### 3.3.8.2.2. 使用基于缓存的会话

略

##### 3.3.8.2.3. 使用基于文件的会话

略

##### 3.3.8.2.4. 使用基于 cookie 的会话

略

#### 3.3.8.3. 在视图中使用会话

当`SessionMiddleware`被激活时，每个`HttpRequest`对象（任何 Django 视图函数的第一个参数）都将有一个`session`属性，这是一个类似字典的对象。

你可以在视图中的任何位置读写`request.session`。您可以多次编辑它。

**`class backends.base.SessionBase`**

这是所有会话对象的基类。它有以下标准字典方法:

**`__getitem__(key)`** 示例： `fav_color = request.session['fav_color']`

**`__setitem__(key, value)`** 示例： `request.session['fav_color'] = 'blue'`

**`__delitem__(key)`** 示例：`del request.session['fav_color']`，如果键不存在则引发`KeyError`。

**`__contains__(key)`** 示例： `'fav_color' in request.session`

**`get(key, default=None)`** 示例： `fav_color = request.session.get('fav_color', 'red')`

**`pop(key, default=__not_given)`** 示例：`fav_color = request.session.pop('fav_color', 'blue')`

**`keys()`**

**`items()`**

**`setdefault()`**

**`clear()`**

**`flush()`** 从会话中删除当前会话数据并删除会话cookie。如果您想确保不能再次从用户的浏览器访问以前的会话数据(例如，`django.contrib.auth.logout()`函数调用它)，就可以使用这个函数。

**`set_test_cookie()`**  设置一个测试`cookie`，以确定用户的浏览器是否支持`cookie`。由于`cookie`的工作方式，在用户的下一个页面请求之前，您无法对此进行测试。

**`test_cookie_worked()`**  返回`True`或`False`，这取决于用户的浏览器是否接受测试`cookie`。由于`cookie`的工作方式，您必须在前面的单独页面请求上调用`set_test_cookie()`。

**`delete_test_cookie()`**  删除测试cookie。

**`set_expiry(value)`** 设置会话的过期时间。您可以传递许多不同的值:

- 如果`value`是整数，那么会话将在那么多秒不活动之后过期。例如，调用`request.session. set_expires(300)`将使会话在5分钟内过期。

- 如果`value`是一个`datetime`或`timedelta`对象，会话将在那个特定的日期/时间过期。注意，只有在使用`PickleSerializer`时，`datetime`和`timedelta`值才是可序列化的。

- 如果值为`0`，当关闭用户的Web浏览器时，用户的会话`cookie`将过期。

- 如果值为`None`，会话将使用全局会话过期策略。

出于过期目的，读取会话不被视为活动。会话过期从最后一次修改会话开始计算。

**`get_expiry_age()`**  返回此会话过期前的秒数。

对于没有自定义过期的会话(或那些设置为在浏览器关闭时过期的会话)，这将等于`SESSION_COOKIE_AGE`。

这个函数接受两个可选的关键字参数:

- `modification`：会话的最后修改，作为`datetime`对象。默认为当前时间。

- `expires`：会话的过期信息，如`datetime`对象、`int`(以秒为单位)或`None`。

默认值为`set_expires()`存储在会话中的值(如果有一个，或者没有)。

**`get_expiry_date()`**  返回此会话将过期的日期。

对于没有自定义过期的会话(或那些设置为在浏览器关闭时过期的会话)，这将等于从现在开始的`SESSION_COOKIE_AGE`秒。

这个函数接受与`get_expiry_age()`相同的关键字参数。

**`get_expire_at_browser_close()`**  返回`True`或`False`，这取决于在关闭用户的Web浏览器时，用户的会话`cookie`是否会过期。

**`clear_expired()`**  从会话存储区中移除过期的会话。这个类方法由`clearsessions`调用。

**`cycle_key()`**  创建一个新的会话密钥，同时保留当前会话数据。`django.contrib.auth.login()`调用此方法来缓解会话固定。

##### 3.3.8.3.1. 会话序列化

默认情况下，Django 使用`JSON`序列化会话数据。您可以使用`SESSION_SERIALIZER`设置来定制会话序列化格式。即使有《写自己的序列化器》中描述的注意事项，我们仍然强烈建议坚持使用`JSON`序列化，尤其是在使用`cookie`后端时。

例如，如果您使用`pickle`来序列化会话数据，下面是一个攻击场景。

如果您正在使用签名`cookie`会话后端，并且攻击者知道`SECRET_KEY` (Django中没有导致泄漏的固有漏洞)，那么攻击者可以将一个字符串插入到他们的会话中，当未进行`pickle`时，该会话将在服务器上执行任意代码。这样做的技术很简单，在互联网上很容易找到。尽管`cookie`会话存储对`cookie`存储的数据进行签名以防止篡改，但`SECRET_KEY`泄漏会立即升级为远程代码执行漏洞。

###### 3.3.8.3.1.1. 绑定序列化器
###### 3.3.8.3.1.2. 写自己的序列化器
##### 3.3.8.3.2. 会话对象指南
##### 3.3.8.3.3. 示例
#### 3.3.8.4. 设置测试 cookie
#### 3.3.8.5. 在视图外使用会话
#### 3.3.8.6. 当会话被保存
#### 3.3.8.7. 浏览长度的会话与持久会话
#### 3.3.8.8. 清除会话存储
#### 3.3.8.9. 设置
#### 3.3.8.10. 会话安全性
#### 3.3.8.11. 技术细节
##### 3.3.8.11.1. 会话对象
#### 3.3.8.12. 扩展基于数据库的会话引擎
##### 3.3.8.12.1. 示例
#### 3.3.8.13. URL 中的会话 ID




## 3.4. 处理表单

> 关于本文档
>
> 本文档介绍了web表单的基础知识以及在Django中如何处理它们。更多细节见 API。

除非您计划构建只发布内容、不接受访问者输入的网站和应用程序，否则您将需要理解和使用表单。

Django提供了一系列工具和库，帮助您构建表单来接受站点访问者的输入，然后处理和响应输入。

### 3.4.1. HTML forms

在HTML中，表单是`<form>…</form>`内的元素集合，允许访问者进行输入文本、选择选项、操作对象或控件等操作，然后将这些信息发送回服务器。

其中一些表单界面元素——文本输入或复选框——相当简单，并且内置在HTML本身中。

其他的要复杂得多。弹出日期选择器或允许您移动滑块或操纵控件的接口通常使用`JavaScript`和`CSS`以及HTML表单`<input>`元素来实现这些效果。

除了`<input>`元素外，表单还必须指定两件事:

- where：返回对应于用户输入的数据的 URL

- how：数据应该由之返回的 HTTP 方法

例如，Django admin 的登录表单包含几个`<input>`元素：其中一个`type="text"`的用于用户名，`type="password"`的用于密码，`type="submit"`的用于登录按钮。

它还包含一些用户看不到的隐藏文本字段，Django 使用这些字段来确定下一步要做什么。

它还告诉浏览器，表单数据应该发送到`<form>`的`action`属性（`/admin/`）指定的URL，并且应该使用`method`属性（`post`）指定的 HTTP 机制发送。

当`<input type="submit" value="Log in">`元素被触发时，数据被返回到`/admin/`。

#### 3.4.1.1. GET 与 POST

`GET`和`POST`是处理表单时使用的惟一`HTTP`方法。

`Django`的登录表单使用`POST`方法返回，在`POST`方法中，浏览器打包表单数据，对其进行编码以便传输，将其发送到服务器，然后接收其响应。

相比之下，GET 将提交的数据绑定到一个字符串中，并使用它来组合 URL 。 URL 包含必须发送数据的地址，以及数据键和值。如果在 Django 文档中进行搜索，就可以看到这一点，该文档将生成一个形式为`https://docs.djangoproject.com/search/?q=forms&release=1`的 URL。

GET和POST通常用于不同的目的。

任何可以用来更改系统状态的请求(例如，在数据库中进行更改的请求)都应该使用POST。GET应该只用于不影响系统状态的请求。

GET也不适合密码表单，因为密码将出现在URL中，因此也出现在浏览器历史记录和服务器日志中，都是纯文本形式。它既不适合处理大量数据，也不适合处理二进制数据，比如图像。使用GET请求管理表单的Web应用程序存在安全风险：攻击者很容易模仿表单的后续操作来访问系统的敏感部分。POST，再加上 Django 的 CSRF 等其他保护，提供了对访问的更多控制。

另一方面，GET 适用于 web 搜索表单之类的东西，因为表示 GET 请求的 url 可以很容易地添加书签、共享或重新提交。

### 3.4.2.  Django 在表单中的角色

处理表单是一项复杂的业务。考虑 Django 的管理，其中可能需要准备许多不同类型的数据项，以便以表单的形式显示、呈现为HTML、使用方便的接口进行编辑、返回到服务器、验证和清理，然后保存或传递给后续处理。

Django的表单功能可以简化和自动化大部分工作，而且比大多数程序员自己编写的代码更安全。

Django处理表单中涉及的三个不同部分:

- 准备和重组数据，使其为呈现做好准备

- 为数据创建HTML表单

- 接收和处理客户提交的表单和数据

可以编写手工完成所有这些工作的代码，但是Django可以为您完成所有这些工作。

### 3.4.3. Django 中的表单

我们简要地描述了HTML表单，但是HTML `<form>`只是所需机制的一部分。

在Web应用程序上下文中，`form`可能指的是HTML `<form>`，或者是生成它的 Django 表单，或者是提交时返回的结构化数据，或者是这些部分的端到端工作集合。

#### 3.4.3.1. `Form`类

这个组件系统的核心是 Django 的`Form`类。就像 Django 模型描述对象的逻辑结构、行为以及向我们表示其部分的方式一样，`Form`类描述表单并确定它的工作方式和外观。

与模型类的字段映射到数据库字段类似，表单类的字段映射到HTML表单`<input>`元素。(`ModelForm`将模型类的字段映射到HTML表单，通过 HTML 表单 `<input>`元素，这就是 Django 管理的基础。)

表单的字段本身就是类，它们管理表单数据，并在提交表单时执行验证。`DateField`和`FileField`处理非常不同的数据类型，并且必须对其执行不同的操作。

表单字段在浏览器中以HTML“widget”(用户界面机制的一部分)的形式表示给用户。每个字段类型都有一个适当的默认小部件类，但是可以根据需要重写这些小部件类。

#### 3.4.3.2. 实例化、处理、渲染 表单

在Django中渲染对象时，我们通常:

1. 在视图中获取它(例如，从数据库中获取)

2. 将其传递给模板上下文

3. 使用模板变量将其扩展为HTML标记

在模板中呈现表单与呈现任何其他类型的对象几乎涉及相同的工作，但是有一些关键的区别。

在不包含数据的模型实例的情况下，在模板中使用它很少有用。另一方面，呈现一个未填充的表单是非常有意义的——当我们希望用户填充表单时，我们就是这么做的。

因此，当我们在视图中处理模型实例时，我们通常从数据库中检索它。当我们处理表单时，我们通常在视图中实例化它。

当我们实例化一个表单时，我们可以选择让它为空或预先填充它，例如:

- 来自保存的模型实例的数据(如用于编辑的管理表单)

- 我们从其他来源整理的数据

- 从以前的HTML表单提交中收到的数据

最后一个例子是最有趣的，因为它使用户不仅可以阅读网站，还可以将信息发送回网站。

### 3.4.4. 创建表单
#### 3.4.4.1. 需要做的工作

假设您想在您的网站上创建一个简单的表单，以便获得用户的名称。你的模板中需要这样的东西:

```html
<form action="/your-name/" method="post">
    <label for="your_name">Your name: </label>
    <input id="your_name" type="text" name="your_name" value="{{ current_name }}">
    <input type="submit" value="OK">
</form>
```

这告诉浏览器使用 POST 方法将表单数据返回到 URL `/your-name/`。它将显示一个文本字段，标记为“Your name:”，以及一个标记为“OK”的按钮。如果模板上下文包含`current_name`变量，那么它将用于预填充`your_name`字段。

您将需要一个视图来呈现包含 HTML 表单的模板，并且可以根据需要提供`current_name`字段。

提交表单时，发送到服务器的 POST 请求将包含表单数据。

现在您还需要一个与`/your-name/` URL对应的视图，该视图将在请求中找到适当的 键/值 对，然后处理它们。

这是一个非常简单的形式。在实践中，表单可能包含数十或数百个字段，其中许多字段可能需要预先填充，我们可能希望用户在完成操作之前完成多次编辑提交周期。

我们可能需要在浏览器中进行一些验证，甚至在提交表单之前，我们可能希望使用更复杂的字段，允许用户从日历中选择日期等等。

在这一点上，让Django为我们完成大部分工作要容易得多。

#### 3.4.4.2. 在 Django 中创建表单
##### 3.4.4.2.1. 表单类

我们已经知道了HTML表单的外观。我们在Django中的起点是这样的:

```python
from django import forms

class NameForm(forms.Form):
    your_name = forms.CharField(label='Your name', max_length=100)
```

这定义了一个只有一个字段(`your_name`)的表单类。我们对字段应用了一个对人友好的标签，它将在呈现时出现在`<label>`中(尽管在本例中，我们指定的标签实际上与我们忽略它时自动生成的标签相同)。

字段的最大允许长度由`max_length`定义。这做了两件事。它将`maxlength="100"`放在HTML `<input>`上(因此浏览器应该首先阻止用户输入超过这个数字的字符)。这还意味着当Django从浏览器接收回表单时，它将验证数据的长度。

表单实例有一个`is_valid()`方法，该方法为其所有字段运行验证例程。调用此方法时，如果所有字段都包含有效数据，则会:

- 返回 `True`。

- 将表单的数据放在其`cleaned_data`属性中。

整个表单，当第一次呈现时，看起来像：

```html
<label for="your_name">Your name: </label>
<input id="your_name" type="text" name="your_name" maxlength="100" required>
```

注意，它不包含`<form>`标记或`submit`按钮。我们必须自己提供模板。

##### 3.4.4.2.2. 视图

发送回Django网站的表单数据由一个视图处理，通常与发布表单的视图相同。

这允许我们重用一些相同的逻辑。

要处理表单，我们需要实例化它在视图中的URL，我们希望它在哪里发布:

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import NameForm

def get_name(request):
    # 如果是post，需要处理表单数据
    if request.method == 'POST':
        # 创建一个表单实例，并用来自请求的数据填充它
        form = NameForm(request.POST)
        # 检查是否有效
        if form.is_valid():
        # 按要求处理 form.cleaned_data，跳转到新 URL
        return HttpResponseRedirect('/thanks/')
    # 如果是get，创建空白表单
    else:
        form = NameForm()
        return render(request, 'name.html', {'form': form})
```

如果我们带着GET请求到达这个视图，它将创建一个空表单实例，并将其放在要呈现的模板上下文中。这是我们第一次访问URL时所期望的。

如果表单是使用POST请求提交的，视图将再次创建一个表单实例，并用`form = NameForm(request.POST)`中的数据填充它。这叫做“绑定数据到表单”（它现在是个绑定表单）。

我们调用表单的`is_valid()`方法，如果不正确，则返回到表单的模板。这一次表单不再是空的(未绑定的)，因此HTML表单将填充以前提交的数据，在这些数据中可以根据需要进行编辑和更正。

如果`is_valid()`为真，我们现在就可以在其`cleaned_data`属性中找到所有经过验证的表单数据。我们可以使用这些数据更新数据库或进行其他处理，然后发送HTTP重定向到浏览器，告诉它下一步要去哪里。

##### 3.4.4.2.3. 模板

我们不需要在name.html模板中做太多工作。最简单的例子是:

```html
<form action="/your-name/" method="post">
    {% csrf_token %}
    {{ form }}
    <input type="submit" value="Submit">
</form>
```

Django的模板语言将把表单的所有字段及其属性从那个{{form}}解压缩到HTML标记中。

> **表单及跨站请求防伪保护**
>
> Django 提供了易于使用的保护，防止跨站点请求伪造。在启用 CSRF 保护的情况下通过 POST 提交表单时，必须使用前面示例中的 csrf_token 模板标记。但是，由于 CSRF 保护没有直接绑定到模板中的表单，因此在本文档的以下示例中省略了此标记。

> **HTML5输入类型和浏览器验证**
>
> 如果表单包含`URLField`、`EmailField`或任何整数字段类型，Django 将使用`url`、`email`和`number`HTML5输入类型。默认情况下，浏览器可以对这些字段应用它们自己的验证，这可能比Django的验证更严格。如果您想禁用此行为，请在表单标记上设置`novalidate`属性，或者在字段上指定一个不同的小部件，如`TextInput`。

现在我们有了一个可用的web表单，由Django表单描述，由视图处理，并呈现为HTML `<form>`。

这就是您开始所需要的全部内容，但是表单框架将更多内容放在您的指尖上。一旦您理解了上面描述的流程的基础知识，您就应该准备好理解表单系统的其他特性，并准备进一步了解底层机制。

### 3.4.5. 关于表单类的更多信息

所有表单类都是作为`django.forms.Form`或`django.forms.ModelForm`的子类创建的。您可以将`ModelForm`看作是`Form`的一个子类。`Form`和`ModelForm`实际上继承了(私有的)`BaseForm`类的公共功能，但是这个实现细节很少重要。

> **模型与表单**
>
> 实际上，如果您的表单要直接用于添加或编辑Django模型，`ModelForm`可以为您节省大量的时间、精力和代码，因为它将从`model`类构建一个表单，以及相应的字段及其属性。

#### 3.4.5.1. 绑定与解绑表单实例

绑定和非绑定表单的区别很重要:

- 未绑定的表单没有与之关联的数据。当呈现给用户时，它将为空或包含默认值。

- 绑定表单已经提交了数据，因此可以用来判断数据是否有效。如果呈现了无效的绑定表单，它可以包含内联错误消息，告诉用户要纠正哪些数据。

表单的`is_bound`属性将告诉您表单是否绑定了数据。

#### 3.4.5.2. 关于字段的更多信息

考虑一个比上面的例子更有用的形式，我们可以用它来实现个人网站上的“联系我”功能：

```python
from django import forms

class ContactForm(forms.Form):
    subject = forms.CharField(max_length=100)
    message = forms.CharField(widget=forms.Textarea)
    sender = forms.EmailField()
    cc_myself = forms.BooleanField(required=False)
```

我们前面的表单使用了单个字段，`your_name`，一个`CharField`。在本例中，表单有四个字段：`subject`、`message`、`sender`、`cc_myself`，使用了`CharField`、`EmailField`和`BooleanField`三种字段类型。

##### 3.4.5.2.1. 小部件

每个表单字段都有一个对应的小部件类，这个小部件类又对应于一个HTML表单小部件，比如`<input type="text">`。

在大多数情况下，该字段将有一个合理的默认小部件。例如，默认情况下，`CharField`将有一个`TextInput`小部件，它在HTML中生成一个`<input type="text">`。如果需要`<textarea>`，则在定义表单字段时指定适当的小部件，就像我们为`message`字段所做的那样。

##### 3.4.5.2.2. 字段数据

无论表单提交的数据是什么，只要通过调用`is_valid()`成功地验证了它（`is_valid()`已返回`True`），验证过的表单数据就会出现在`form.cleaned_data`字典中。这些数据将被很好地转换为Python类型。

> 注意:您仍然可以直接从请求访问未经验证的`request.POST`，但是经过验证的数据更好。

在上面的联系人表单示例中，`cc_myself`将是一个布尔值。同样，像`IntegerField`和`FloatField`这样的字段分别将值转换为Python `int`和`float`。

下面是如何在处理这个表单的视图中处理表单数据:

```python
# views.py

from django.core.mail import send_mail

if form.is_valid():
    subject = form.cleaned_data['subject']
    message = form.cleaned_data['message']
    sender = form.cleaned_data['sender']
    cc_myself = form.cleaned_data['cc_myself']

    recipients = ['info@example.com']
    if cc_myself:
        recipients.append(sender)

    send_mail(subject, message, sender, recipients)
    return HttpResponseRedirect('/thanks/')
```

一些字段类型需要一些额外的处理。例如，使用表单上传的文件需要以不同的方式处理(可以从`request.FILES,`检索它们，而不是`request.POST`)。有关如何使用表单处理文件上载的详细信息，请参见《将上载的文件绑定到窗体》。

### 3.4.6. 使用表单模板

将表单放入模板所需要做的就是将表单实例放入模板上下文。因此，如果在上下文中调用表单，{{form}}将呈现它的`<label>`和`<input>`元素。

#### 3.4.6.1. 表单渲染选项

> **附加表单模板特征**
>
> 不要忘记表单的输出不包括周围的`<form>`标记或表单的`submit`控件。你必须自己提供这些。

对于`<label>/<input>`对，还有其他的输出选项：

- `{{ form.as_table }}`将渲染成由 `<tr>` 标签包裹的表格单元。

- `{{ form.as_p }}` 将由`<p>`标签包裹。

- `{{ form.as_ul }}` 将由`<li>`标签包裹。

注意，您必须自己提供周围的`<table>`或`<ul>`元素。

这是`{{ form.as_p }}`的输出用于我们的`ContactForm`实例：

```html
<p><label for="id_subject">Subject:</label>
<input id="id_subject" type="text" name="subject" maxlength="100" required></p>
<p><label for="id_message">Message:</label>
<textarea name="message" id="id_message" required></textarea></p>
<p><label for="id_sender">Sender:</label>
<input type="email" name="sender" id="id_sender" required></p>
<p><label for="id_cc_myself">Cc myself:</label>
<input type="checkbox" name="cc_myself" id="id_cc_myself"></p>
```

注意，每个表单字段都有一个ID属性设置为`id_<field-name>`，该属性由附带的标签标记引用。这对于确保表单可被辅助技术(如屏幕阅读器软件)访问非常重要。

您还可以自定义生成标签和`id`的方式。

#### 3.4.6.2. 手动渲染字段

我们不必让 Django 打开表单的字段，如果愿意，我们可以手动执行(例如，允许重新排序字段)。每个字段都可以通过`{{form.name_of_field}}`作为表单的属性使用，并在Django模板中适当呈现。例如:

```html
{{ form.non_field_errors }}
<div class="fieldWrapper">
    {{ form.subject.errors }}
    <label for="{{ form.subject.id_for_label }}">Email subject:</label>
    {{ form.subject }}
</div>
<div class="fieldWrapper">
    {{ form.message.errors }}
    <label for="{{ form.message.id_for_label }}">Your message:</label>
    {{ form.message }}
</div>
<div class="fieldWrapper">
    {{ form.sender.errors }}
    <label for="{{ form.sender.id_for_label }}">Your email address:</label>
    {{ form.sender }}
</div>
<div class="fieldWrapper">
    {{ form.cc_myself.errors }}
    <label for="{{ form.cc_myself.id_for_label }}">CC yourself?</label>
    {{ form.cc_myself }}
</div>
```

还可以使用`label_tag()`生成完整的`<label>`元素。例如:

```html
<div class="fieldWrapper">
    {{ form.subject.errors }}
    {{ form.subject.label_tag }}
    {{ form.subject }}
</div>
```

##### 3.4.6.2.1. 渲染表单的错误消息

当然，这种灵活性的代价是更多的工作。到目前为止，我们还不必担心如何显示表单错误，因为这已经为我们处理好了。在本例中，我们必须确保处理每个字段的任何错误以及整个表单的任何错误。注意表单顶部的`{{ form.non_field_errors }}`以及模板在每个字段上查找错误。

使用`{{form.name_of_field.errors}}`显示一个表单错误列表，呈现为无序列表。这看起来像:

```html
<ul class="errorlist">
    <li>Sender is required.</li>
</ul>
```

列表中有一个 CSS 类`errorlist`，允许您对其外观进行样式设置。如果你想进一步定制错误的显示，你可以通过循环这些错误:

```html
{% if form.subject.errors %}
    <ol>
    {% for error in form.subject.errors %}
        <li><strong>{{ error|escape }}</strong></li>
    {% endfor %}
    </ol>
{% endif %}
```

非字段错误（和/或使用`form.as_p()`等帮助工具时在表单顶部呈现的隐藏字段错误）将与额外的非字段类一起呈现，以帮助将它们与特定于字段的错误区分开来。

例如，`{{form.non_field_errors}}`看起来像:

```html
<ul class="errorlist nonfield">
    <li>Generic validation error</li>
</ul>
```

#### 3.4.6.3. 遍历表单字段

如果对每个表单字段使用相同的HTML，则可以使用`{% for %}`循环遍历每个字段，从而减少重复代码:

```html
{% for field in form %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
        {% if field.help_text %}
            <p class="help">{{ field.help_text|safe }}</p>
        {% endif %}
    </div>
{% endfor %}
```

`{{ field }}`字段上有用的属性包括：

- **{{ field.label }}** 字段标签，比如`Email address`。

- **{{ field.label_tag }}** 由适当的 HTML `<label>`标签包裹的字段标签。这包括表单的`label_suffix`，例如，缺省的`label_suffix`是个冒号：

```html
<label for="id_email">Email address:</label>
```

- **{{ field.id_for_label }}** 将用于此字段的ID(在上面的示例中为`id_email`)。如果您正在手工构造标签，您可能想要使用它来代替`label_tag`。例如，如果您有一些内联 JavaScript，并且希望避免硬编码字段的ID，那么它也很有用。

- **{{ field.value }}** 字段的值，比如`someone@example.com.`。

- **{{ field.html_name }}** 将在输入元素的`name`字段中使用的字段的名称。如果已经设置了表单前缀，那么这将考虑它。

- **{{ field.help_text }}** 与该字段关联的任何帮助文本。

- **{{ field.errors }}** 输出一个`<ul class="errorlist">`，其中包含与此字段对应的任何验证错误。您可以使用`{% for error in field.errors %}`自定义错误的表示形式。在本例中，循环中的每个对象都是一个包含错误消息的简单字符串。

- **{{ field.is_hidden }}** 如果表单字段是隐藏字段，则此属性为真，否则为假。它作为模板变量不是特别有用，但在条件测试中可能有用，比如:

```html
{% if field.is_hidden %}
    {# 做一些事 #}
{% endif %}
```

- **{{ field.field }}** `BoundField`包装的表单类的`Field`实例，可以通过它访问`Field`属性，比如`{{ char_field.field.max_length }}`。

##### 3.4.6.3.1. 遍历隐藏与可见字段

如果您在模板中手工布局表单，而不是依赖Django的默认表单布局，那么您可能希望将`<input type="hidden">`字段与非隐藏字段区别对待。例如，由于隐藏字段不显示任何内容，因此将错误消息“放在”字段旁边可能会给用户造成混淆——因此应该以不同的方式处理这些字段的错误。

Django在表单上提供了两种方法，允许您独立地遍历隐藏字段和可见字段:

`hidden_fields()`和`visible_fields ()`。下面是对前面使用这两种方法的示例的修改:

```html
{# 遍历隐藏字段 #}
{% for hidden in form.hidden_fields %}
    {{ hidden }}
{% endfor %}

{# 遍历可见字段 #}
{% for field in form.visible_fields %}
    <div class="fieldWrapper">
        {{ field.errors }}
        {{ field.label_tag }} {{ field }}
    </div>
{% endfor %}
```

此示例不处理隐藏字段中的任何错误。通常，隐藏字段中的错误是表单篡改的标志，因为正常的表单交互不会改变它们。不过，您也可以轻松地为这些表单错误插入一些错误显示。

#### 3.4.6.4. 重用表单模板

如果你的网站在多个地方对表单使用相同的呈现逻辑，你可以将表单的循环保存在一个独立的模板中，并使用`include`标签在其他模板中重用，从而减少重复:

```html
<!--引用模板-->
{% include "form_snippet.html" %}
```

```html
<!--定义模板-->
{% for field in form %}
<div class="fieldWrapper">
    {{ field.errors }}
    {{ field.label_tag }} {{ field }}
</div>
{% endfor %}
```

如果传递给模板的表单对象在上下文中有不同的名称，可以使用`include`标记的`with`参数为其别名:

```html
{% include "form_snippet.html" with form=comment_form %}
```

如果您发现自己经常这样做，您可能会考虑创建一个自定义包含标记。

### 3.4.7. 更多主题

#### 3.4.7.1. 表单集

**class BaseFormSet**

表单集是一个抽象层，用于处理同一页面上的多个表单。它可以与数据网格相比较。假设你有以下表单:

```python
>>> from django import forms
>>> class ArticleForm(forms.Form):
... title = forms.CharField()
... pub_date = forms.DateField()
```

您可能希望允许用户同时创建几篇文章。要从`ArticleForm`创建一个表单集，您可以这样做:

```python
>>> from django.forms import formset_factory
>>> ArticleFormSet = formset_factory(ArticleForm)
```

现在您已经创建了一个名为`ArticleFormSet`的表单集。`formset`使您能够遍历表单集中的表单，并像使用常规表单一样显示它们:

```bash
>>> formset = ArticleFormSet()
>>> for form in formset:
... print(form.as_table())
<tr><th><label for="id_form-0-title">Title:</label></th><td><input type="text" name="form-0-title" id="id_form-0-title"></td></tr>
<tr><th><label for="id_form-0-pub_date">Pub date:</label></th><td><input type="text"name="form-0-pub_date" id="id_form-0-pub_date"></td></tr>
```

可以看到，它只显示了一个空表单。显示的空表单的数量由额外的参数控制。默认情况下，`formset_factory()`定义一个额外的表单。下面的示例将显示两个空白表单:

```bash
>>> ArticleFormSet = formset_factory(ArticleForm, extra=2)
```

遍历表单集将按创建表单的顺序呈现表单。您可以通过为`__iter__()`方法提供一个替代实现来更改这个顺序。

还可以将表单集编入索引，从而返回相应的表单。如果您覆盖了`__iter__()`，那么您还需要覆盖`__getitem__`以获得匹配的行为。

##### 3.4.7.1.1. 对表单集使用初始化数据

##### 3.4.7.1.2. 限制表单的最大数量
##### 3.4.7.1.3. 表单集验证
##### 3.4.7.1.4. 理解管理表单
##### 3.4.7.1.5. 定制表单集验证
##### 3.4.7.1.6. 验证表单集中的表单数量
##### 3.4.7.1.7. 处理表单的排序和删除
##### 3.4.7.1.8. 向表单集添加附加字段
##### 3.4.7.1.9. 向表单集表单传递定制参数
##### 3.4.7.1.10. 定制表单集的前缀
##### 3.4.7.1.11. 在视图和模板中使用表单集
##### 3.4.7.1.12. 手动渲染可删除和可排序
##### 3.4.7.1.13. 在一个视图中使用多个表单集

#### 3.4.7.2. 从模型创建表单
##### 3.4.7.2.1. 模型表单

**class ModelForm**

如果您正在构建一个数据库驱动的应用程序，那么您很可能拥有与 Django 模型紧密映射的表单。例如，您可能有一个`BlogComment`模型，您希望创建一个表单，让人们提交评论。在这种情况下，在表单中定义字段类型是多余的，因为您已经在模型中定义了字段。

因此，Django 提供了一个`helper`类，它允许您从 Django 模型创建一个表单类。

例如:

```python
>>> from django.forms import ModelForm
>>> from myapp.models import Article
# Create the form class.
>>> class ArticleForm(ModelForm):
...     class Meta:
...         model = Article
...         fields = ['pub_date', 'headline', 'content', 'reporter']
# 创建一个表单以添加一篇文章
>>> form = ArticleForm()
# 创建一个表单以修改已存在的文章
>>> article = Article.objects.get(pk=1)
>>> form = ArticleForm(instance=article)
```

###### 3.4.7.2.1.1. 字段类型

生成的表单类将为指定的每个模型字段提供一个表单字段，其顺序在`fields`属性中指定。

每个模型字段都有对应的默认表单字段。例如，模型上的`CharField`表示为表单上的`CharField`。模型`ManyToManyField`表示为`MultipleChoiceField`。以下是转换的完整列表：

Model field | Form field
---|---
AutoField | Not represented in the form
BigAutoField | Not represented in the form
BigIntegerField | IntegerField with min_value set to -9223372036854775808 and max_value set to 9223372036854775807.
BinaryField | CharField, if editable is set to True on the model field, otherwise not represented in the form.
BooleanField | BooleanField, or NullBooleanField if null=True.
CharField | CharField with max_length set to the model field’s max_length and empty_value set to None if null=True.
DateField | DateField
DateTimeField | DateTimeField
DecimalField | DecimalField
EmailField | EmailField
FileField | FileField
FilePathField | FilePathField
FloatField | FloatField
ForeignKey | ModelChoiceField (see below)
ImageField | ImageField
IntegerField | IntegerField
IPAddressField | IPAddressField
GenericIPAddressField | GenericIPAddressField
ManyToManyField | ModelMultipleChoiceField (see below)
NullBooleanField | NullBooleanField
PositiveIntegerField | IntegerField
PositiveSmallIntegerField | IntegerField
SlugField | SlugField
SmallIntegerField | IntegerField
TextField | CharField with widget=forms.Textarea
TimeField | TimeField
URLField | URLField

正如您所期望的，`ForeignKey`和`ManyToManyField`模型字段类型是特殊的情况:

- `ForeignKey`由`django.forms.ModelChoiceField`表示。它是一个`ChoiceField`，它的选项是一个模型`QuerySet`。

- `ManyToManyField`由`django.forms.ModelMultipleChoiceField`表示。它是一个`ModelMultipleChoiceField`，它的选项是一个模型`QuerySet`。

此外，每个生成的表单字段都有如下属性设置：

- 如果模型字段的`blank=True`，那么`required`在表单字段上被设置为`False`。否则，`required=True`。

- 表单字段的标签被设置为模型字段的`verbose_name`，第一个字符大写。

- 表单字段的`help_text`被设置为模型字段的`help_text`。

- 如果模型字段具有`choices`集，那么表单字段的`widget`将被设置为`Select`，选项来自模型字段的选项。这些选项通常包括默认选择的空白选项。如果字段是必需的，这将强制用户进行选择。如果模型字段有`blank=False`和一个显式的默认值(默认值将被初始选择)，则不会包含空白选项。

最后，注意您可以覆盖用于给定模型字段的表单字段。参见下面的《覆盖默认字段》。

###### 3.4.7.2.1.2. 一个完整的例子

考虑一个模型集：

```python
from django.db import models
from django.forms import ModelForm

TITLE_CHOICES = (
    ('MR', 'Mr.'),
    ('MRS', 'Mrs.'),
    ('MS', 'Ms.'),
)

class Author(models.Model):
    name = models.CharField(max_length=100)
    title = models.CharField(max_length=3, choices=TITLE_CHOICES)
    birth_date = models.DateField(blank=True, null=True)
    def __str__(self):
        return self.name

class Book(models.Model):
    name = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = ['name', 'title', 'birth_date']

class BookForm(ModelForm):
    class Meta:
        model = Book
        fields = ['name', 'authors']
```

使用这些模型，上面的`ModelForm`子类大致相当于这个（惟一的区别是`save()`方法，我们稍后将讨论它）：

```python
from django import forms

class AuthorForm(forms.Form):
    name = forms.CharField(max_length=100)
    title = forms.CharField(
        max_length=3,
        widget=forms.Select(choices=TITLE_CHOICES),
    )
    birth_date = forms.DateField(required=False)

class BookForm(forms.Form):
    name = forms.CharField(max_length=100)
    authors = forms.ModelMultipleChoiceField(queryset=Author.objects.all())
```

###### 3.4.7.2.1.3. 验证`ModelForm`

验证模型表单涉及两个主要步骤:

1. 验证表单

2. 验证模型实例

与普通表单验证一样，模型表单验证在调用`is_valid()`或访问`errors`属性时隐式触发，在调用`full_clean()`时显式触发，尽管在实践中通常不会使用后一种方法。

模型验证`Model.full_clean()`在调用表单的`clean()`方法之后，从表单验证步骤中触发。

> 警告：清理过程以各种方式修改传递给`ModelForm`构造函数的模型实例。例如，模型上的任何日期字段都转换为实际的日期对象。验证失败可能会使基础模型实例处于不一致的状态，因此不建议重用它。

###### 3.4.7.2.1.4. 覆盖`clean()`方法

您可以在模型表单上覆盖`clean()`方法，以与在普通表单上相同的方式提供额外的验证。

附加到模型对象的模型表单实例将包含一个实例属性，该属性允许其方法访问特定的模型实例。

> 警告：`ModelForm.clean()`方法设置一个标志，`unique`、`unique_together`或`unique_for_date|month|year`，使模型验证步骤验证模型字段的唯一性。
>
> 如果希望覆盖`clean()`方法并保持此验证，则必须调用父类的`clean()`方法。


###### 3.4.7.2.1.5. 与模型验证的交互

作为验证过程的一部分，`ModelForm`将调用模型上每个字段的`clean()`方法，该方法在表单上有对应的字段。如果您排除了任何模型字段，验证将不会在这些字段上运行。

有关字段清理和验证如何工作的更多信息，请参阅表单验证文档。

在进行唯一性检查之前，将调用模型的`clean()`方法。有关模型的`clean()`钩子的更多信息，请参见验证对象。

###### 3.4.7.2.1.6. 考虑模型的`error_messages`

在`form field`或`form Meta`定义的错误消息总是优先于在`model field`定义的错误消息。

在`model field`上定义的错误消息只在模型验证步骤中引发`ValidationError`时使用，而在表单级别上没有定义相应的错误消息。

通过将`NON_FIELD_ERRORS`键添加到`ModelForm`内部元类的`error_messages`字典中，您可以覆盖由模型验证引发的`NON_FIELD_ERRORS`中的错误消息:

```python
from django.core.exceptions import NON_FIELD_ERRORS
from django.forms import ModelForm

class ArticleForm(ModelForm):
    class Meta:
    error_messages = {
    NON_FIELD_ERRORS: {
        'unique_together': "%(model_name)s's %(field_labels)s are not unique.",
    }
}
```

###### 3.4.7.2.1.7. `save()`方法

每个`ModelForm`也有一个`save()`方法。此方法从绑定到表单的数据创建并保存数据库对象。`ModelForm`的子类可以接受现有的模型实例作为关键字参数实例。如果提供了这个，`save()`将更新该实例。如果没有提供，`save()`将创建指定模型的新实例：

```python
>>> from myapp.models import Article
>>> from myapp.forms import ArticleForm

# 从Post数据创建表单实例
>>> f = ArticleForm(request.POST)

# 从表单数据保存一个文章对象
>>> new_article = f.save()

# 创建表单以编辑已有文章，但是用Post数据填充该表单
>>> a = Article.objects.get(pk=1)
>>> f = ArticleForm(request.POST, instance=a)
>>> f.save()
```

注意，如果没有验证表单，调用`save()`将通过检查`form.errors`来验证。如果表单中的数据没有经过验证，将会引发`ValueError`，即,如果`form.errors`的值为`True`。

如果表单的数据中没有出现可选字段，则生成的模型实例将对该字段使用模型字段缺省值(如果有的话)。此行为不适用于使用`CheckboxInput`、`CheckboxSelectMultiple`或`SelectMultiple`(或其`value_omitted_from_data()`方法总是返回`False`的任何自定义小部件)的字段，因为未选中的复选框和未选中的`<select multiple>`不会出现在HTML表单提交的数据中。如果您正在设计一个API，并且希望使用这些小部件之一的字段的默认回退行为，请使用自定义表单字段或小部件。

这个`save()`方法接受一个可选的`commit`关键字参数，该参数接受`True`或`False`。如果使用`commit=False`调用`save()`，那么它将返回一个尚未保存到数据库的对象。在本例中，由您来调用结果模型实例上的`save()`。如果您想在保存对象之前对其进行自定义处理，或者想使用专门的模型保存选项之一，那么这是非常有用的。默认情况下，`commit`为`True`。

当您的模型与另一个模型具有多对多关系时，使用`commit=False`的另一个副作用是可见的。如果您的模型具有多对多关系，并且在保存表单时指定`commit=False`, Django就不能立即为多对多关系保存表单数据。这是因为在实例存在于数据库中之前，不可能为实例保存许多到许多的数据。

为了解决这个问题，每次使用`commit=False`保存表单时，Django都会向`ModelForm`子类添加一个`save_m2m()`方法。在手动保存表单生成的实例之后，可以调用`save_m2m()`来保存多对多表单数据。例如:

```python
# 从Post数据创建表单实例
>>> f = AuthorForm(request.POST)

# 创建，但是不保存新作者实例
>>> new_author = f.save(commit=False)

# 用某些方法修改新作者实例
>>> new_author.some_field = 'some_value'

# 保存新作者实例
>>> new_author.save()

# 为表单保存多对多数据
>>> f.save_m2m()
```

只有在使用`save(commit=False)`时才需要调用`save_m2m()`。当您在表单上使用一个简单的`save()`时，所有数据(包括多对多数据)都被保存，而不需要任何额外的方法调用。例如:

```python
# 从Post数据创建表单实例
>>> a = Author()
>>> f = AuthorForm(request.POST, instance=a)

# 创建并保存新作者实例，不需要做其它任何事
>>> new_author = f.save()
```

除了`save()`和`save_m2m()`方法之外，`ModelForm`的工作方式与任何其他表单的工作方式完全相同。例如，`is_valid()`方法用于检查有效性，`is_multipart()`方法用于确定表单是否需要多部分文件上传(因此是否`request.FILES`必须传递到表单)，等等。有关更多信息，请参见将上载的文件绑定到表单。

###### 3.4.7.2.1.8. 选择要使用的字段

强烈建议您显式地设置应该使用`fields`属性在表单中编辑的所有字段。当表单意外地允许用户设置某些字段时，特别是在向模型添加新字段时，如果不这样做，很容易导致安全问题。根据表单的呈现方式，这个问题甚至可能在web页面上不可见。

另一种方法是自动包含所有字段，或者只将某些字段列入黑名单。众所周知，这种基本方法的安全性要低得多，并导致主要网站(如GitHub)受到严重攻击。

然而，当你可以保证这些安全问题不适用于你时，有两个快捷方式可用:

1. 将`fields`属性设置为特殊值`__all__`，以指示应该使用模型中的所有字段。例如:

```python
from django.forms import ModelForm

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = '__all__'
```

2. 将`ModelForm`的内部元类的`exclude`属性设置为要从表单中排除的字段列表。例如:

```python
class PartialAuthorForm(ModelForm):
    class Meta:
        model = Author
        exclude = ['title']
```

由于`Author`模型有3个字段`name`、`title`和`birth_date`，这将导致字段`name`和`birth_date`出现在表单上。

如果使用这两种方法中的任何一种，表单中字段出现的顺序将与模型中定义的字段的顺序相同，`ManyToManyField`实例最后出现。

此外，Django 应用了以下规则：如果在模型字段上设置`editable=False`，那么通过`ModelForm`从模型创建的任何表单都不会包含该字段。

注意:上面的逻辑没有包含在表单中的任何字段都不会被表单的`save()`方法设置。此外，如果手动将排除的字段添加回表单，则不会从模型实例初始化它们。

Django 将阻止保存不完整模型的任何尝试，因此，如果模型不允许丢失的字段为空，并且不为丢失的字段提供默认值，那么`save()`具有丢失字段的`ModelForm`的任何尝试都将失败。为了避免这种失败，你必须实例化你的模型与初始值的缺失，但必需的字段:

```python
author = Author(title='Mr')
form = PartialAuthorForm(request.POST, instance=author)
form.save()
```

或者，您可以使用`save(commit=False)`并手动设置任何额外的必需字段:

```python
form = PartialAuthorForm(request.POST)
author = form.save(commit=False)
author.title = 'Mr'
author.save()
```

有关使用`save(commit=False)`的详细信息，请参阅有关保存表单的部分。

###### 3.4.7.2.1.9. 覆盖缺省字段

如上面的字段类型表所述，默认字段类型是合理的默认值。如果您的模型中有一个`DateField`，那么您可能希望它在表单中表示为一个`DateField`。但是`ModelForm`为您提供了更改给定模型的表单字段的灵活性。

要为字段指定自定义小部件，请使用内部元类的`widgets`属性。这应该是一个字典，将字段名映射到小部件类或实例。

例如，如果您希望`Author`的`name`属性的`CharField`由`<textarea>`表示，而不是其默认的`<input type="text">`，您可以覆盖字段的小部件:

```python
from django.forms import ModelForm, Textarea
from myapp.models import Author

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = ('name', 'title', 'birth_date')
        widgets = {
            'name': Textarea(attrs={'cols': 80, 'rows': 20}),
        }
```

`widgets`字典接受小部件实例（例如`Textarea(…)`）或类（例如`Textarea`）。

类似地，如果希望进一步定制字段，可以指定内部元类的`labels`、`help_text`和`error_messages`属性。

例如，如果您想为`name`字段定制所有面向字符串的用户的措辞:

```python
from django.utils.translation import gettext_lazy as _

class AuthorForm(ModelForm):
    class Meta:
        model = Author
        fields = ('name', 'title', 'birth_date')
        labels = {
            'name': _('Writer'),
        }
        help_texts = {
            'name': _('Some useful help text.'),
        }
        error_messages = {
            'name': {
            'max_length': _("This writer's name is too long."),
            },
        }
```

您还可以指定`field_classes`来定制表单实例化的字段的类型。

例如，如果您想对`slug`字段使用`MySlugFormField`，您可以执行以下操作:

```python
from django.forms import ModelForm
from myapp.models import Article

class ArticleForm(ModelForm):
    class Meta:
        model = Article
        fields = ['pub_date', 'headline', 'content', 'reporter', 'slug']
        field_classes = {
            'slug': MySlugFormField,
        }
```

最后，如果您想完全控制字段——包括它的类型、验证器、`required`等等——您可以像在常规表单中那样声明性地指定字段。

如果你想指定一个字段的验证器，你可以通过声明的方式定义字段并设置它的验证器参数:

```python
from django.forms import CharField, ModelForm
from myapp.models import Article

class ArticleForm(ModelForm):
    slug = CharField(validators=[validate_slug])
    class Meta:
        model = Article
        fields = ['pub_date', 'headline', 'content', 'reporter', 'slug']
```

> 注意：当您显式地实例化这样一个表单字段时，理解`ModelForm`和常规表单之间的关系是很重要的。
>
> `ModelForm`是一种可以自动生成特定字段的常规表单。自动生成的字段依赖于元类的内容以及已经声明性定义的字段。
>
> 基本上，`ModelForm`只生成表单中缺少的字段，或者换句话说，生成没有声明式定义的字段。
>
> 以声明方式定义的字段保持原样，因此对元属性(如小部件、标签、`help_text`或`error_messages`)所做的任何自定义都将被忽略;这些只适用于自动生成的字段。
>
> 类似地，以声明方式定义的字段不会从相应的模型中绘制它们的属性，比如`max_length`或`required`。如果希望维护模型中指定的行为，则必须在声明表单字段时显式设置相关参数。
>
> 例如，如果`Article`模型看起来像这样:
>
>```python
>class Article(models.Model):
>    headline = models.CharField(
>        max_length=200,
>        null=True,
>        blank=True,
>        help_text='Use puns liberally',
>    )
>    content = models.TextField()
>```
>
>你想对标题做一些自定义验证，同时保留指定的`blank`和`help_text`值，你可以这样定义`ArticleForm`:
>
>```python
>class ArticleForm(ModelForm):
>    headline = MyFormField(
>        max_length=200,
>        required=False,
>        help_text='Use puns liberally',
>    )
>    class Meta:
>        model = Article
>        fields = ['headline', 'content']
>```
>
>您必须确保表单字段的类型可用于设置相应模型字段的内容。当它们不兼容时，您将得到一个`ValueError`，因为没有发生隐式转换。
>
>有关字段及其参数的更多信息，请参阅表单字段文档。

###### 3.4.7.2.1.10. 启用字段本地化

默认情况下，`ModelForm`中的字段不会本地化它们的数据。要启用字段的本地化，可以在元类上使用`localized_fields`属性。

```python
>>> from django.forms import ModelForm
>>> from myapp.models import Author
>>> class AuthorForm(ModelForm):
...     class Meta:
...         model = Author
...         localized_fields = ('birth_date',)
```

如果`localized_fields`被设置为特殊值`__all__`，那么所有字段都将被本地化。

###### 3.4.7.2.1.11. 表单继承

与基本表单一样，您可以通过继承模型表单来扩展和重用它们。如果您需要在父类上声明额外的字段或额外的方法，以便在从模型派生的许多表单中使用，那么这是非常有用的。例如，使用前面的`ArticleForm`类:

```python
>>> class EnhancedArticleForm(ArticleForm):
...     def clean_pub_date(self):
...         ...
```

这将创建一个与`ArticleForm`行为相同的表单，只不过`pub_date`字段需要进行一些额外的验证和清理。

如果你想更改`Meta.fields`或`Meta.exclude`列表，还可以子类化父类的`Meta`内部类：

```python
>>> class RestrictedArticleForm(EnhancedArticleForm):
...     class Meta(ArticleForm.Meta):
...         exclude = ('body',)
```

这将从`EnhancedArticleForm`中添加额外的方法，并修改原始`ArticleForm.Meta`，删除一个字段。

然而，有几件事需要注意。

- 应用普通 Python 名称解析规则。如果有多个基类声明一个`Meta`内部类，则只使用第一个基类。这意味着子元素的`Meta`元素如果不存在，就是第一个父元素的`Meta`元素，等等。

- 可以同时从`Form`和`ModelForm`继承，但是，您必须确保`ModelForm`首先出现在`MRO`中。这是因为这些类依赖于不同的元类，而一个类只能有一个元类。

- 通过将子类字段的名称设置为`None`，可以声明性地删除从父类继承的字段。

您只能使用此技术从父类声明定义的字段中选择退出。它不会阻止`ModelForm`元类生成一个默认字段。若要从默认字段选择退出，请参见《选择要使用的字段》。

###### 3.4.7.2.1.12. 提供初始值

与常规表单一样，可以通过在实例化表单时指定初始参数来指定表单的初始数据。以这种方式提供的初值将覆盖表单字段中的初值和附加模型实例中的值。例如:

```python
>>> article = Article.objects.get(pk=1)
>>> article.headline
'My headline'
>>> form = ArticleForm(initial={'headline': 'Initial headline'}, instance=article)
>>> form['headline'].value()
'Initial headline'
```

###### 3.4.7.2.1.13. `ModelForm`工厂函数

您可以使用独立函数`modelform_factory()`从给定模型创建表单，而不是使用类定义。如果你没有太多的自定义，这可能会更方便:

```python
>>> from django.forms import modelform_factory
>>> from myapp.models import Book
>>> BookForm = modelform_factory(Book, fields=("author", "title"))
```

这也可以用来对现有的表单进行简单的修改，例如指定要用于给定字段的小部件:

```python
>>> from django.forms import Textarea
>>> Form = modelform_factory(Book, form=BookForm,
... widgets={"title": Textarea()})
```

要包含的字段可以使用`fields`和`exclude`关键字参数，或者`ModelForm`内部`Meta`类上相应的属性。请参见`ModelForm`《选择要使用文档的字段》。

或为特定字段启用本地化:

```python
>>> Form = modelform_factory(Author, form=AuthorForm, localized_fields=("birth_date",))
```

##### 3.4.7.2.2. 模型表单集
###### 3.4.7.2.2.1. Changing the queryset
###### 3.4.7.2.2.2. Changing the form
###### 3.4.7.2.2.3. Specifying widgets to use in the form with widgets
###### 3.4.7.2.2.4. Enabling localization for fields with localized_fields
###### 3.4.7.2.2.5. Providing initial values
###### 3.4.7.2.2.6. Saving objects in the formset
###### 3.4.7.2.2.7. Limiting the number of editable objects
###### 3.4.7.2.2.8. Using a model formset in a view
###### 3.4.7.2.2.9. Overriding clean() on a ModelFormSet
###### 3.4.7.2.2.10. Using a custom queryset
###### 3.4.7.2.2.11. Using the formset in the template

##### 3.4.7.2.3. 初始化表单集
###### 3.4.7.2.3.1. Overriding methods on an InlineFormSet
###### 3.4.7.2.3.2. More than one foreign key to the same model
###### 3.4.7.2.3.3. Using an inline formset in a view
###### 3.4.7.2.3.4. Specifying widgets to use in the inline form

#### 3.4.7.3. 表单资源（媒体类）

呈现一个吸引人且易于使用的 Web 表单需要的不仅仅是 HTML ——它还需要 CSS 样式表，如果您想使用花哨的“Web2.0”小部件，您可能还需要在每个页面上包含一些 JavaScript。任何给定页面所需的 CSS 和 JavaScript 的确切组合将取决于该页面上使用的小部件。

这就是资产定义的用武之地。Django 允许您将不同的文件(比如样式表和脚本)与需要这些资产的表单和小部件关联起来。例如，如果希望使用日历呈现`DateFields`，可以定义一个自定义`Calendar`小部件。然后，这个小部件可以与呈现日历所需的`CSS`和`JavaScript`相关联。当在表单上使用`Calendar`小部件时，Django 能够识别所需的`CSS`和`JavaScript`文件，并以适合在 Web 页面上轻松包含的形式提供文件名列表。

> **资产和 Django 管理**
>
> Django管理应用程序为日历、筛选的选择等定义了许多定制的小部件。这些小部件定义了资产需求，Django管理员使用定制小部件替代Django缺省值。管理模板将只包含在任何给定页面上呈现小部件所需的文件。
>
> 如果您喜欢Django管理应用程序使用的小部件，可以在自己的应用程序中使用它们。它们都存储在`django.contrib.admin.widgets`中。

> **JavaScript工具箱**
>
> 存在许多 JavaScript 工具包，其中许多包含小部件(如日历小部件)，可用于增强应用程序。Django 故意避免支持任何一个 JavaScript 工具包。每个工具包都有自己的相对优势和弱点——使用任何适合您需求的工具包。Django 能够与任何 JavaScript 工具包集成。

##### 3.4.7.3.1. 静态定义资产

将资产定义为静态定义是最简单的方法。使用此方法，声明是一个内部`Media`类。

内部类的属性定义了需求。

这里有一个简单的例子:

```python
from django import forms

class CalendarWidget(forms.TextInput):
    class Media:
        css = {
        'all': ('pretty.css',)
        }
        js = ('animations.js', 'actions.js')
```

这段代码定义了一个`calendar`小部件，它将基于`TextInput`。每当在表单上使用`CalendarWidget`时，该表单都会被指向 CSS 文件`pretty.css` 文件，和 Javascript 文件`animation.js`、`actions.js`。

这个静态定义在运行时转换为一个名为`media`的小部件属性。`calendar`小部件实例的资产列表可以通过此属性检索:

```bash
>>> w = CalendarWidget()
>>> print(w.media)
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://static.example.com/actions.js"></script>
```

这里列出了所有可能的`Media`选项。没有必须的选项。

###### 3.4.7.3.1.1. css

描述各种形式输出媒体所需的 CSS 文件的字典。

字典中的值应该是一个元组/文件名列表。有关如何指定这些文件的路径的详细信息，请参阅“路径”一节。

字典中的键是输出媒体类型。这些是CSS文件在媒体声明中接受的相同类型：`all`、`aural`、`braille`、`embossed`、`handheld`、`print`、`projection`、`screen`、`tty`和`tv`。如果需要为不同的媒体类型提供不同的样式表，请为每个输出媒体提供 CSS 文件列表。

下面的例子将提供两个 CSS 选项——一个用于屏幕，另一个用于打印：

```python
class Media:
    css = {
        'screen': ('pretty.css',),
        'print': ('newspaper.css',)
    }
```

如果一组 CSS 文件适用于多种输出媒体类型，字典键可以是输出媒体类型的逗号分隔列表。在下面的例子中，电视和投影机将有相同的媒体要求:

```python
class Media:
    css = {
        'screen': ('pretty.css',),
        'tv,projector': ('lo_res.css',),
        'print': ('newspaper.css',)
    }
```

如果要呈现最后一个CSS定义，它将变成如下HTML:

```html
<link href="http://static.example.com/pretty.css" type="text/css" media="screen" rel="stylesheet">
<link href="http://static.example.com/lo_res.css" type="text/css" media="tv,projector" rel="stylesheet">
<link href="http://static.example.com/newspaper.css" type="text/css" media="print"rel="stylesheet">
```

###### 3.4.7.3.1.2. js

描述所需 JavaScript 文件的元组。有关如何指定这些文件的路径的详细信息，请参阅“路径”一节。

###### 3.4.7.3.1.3. extend

为媒体声明定义继承行为的布尔值。

默认情况下，任何使用静态媒体定义的对象都将继承与父小部件关联的所有资产。

无论父类如何定义自己的需求，都会发生这种情况。例如，如果我们从上面的例子扩展我们的基本日历小部件:

```python
>>> class FancyCalendarWidget(CalendarWidget):
... class Media:
... css = {'all': ('fancy.css',)}
... js = ('whizbang.js',)
>>> w = FancyCalendarWidget()
>>> print(w.media)
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
<link href="http://static.example.com/fancy.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://static.example.com/actions.js"></script>
<script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
```

`FancyCalendar`小部件从其父小部件继承所有资产。如果不希望以这种方式继承媒体，请在媒体声明中添加一个`extend=False`声明:

```python
>>> class FancyCalendarWidget(CalendarWidget):
... class Media:
... extend = False
... css = { 'all': ('fancy.css',) }
... js = ('whizbang.js',)
>>> w = FancyCalendarWidget()
>>> print(w.media)
<link href="http://static.example.com/fancy.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
```

如果您需要对继承进行更多的控制，请使用动态属性定义您的资产。动态属性使您能够完全控制哪些文件是继承的，哪些不是。

##### 3.4.7.3.2. 作为动态属性的媒体

如果需要对资产需求执行更复杂的操作，可以直接定义`media`属性。这是通过定义一个返回`forms.Media`实例的小部件属性来实现的。`forms.Media`的构造函数接受与静态媒体定义中使用的格式相同的 css 和 js 关键字参数。

例如，我们的日历小部件的静态定义也可以用动态的方式定义:

```python
class CalendarWidget(forms.TextInput):
    @property
    def media(self):
        return forms.Media(css={'all': ('pretty.css',)}, js=('animations.js', 'actions.js'))
```

有关如何为动态媒体属性构造返回值的详细信息，请参阅“媒体对象”一节。

##### 3.4.7.3.3. 资产定义中的路径

用于指定资产的路径可以是相对的，也可以是绝对的。如果路径以`/`、`http://`或`https://`开头，它将被解释为绝对路径，并保持原样。所有其他路径都将以适当前缀的值作为前缀。如果`django.contrib.staticfiles`应用程序，它将用于服务资产。

不管你是否使用`django.contrib.staticfiles`，要呈现完整的web页面，需要使用`STATIC_URL`和`STATIC_ROOT`设置。

为了找到合适的前缀，Django将检查`STATIC_URL`设置是否为`None`，并自动返回到使用`MEDIA_URL`。例如，如果您的站点的`MEDIA_URL`是`http://uploads.example.com/`，而`STATIC_URL`是`None`:

```python
>>> from django import forms
>>> class CalendarWidget(forms.TextInput):
... class Media:
... css = { 'all': ('/css/pretty.css',) }
... js = ('animations.js', 'http://othersite.com/actions.js')
>>> w = CalendarWidget()
>>> print(w.media)
<link href="/css/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://uploads.example.com/animations.js"></script>
<script type="text/javascript" src="http://othersite.com/actions.js"></script>
```

但是如果`STATIC_URL`是`'http://static.example.com/'`:

```python
>>> w = CalendarWidget()
>>> print(w.media)
<link href="/css/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://othersite.com/actions.js"></script>
```

或者如果使用`manifestStstaticfilesstorage`配置`staticfiles`：

```python
>>> w = CalendarWidget()
>>> print(w.media)
<link href="/css/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="https://static.example.com/animations.27e20196a850.js"></script>
<script type="text/javascript" src="http://othersite.com/actions.js"></script>
```

##### 3.4.7.3.4. 媒体对象

当查询小部件或表单的媒体属性时，返回的值是一个表单。媒体对象。

正如我们已经看到的，媒体对象的字符串表示形式是在HTML页面的`<head>`块中包含相关文件所需的HTML。

然而，媒体对象还有一些其他有趣的属性。

###### 3.4.7.3.4.1. 资产的子集

如果只想要特定类型的文件，可以使用下标操作符过滤掉感兴趣的媒体。例如:

```python
>>> w = CalendarWidget()
>>> print(w.media)
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://static.example.com/actions.js"></script>
>>> print(w.media['css'])
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
```

当您使用下标操作符时，返回的值是一个新媒体对象——但只包含感兴趣的媒体。

###### 3.4.7.3.4.2. 结合媒体对象

还可以将媒体对象添加到一起。当添加两个媒体对象时，得到的媒体对象包含两者指定的资产的并集:

```python
>>> from django import forms
>>> class CalendarWidget(forms.TextInput):
... class Media:
... css = { 'all': ('pretty.css',) }
... js = ('animations.js', 'actions.js')
>>> class OtherWidget(forms.TextInput):
... class Media:
... js = ('whizbang.js',)
>>> w1 = CalendarWidget()
>>> w2 = OtherWidget()
>>> print(w1.media + w2.media)
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://static.example.com/actions.js"></script>
<script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
```

###### 3.4.7.3.4.3. 排序资产

将资产插入DOM的顺序通常很重要。例如，您可能有一个依赖于`jQuery`的脚本。因此，组合媒体对象试图保持在每个媒体类中定义资产的相对顺序。

例如:

```python
>>> from django import forms
>>> class CalendarWidget(forms.TextInput):
... class Media:
... js = ('jQuery.js', 'calendar.js', 'noConflict.js')
>>> class TimeWidget(forms.TextInput):
... class Media:
... js = ('jQuery.js', 'time.js', 'noConflict.js')
>>> w1 = CalendarWidget()
>>> w2 = TimeWidget()
>>> print(w1.media + w2.media)
<script type="text/javascript" src="http://static.example.com/jQuery.js"></script>
<script type="text/javascript" src="http://static.example.com/calendar.js"></script>
<script type="text/javascript" src="http://static.example.com/time.js"></script>
<script type="text/javascript" src="http://static.example.com/noConflict.js"></script>
```

将媒体对象与资产以冲突的顺序组合在一起会导致媒体冲突。

在较早的版本中，媒体对象的资产以一种试图保持每个列表中元素的相对顺序的方式连接而不是合并。

##### 3.4.7.3.5. 表单上的媒体

小部件并不是唯一具有媒体定义的对象——表单也可以定义媒体。表单上媒体定义的规则与小部件的规则相同：声明可以是静态的，也可以是动态的，这些声明的路径和继承规则完全相同。

无论您是否定义了媒体声明，所有表单对象都具有媒体属性。此属性的默认值是为窗体中的所有小部件添加媒体定义的结果：

```python
>>> from django import forms
>>> class ContactForm(forms.Form):
... date = DateField(widget=CalendarWidget)
... name = CharField(max_length=40, widget=OtherWidget)
>>> f = ContactForm()
>>> f.media
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://static.example.com/actions.js"></script>
<script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
```

如果你想将额外的资产与表单相关联——例如，CSS用于表单布局——只需在表单中添加一个媒体声明:

```python
>>> class ContactForm(forms.Form):
... date = DateField(widget=CalendarWidget)
... name = CharField(max_length=40, widget=OtherWidget)
... class Media:
... css = {'all': ('layout.css',)}
>>> f = ContactForm()
>>> f.media
<link href="http://static.example.com/pretty.css" type="text/css" media="all" rel="stylesheet">
<link href="http://static.example.com/layout.css" type="text/css" media="all" rel="stylesheet">
<script type="text/javascript" src="http://static.example.com/animations.js"></script>
<script type="text/javascript" src="http://static.example.com/actions.js"></script>
<script type="text/javascript" src="http://static.example.com/whizbang.js"></script>
```

参见:

《表单参考》包含完整的API参考，包括表单字段、表单小部件以及表单和字段验证。

## 3.5. 模板
### 3.5.1. 支持模板引擎
#### 3.5.1.1. 配置
#### 3.5.1.2. 使用
#### 3.5.1.3. 内建后端
#### 3.5.1.4. 定制后端
#### 3.5.1.5. 定制后端的调试集成
##### 3.5.1.5.1. Template postmortem
##### 3.5.1.5.2. 上下文行信息
##### 3.5.1.5.3. 原始API和第三方集成
### 3.5.2. Django 模板语言
#### 3.5.2.1. Syntax
##### 3.5.2.1.1. Variables
##### 3.5.2.1.2. Tags
##### 3.5.2.1.3. Filters
##### 3.5.2.1.4. Comments
#### 3.5.2.2. Components
##### 3.5.2.2.1. Engine
##### 3.5.2.2.2. Template
##### 3.5.2.2.3. Context
##### 3.5.2.2.4. Loaders
##### 3.5.2.2.5. Context processors




## 3.6. 基于类的视图

视图是可调用的，它接受请求并返回响应。这不仅仅是一个函数，Django提供了一些可以用作视图的类的例子。通过利用继承和`mixin`，您可以构造视图并重用代码。还有一些用于简单任务的通用视图，我们将在稍后讨论，但是您可能希望设计自己的可重用视图结构，以适合您的用例。

### 3.6.1. 基于类的视图介绍

基于类的视图提供了将视图实现为Python对象而不是函数的另一种方法。它们不取代基于函数的视图，但与基于函数的视图相比，具有一定的区别和优势:

- 与特定HTTP方法(GET、POST等)相关的代码组织可以通过单独的方法而不是条件分支来解决。

- 面向对象的技术，如`mixin`(多重继承)，可用于将代码分解为可重用组件。

#### 3.6.1.1. 通用视图、类视图、类通用视图之间的关系和历史

最初只有视图函数契约，Django 向函数传递了一个`HttpRequest`，并期望返回一个`HttpResponse`。这就是 Django 所提供的。

在早期，人们认识到在视图开发中有一些常见的习惯用法和模式。引入了基于函数的通用视图来抽象这些模式，并简化了常见情况下的视图开发。

基于函数的通用视图的问题在于，虽然它们很好地涵盖了简单的情况，但是除了一些简单的配置选项之外，没有其他方法可以扩展或定制它们，从而限制了它们在许多实际应用程序中的实用性。

基于类的通用视图与基于函数的通用视图具有相同的目标，从而使视图开发更加容易。然而，通过使用`mixin`实现解决方案的方法提供了一个工具包，使得基于类的通用视图比基于函数的视图更具可扩展性和灵活性。

如果您在过去尝试过基于函数的通用视图，但是发现它们缺乏功能，那么不应该将基于类的通用视图视为简单的基于类的等效视图，而应该将其视为解决通用视图待解决的原始问题的新方法。

Django 用于构建基于类的通用视图的基类和`mixin`工具包是为最大的灵活性而构建的，因此有许多以默认方法实现和属性形式出现的挂钩，在最简单的用例中，您不太可能关心这些。

例如，该实现使用`get_form`方法，而不是将您限制为基于类的`form_class`属性，该方法调用`get_form_class`方法，该方法在其默认实现中只返回该类的`form_class`属性。这为您提供了几个选项来指定要使用的表单，从简单的属性到完全动态的、可调用的钩子。

这些选项似乎为简单的情况增加了空洞的复杂性，但是没有它们，更高级的设计将受到限制。

#### 3.6.1.2. 使用基于类的视图

在其核心中使用基于类的视图，基于类的视图允许您使用不同的类实例方法响应不同的HTTP请求方法，而不是在单个视图函数中使用有条件的分支代码。

因此，在视图函数中处理HTTP GET的代码应该是这样的:

```python
from django.http import HttpResponse

def my_view(request):
    if request.method == 'GET':
        # <view logic>
        return HttpResponse('result')
```

在基于类的视图中，这将变成:

```python
from django.http import HttpResponse
from django.views import View

class MyView(View):
    def get(self, request):
        # <view logic>
        return HttpResponse('result')
```

因为 Django 的 URL 解析器期望将请求和关联的参数发送给可调用的函数，而不是类，所以基于类的视图有一个`as_view()`类方法，它返回一个函数，当一个匹配关联模式的 URL 请求到达时，可以调用这个函数。该函数创建类的实例并调用其`dispatch()`方法。`dispatch`查看请求以确定它是否是`GET`、`POST`等，如果定义了匹配方法，则将请求转发给匹配方法，如果没有定义匹配方法，则引发`httpresponsenotal`:

```python
# urls.py
from django.urls import path
from myapp.views import MyView

urlpatterns = [
    path('about/', MyView.as_view()),
]
```

值得注意的是，您的方法返回的内容与您从基于函数的视图(即某种形式的`HttpResponse`)返回的内容相同。这意味着`http`快捷方式或`TemplateResponse`对象可以在基于类的视图中有效使用。

虽然一个最小的基于类的视图不需要任何类属性来执行它的工作，但是类属性在许多基于类的设计中是有用的，并且有两种方法来配置或设置类属性。

第一种是标准的 Python 方法，用于子类化和覆盖子类中的属性和方法。如果你的父类有这样一个属性`greeting`:

```python
from django.http import HttpResponse
from django.views import View

class GreetingView(View):
    greeting = "Good Day"
    def get(self, request):
        return HttpResponse(self.greeting)
```

你可以在子类中重写它:

```python
class MorningGreetingView(GreetingView):
    greeting = "Morning to ya"
```

另一个选项是将类属性配置为`URLconf`中的`as_view()`调用的关键字参数:

```python
urlpatterns = [
    path('about/', GreetingView.as_view(greeting="G'day")),
]
```

> 注意:当您的类为每个发送给它的请求实例化时，通过`as_view()`入口点设置的类属性只在导入url时配置一次。

#### 3.6.1.3. 使用`mixins`

`mixin`是一种多重继承的形式，其中可以组合多个父类的行为和属性。

例如，在基于类的通用视图中有一个名为`TemplateResponseMixin`的`mixin`，其主要目的是定义`render_to_response()`方法。当结合`View`基类的行为,结果是一个`TemplateView`类，它将分派请求到适当的匹配方法（`View`基类中定义的行为），并有`render_to_response()`方法，它使用`template_name`属性返回一个`TemplateResponse`对象(`TemplateResponseMixin`中定义的行为)。

`mixin`是跨多个类重用代码的一种很好的方法，但是它们也带来了一些成本。代码分散在`mixin`越多，越难通过会读一个子类，知道它是怎样实现的，难度是，你子类化的东西有一个继承树，知道哪些`mixin`中的哪些方法被覆盖。

还要注意，您只能从一个通用视图继承——也就是说，只有一个父类可以从视图继承，其余的（如果有的话）应该是`mixin`。尝试从从继承自`View`的多个视图继承——例如，尝试在列表顶部使用一个表单并组合`ProcessFormView和ListView`——不会像预期的那样工作。

#### 3.6.1.4. 使用基于类的视图处理表单

使用基于函数的视图处理表单大致是这样的：

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from .forms import MyForm

def myview(request):
    if request.method == "POST":
        form = MyForm(request.POST)
        if form.is_valid():
            # <process form cleaned data>
            return HttpResponseRedirect('/success/')
    else:
        form = MyForm(initial={'key': 'value'})        
    return render(request, 'form_template.html', {'form': form})
```

类似的基于类的视图可能如下所示:

```python
from django.http import HttpResponseRedirect
from django.shortcuts import render
from django.views import View
from .forms import MyForm

class MyFormView(View):
    form_class = MyForm
    initial = {'key': 'value'}
    template_name = 'form_template.html'

    def get(self, request, *args, **kwargs):
        form = self.form_class(initial=self.initial)
        return render(request, self.template_name, {'form': form})

    def post(self, request, *args, **kwargs):
        form = self.form_class(request.POST)
        if form.is_valid():
            # <process form cleaned data>
            return HttpResponseRedirect('/success/')
        return render(request, self.template_name, {'form': form})
```

这是一个非常简单的例子，但是您可以看到您可以通过覆盖任何类属性来定制这个视图，例如通过`URLconf`配置来覆盖`form_class`，或者子类化和覆盖一个或多个方法。

#### 3.6.1.5. Decorating class-based views

基于类的视图的扩展并不仅限于使用`mixin`。您还可以使用装饰器。由于基于类的视图不是函数，所以根据您是使用`as_view()`还是创建子类，装饰它们的工作方式会有所不同。

##### 3.6.1.5.1. Decorating in URLconf
##### 3.6.1.5.2. Decorating the class

### 3.6.2. 内建的基于类的通用视图
#### 3.6.2.1. Extending generic views
#### 3.6.2.2. Generic views of objects
##### 3.6.2.2.1. Making 'friendly' template contexts
##### 3.6.2.2.2. Adding extra context
##### 3.6.2.2.3. Viewing subsets of objects
##### 3.6.2.2.4. Dynamic filtering
##### 3.6.2.2.5. Performing extra work

### 3.6.3. 基于类的视图的表单处理

表单处理一般有3条路径:

- 初始GET(空白或预填充表单)

- 发布无效数据(通常是带有错误的重新显示表单)

- 发布有效数据(处理数据，通常重定向)

自己实现这一点通常会导致大量重复的样板代码（参见《在视图中使用表单》）。为了避免这种情况，Django 提供了一组用于表单处理的基于类的通用视图。

#### 3.6.3.1. 基本表单

联系方式表单：

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField()
    message = forms.CharField(widget=forms.Textarea)
    def send_email(self):
        # send email using the self.cleaned_data dictionary
        pass
```

视图可以使用`FormView`构造:

```python
from myapp.forms import ContactForm
from django.views.generic.edit import FormView

class ContactView(FormView):
    template_name = 'contact.html'
    form_class = ContactForm
    success_url = '/thanks/'
    def form_valid(self, form):
        # 有效表单数据被POSTed时，此方法被调用，它返回一个HttpResponse
        form.send_email()
        return super().form_valid(form)
```

注:

- `FormView`继承了`TemplateResponseMixin`，因此可以在这里使用`template_name`。
- `form_valid()`的默认实现只是重定向到`success_url`。

#### 3.6.3.2. 模型表单

通用视图在处理模型时非常出色。这些通用视图将自动创建一个`ModelForm`，只要它们能够计算出使用哪个模型类:

- 如果给定模型属性，则使用模型类。
- 如果`get_object()`返回一个对象，则使用该对象的类。
- 如果给定`queryset`，将使用该`queryset`的模型。

模型表单视图提供一个`form_valid()`实现，该实现自动保存模型。如果您有任何特殊要求，您可以覆盖它;请看下面的例子。

您甚至不需要为`CreateView`或`UpdateView`提供`success_url`—如果模型对象可用，它们将使用`get_absolute_url()`。

如果您想使用自定义`ModelForm`(例如添加额外的验证)，只需在视图上设置`form_class`。

> 注意:在指定自定义表单类时，必须仍然指定模型，即使`form_class`可能是一个`ModelForm`。

首先，我们需要将`get_absolute_url()`添加到我们的`Author`类中:

```python
from django.db import models
from django.urls import reverse

class Author(models.Model):
    name = models.CharField(max_length=200)
    def get_absolute_url(self):
        return reverse('author-detail', kwargs={'pk': self.pk})
```

然后我们可以使用`CreateView`和`friends`来做实际的工作。注意我们如何在这里配置通用的基于类的视图;我们不需要自己写任何逻辑:

```python
from django.urls import reverse_lazy
from django.views.generic.edit import CreateView, DeleteView, UpdateView
from myapp.models import Author

class AuthorCreate(CreateView):
    model = Author
    fields = ['name']

class AuthorUpdate(UpdateView):
    model = Author
    fields = ['name']

class AuthorDelete(DeleteView):
    model = Author
    success_url = reverse_lazy('author-list')
```

> 注意:这里我们必须使用`reverse_lazy()`，而不仅仅是`reverse()`，因为导入文件时`url`没有加载。

`fields`属性的工作方式与`ModelForm`内部元类上的`fields`属性相同。除非以另一种方式定义表单类，否则属性是必需的，否则视图将引发`ImproperlyConfigured`异常。

如果同时指定`fields`和`form_class`属性，将会引发`ImproperlyConfigured`异常。

最后，我们将这些新视图连接到`URLconf`:

```python
from django.urls import path
from myapp.views import AuthorCreate, AuthorDelete, AuthorUpdate

urlpatterns = [
    # ...
    path('author/add/', AuthorCreate.as_view(), name='author-add'),
    path('author/<int:pk>/', AuthorUpdate.as_view(), name='author-update'),
    path('author/<int:pk>/delete/', AuthorDelete.as_view(), name='author-delete'),
]
```

注意:这些视图继承了`SingleObjectTemplateResponseMixin`，它使用`template_name_suffix`根据模型构造`template_name`。

在这个例子中:

- `CreateView`和`UpdateView`使用`myapp/author_form.html`

- `DeleteView`使用`myapp/author_confirm_delete.html`

如果希望为`CreateView`和`UpdateView`创建单独的模板，可以在视图类上设置`template_name`或`template_name_suffix`。

#### 3.6.3.3. 模型与`request.user`

要跟踪使用`CreateView`创建对象的用户，可以使用自定义`ModelForm`来完成此任务。首先，将外键关系添加到模型中：

```python
from django.contrib.auth.models import User
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=200)
    created_by = models.ForeignKey(User, on_delete=models.CASCADE)
    # ...
```

在视图中，确保要编辑的字段列表中不包含`created_by`，并覆盖`form_valid()`来添加用户:

```python
from django.views.generic.edit import CreateView
from myapp.models import Author

class AuthorCreate(CreateView):
    model = Author
    fields = ['name']
    def form_valid(self, form):
        form.instance.created_by = self.request.user
        return super().form_valid(form)
```

#### 3.6.3.4. AJAX 示例

下面是一个简单的例子，展示了如何实现一个表单，它既适用于AJAX请求，也适用于“普通”`POST`:

```python
from django.http import JsonResponse
from django.views.generic.edit import CreateView
from myapp.models import Author

class AjaxableResponseMixin:
    # Mixin将AJAX支持添加到表单中。必须与基于对象的FormView(例如CreateView)一起使用

    def form_invalid(self, form):
        response = super().form_invalid(form)
        if self.request.is_ajax():
            return JsonResponse(form.errors, status=400)
        else:
            return response

    def form_valid(self, form):
        # 我们确保调用父类的form_valid()方法，因为它可能执行一些处理(例如，对于CreateView，它将调用form.save())。
        response = super().form_valid(form)
        if self.request.is_ajax():
            data = {
                'pk': self.object.pk,
            }
            return JsonResponse(data)
        else:
            return response

class AuthorCreate(AjaxableResponseMixin, CreateView):
    model = Author
    fields = ['name']
```






### 3.6.4. 在基于类的视图中使用`mixins`
#### 3.6.4.1. Context and template responses
#### 3.6.4.2. Building up  Django 's generic class-based views
##### 3.6.4.2.1. DetailView: working with a single Django object
##### 3.6.4.2.2. ListView: working with many Django objects
#### 3.6.4.3. Using  Django 's class-based view mixins
##### 3.6.4.3.1. Using SingleObjectMixin with View
##### 3.6.4.3.2. Using SingleObjectMixin with ListView
#### 3.6.4.4. Avoid anything more complex
##### 3.6.4.4.1. Using FormMixin with DetailView
##### 3.6.4.4.2. A better solution
##### 3.6.4.4.3. An alternative better solution
#### 3.6.4.5. More than just HTML

### 3.6.5. 基本示例
### 3.6.6. Simple usage in your URLconf
### 3.6.7. Subclassing generic views
#### 3.6.7.1. Supporting other HTTP methods




## 3.7. 迁移
### 3.7.1. The Commands
### 3.7.2. Backend Support
#### 3.7.2.1. PostgreSQL
#### 3.7.2.2. MySQL
#### 3.7.2.3. SQLite
### 3.7.3. Workflow
#### 3.7.3.1. Version control
### 3.7.4. Dependencies
### 3.7.5. Migration files
#### 3.7.5.1. Custom fields
#### 3.7.5.2. Model managers
#### 3.7.5.3. Initial migrations
#### 3.7.5.4. History consistency
### 3.7.6. Adding migrations to apps
### 3.7.7. Historical models
### 3.7.8. Considerations when removing model fields
### 3.7.9. Data Migrations
#### 3.7.9.1. Accessing models from other apps
#### 3.7.9.2. More advanced migrations
### 3.7.10. Squashing migrations
### 3.7.11. Serializing values
#### 3.7.11.1. Adding a deconstruct() method
### 3.7.12. Supporting multiple Django versions




## 3.8. 管理文件
### 3.8.1. Using files in models
### 3.8.2. The File object
### 3.8.3. File storage
#### 3.8.3.1. Storage objects
#### 3.8.3.2. The built-in filesystem storage class




## 3.9. 测试
### 3.9.1. Writing and running tests
#### 3.9.1.1. Writing tests
#### 3.9.1.2. Running tests
#### 3.9.1.3. The test database
#### 3.9.1.4. Order in which tests are executed
#### 3.9.1.5. Rollback emulation
#### 3.9.1.6. Other test conditions
#### 3.9.1.7. Understanding the test output
#### 3.9.1.8. Speeding up the tests
##### 3.9.1.8.1. Running tests in parallel
##### 3.9.1.8.2. Password hashing
##### 3.9.1.8.3. Preserving the test database
### 3.9.2. Testing tools
#### 3.9.2.1. The test client
##### 3.9.2.1.1. Overview and a 简单的例子
##### 3.9.2.1.2. Making requests
##### 3.9.2.1.3. Testing responses
##### 3.9.2.1.4. Exceptions
##### 3.9.2.1.5. Persistent state
##### 3.9.2.1.6. Setting the language
##### 3.9.2.1.7. Example
#### 3.9.2.2. Provided test case classes
##### 3.9.2.2.1. SimpleTestCase
##### 3.9.2.2.2. TransactionTestCase
##### 3.9.2.2.3. TestCase
##### 3.9.2.2.4. LiveServerTestCase
#### 3.9.2.3. Test cases features
##### 3.9.2.3.1. Default test client
##### 3.9.2.3.2. Customizing the test client
##### 3.9.2.3.3. Fixture loading
##### 3.9.2.3.4. URLconf configuration
##### 3.9.2.3.5. Multi-database support
##### 3.9.2.3.6. Overriding settings
##### 3.9.2.3.7. Emptying the test outbox
##### 3.9.2.3.8. Assertions
##### 3.9.2.3.9. Tagging tests
#### 3.9.2.4. Email services
#### 3.9.2.5. Management Commands
#### 3.9.2.6. Skipping tests
### 3.9.3. Advanced testing topics
#### 3.9.3.1. The request factory
##### 3.9.3.1.1. Example
#### 3.9.3.2. Tests and multiple host names
#### 3.9.3.3. Tests and multiple databases
##### 3.9.3.3.1. Testing primary/replica configurations
##### 3.9.3.3.2. Controlling creation order for test databases
#### 3.9.3.4. Advanced features of TransactionTestCase
#### 3.9.3.5. Using the Django test runner to test reusable applications
#### 3.9.3.6. Using different testing frameworks
##### 3.9.3.6.1. Defining a test runner
###### 3.9.3.6.1.1. Attributes
###### 3.9.3.6.1.2. Methods
##### 3.9.3.6.2. Testing utilities
###### 3.9.3.6.2.1. django.test.utils
###### 3.9.3.6.2.2. django.db.connection.creation
##### 3.9.3.6.3. Integration with coverage.py




## 3.10. 用户认证
### 3.10.1. Using the Django authentication system
#### 3.10.1.1. User objects
##### 3.10.1.1.1. Creating users
##### 3.10.1.1.2. Creating superusers
##### 3.10.1.1.3. Changing passwords
##### 3.10.1.1.4. Authenticating users
#### 3.10.1.2. Permissions and Authorization
##### 3.10.1.2.1. Default permissions
##### 3.10.1.2.2. Groups
##### 3.10.1.2.3. Programmatically creating permissions
##### 3.10.1.2.4. Permission caching
#### 3.10.1.3. Authentication in Web requests
##### 3.10.1.3.1. How to log a user in
###### 3.10.1.3.1.1. Selecting the authentication backend
##### 3.10.1.3.2. How to log a user out
##### 3.10.1.3.3. Limiting access to logged-in users
###### 3.10.1.3.3.1. The raw way
###### 3.10.1.3.3.2. The login_required decorator
###### 3.10.1.3.3.3. The LoginRequired mixin
###### 3.10.1.3.3.4. Limiting access to logged-in users that pass a test
###### 3.10.1.3.3.5. The permission_required decorator
###### 3.10.1.3.3.6. The PermissionRequiredMixin mixin
##### 3.10.1.3.4. Redirecting unauthorized requests in class-based views
###### 3.10.1.3.4.1. Session invalidation on password change
##### 3.10.1.3.5. Authentication Views
###### 3.10.1.3.5.1. Using the views
###### 3.10.1.3.5.2. All authentication views
##### 3.10.1.3.6. Helper functions
##### 3.10.1.3.7. Built-in forms
##### 3.10.1.3.8. Authentication data in templates
###### 3.10.1.3.8.1. Users
###### 3.10.1.3.8.2. Permissions
#### 3.10.1.4. Managing users in the admin
##### 3.10.1.4.1. Creating users
##### 3.10.1.4.2. Changing passwords
### 3.10.2. Password management in  Django 
#### 3.10.2.1. How Django stores passwords
##### 3.10.2.1.1. Using Argon2 with  Django 
##### 3.10.2.1.2. Using bcrypt with  Django 
##### 3.10.2.1.3. Increasing the work factor
###### 3.10.2.1.3.1. PBKDF2 and bcrypt
###### 3.10.2.1.3.2. Argon2
##### 3.10.2.1.4. Password upgrading
##### 3.10.2.1.5. Password upgrading without requiring a login
##### 3.10.2.1.6. Included hashers
##### 3.10.2.1.7. Writing your own hasher
#### 3.10.2.2. Manually managing a user's password
#### 3.10.2.3. Password validation
##### 3.10.2.3.1. Enabling password validation
##### 3.10.2.3.2. Included validators
##### 3.10.2.3.3. Integrating validation
##### 3.10.2.3.4. Writing your own validator
### 3.10.3. Customizing authentication in  Django 
#### 3.10.3.1. Other authentication sources
##### 3.10.3.1.1. Specifying authentication backends
##### 3.10.3.1.2. Writing an authentication backend
##### 3.10.3.1.3. Handling authorization in custom backends
###### 3.10.3.1.3.1. Authorization for anonymous users
###### 3.10.3.1.3.2. Authorization for inactive users
###### 3.10.3.1.3.3. Handling object permissions
#### 3.10.3.2. Custom permissions
#### 3.10.3.3. Extending the existing User model
#### 3.10.3.4. Substituting a custom User model
##### 3.10.3.4.1. Using a custom user model when starting a project
##### 3.10.3.4.2. Changing to a custom user model mid-project
##### 3.10.3.4.3. Reusable apps and AUTH_USER_MODEL
##### 3.10.3.4.4. Referencing the User model
##### 3.10.3.4.5. Specifying a custom user model
##### 3.10.3.4.6. Writing a manager for a custom user model
##### 3.10.3.4.7. Extending  Django 's default User
##### 3.10.3.4.8. Custom users and the built-in auth forms
##### 3.10.3.4.9. Custom users and django.contrib.admin
##### 3.10.3.4.10. Custom users and permissions
##### 3.10.3.4.11. Custom users and proxy models
##### 3.10.3.4.12. A full example
### 3.10.4. Overview
### 3.10.5. Installation
### 3.10.6. Usage




## 3.11. 缓存
### 3.11.1. Setting up the cache
#### 3.11.1.1. Memcached
#### 3.11.1.2. Database caching
##### 3.11.1.2.1. Creating the cache table
##### 3.11.1.2.2. Multiple databases
#### 3.11.1.3. Filesystem caching
#### 3.11.1.4. Local-memory caching
#### 3.11.1.5. Dummy caching (for development)
#### 3.11.1.6. Using a custom cache backend
#### 3.11.1.7. Cache arguments
### 3.11.2. The per-site cache
### 3.11.3. The per-view cache
#### 3.11.3.1. Specifying per-view cache in the URLconf
### 3.11.4. Template fragment caching
### 3.11.5. The low-level cache API
#### 3.11.5.1. Accessing the cache
#### 3.11.5.2. Basic usage
#### 3.11.5.3. Cache key prefixing
#### 3.11.5.4. Cache versioning
#### 3.11.5.5. Cache key transformation
#### 3.11.5.6. Cache key warnings
### 3.11.6. Downstream caches
### 3.11.7. Using Vary headers
### 3.11.8. Controlling cache: Using other headers
### 3.11.9. Order of MIDDLEWARE




## 3.12. 条件性视图处理
### 3.12.1. The condition decorator
### 3.12.2. Shortcuts for only computing one value
#### 3.12.2.1. Use condition when testing both conditions
### 3.12.3. Using the decorators with other HTTP methods
### 3.12.4. Comparison with middleware conditional processing




## 3.13. 加密签名
### 3.13.1. Protecting the SECRET_KEY
### 3.13.2. Using the low-level API
#### 3.13.2.1. Using the salt argument
#### 3.13.2.2. Verifying timestamped values
#### 3.13.2.3. Protecting complex data structures




## 3.14. 发送 email
### 3.14.1. 简单的例子
### 3.14.2. send_mail()
### 3.14.3. send_mass_mail()
### 3.14.4. mail_admins()
### 3.14.5. mail_managers()
### 3.14.6. Examples
### 3.14.7. Preventing header injection
### 3.14.8. The EmailMessage class
#### 3.14.8.1. EmailMessage Objects
#### 3.14.8.2. Sending alternative content types
### 3.14.9. Email backends
#### 3.14.9.1. Obtaining an instance of an email backend
##### 3.14.9.1.1. SMTP backend
##### 3.14.9.1.2. Console backend
##### 3.14.9.1.3. File backend
##### 3.14.9.1.4. In-memory backend
##### 3.14.9.1.5. Dummy backend
#### 3.14.9.2. Defining a custom email backend
#### 3.14.9.3. Sending multiple emails
### 3.14.10. Configuring email for development




## 3.15. 国际化与本地化
### 3.15.1. Translation
#### 3.15.1.1. Overview
#### 3.15.1.2. Internationalization: in Python code
##### 3.15.1.2.1. Standard translation
##### 3.15.1.2.2. Comments for translators
##### 3.15.1.2.3. Marking strings as no-op
##### 3.15.1.2.4. Pluralization
##### 3.15.1.2.5. Contextual markers
##### 3.15.1.2.6. Lazy translation
###### 3.15.1.2.6.1. Model fields and relationships verbose_name and help_text option values
###### 3.15.1.2.6.2. Model verbose names values
###### 3.15.1.2.6.3. Model methods short_description attribute values
##### 3.15.1.2.7. Working with lazy translation objects
###### 3.15.1.2.7.1. Lazy translations and plural
###### 3.15.1.2.7.2. Formatting strings: format_lazy()
###### 3.15.1.2.7.3. Other uses of lazy in delayed translations
##### 3.15.1.2.8. Localized names of languages
#### 3.15.1.3. Internationalization: in template code
##### 3.15.1.3.1. trans template tag
##### 3.15.1.3.2. blocktrans template tag
##### 3.15.1.3.3. String literals passed to tags and filters
##### 3.15.1.3.4. Comments for translators in templates
##### 3.15.1.3.5. Switching language in templates
##### 3.15.1.3.6. Other tags
###### 3.15.1.3.6.1. get_available_languages
###### 3.15.1.3.6.2. get_current_language
###### 3.15.1.3.6.3. get_current_language_bidi
###### 3.15.1.3.6.4. i18n context processor
###### 3.15.1.3.6.5. get_language_info
###### 3.15.1.3.6.6. get_language_info_list
###### 3.15.1.3.6.7. Template filters
#### 3.15.1.4. Internationalization: in JavaScript code
##### 3.15.1.4.1. The JavaScriptCatalog view
##### 3.15.1.4.2. Using the JavaScript translation catalog
###### 3.15.1.4.2.1. gettext
###### 3.15.1.4.2.2. ngettext
###### 3.15.1.4.2.3. interpolate
###### 3.15.1.4.2.4. get_format
###### 3.15.1.4.2.5. gettext_noop
###### 3.15.1.4.2.6. pgettext
###### 3.15.1.4.2.7. npgettext
###### 3.15.1.4.2.8. pluralidx
##### 3.15.1.4.3. The JSONCatalog view
##### 3.15.1.4.4. Note on performance
#### 3.15.1.5. Internationalization: in URL patterns
##### 3.15.1.5.1. Language prefix in URL patterns
##### 3.15.1.5.2. Translating URL patterns
##### 3.15.1.5.3. Reversing in templates
#### 3.15.1.6. Localization: how to create language files
##### 3.15.1.6.1. Message files
##### 3.15.1.6.2. Compiling message files
##### 3.15.1.6.3. Troubleshooting: gettext() incorrectly detects python-format in strings with percent signs
##### 3.15.1.6.4. Creating message files from JavaScript source code
##### 3.15.1.6.5. gettext on Windows
##### 3.15.1.6.6. Customizing the makemessages command
#### 3.15.1.7. Miscellaneous
##### 3.15.1.7.1. The set_language redirect view
##### 3.15.1.7.2. Explicitly setting the active language
##### 3.15.1.7.3. Using translations outside views and templates
##### 3.15.1.7.4. Language cookie
#### 3.15.1.8. Implementation notes
##### 3.15.1.8.1. Specialties of Django translation
##### 3.15.1.8.2. How Django discovers language preference
##### 3.15.1.8.3. How Django discovers translations
##### 3.15.1.8.4. Using a non-English base language
### 3.15.2. Format localization
#### 3.15.2.1. Overview
#### 3.15.2.2. Locale aware input in forms
#### 3.15.2.3. Controlling localization in templates
##### 3.15.2.3.1. Template tags
###### 3.15.2.3.1.1. localize
##### 3.15.2.3.2. Template filters
###### 3.15.2.3.2.1. localize
###### 3.15.2.3.2.2. unlocalize
##### 3.15.2.3.3. Creating custom format files
##### 3.15.2.3.4. Limitations of the provided locale formats
###### 3.15.2.3.4.1. Switzerland (German)
### 3.15.3. Time zones
#### 3.15.3.1. Overview
#### 3.15.3.2. Concepts
##### 3.15.3.2.1. Naive and aware datetime objects
##### 3.15.3.2.2. Interpretation of naive datetime objects
##### 3.15.3.2.3. Default time zone and current time zone
##### 3.15.3.2.4. Selecting the current time zone
#### 3.15.3.3. Time zone aware input in forms
#### 3.15.3.4. Time zone aware output in templates
##### 3.15.3.4.1. Template tags
###### 3.15.3.4.1.1. localtime
###### 3.15.3.4.1.2. timezone
###### 3.15.3.4.1.3. get_current_timezone
##### 3.15.3.4.2. Template filters
###### 3.15.3.4.2.1. localtime
###### 3.15.3.4.2.2. utc
###### 3.15.3.4.2.3. timezone
#### 3.15.3.5. Migration guide
##### 3.15.3.5.1. Database
###### 3.15.3.5.1.1. PostgreSQL
###### 3.15.3.5.1.2. Other databases
##### 3.15.3.5.2. Code
##### 3.15.3.5.3. Fixtures
#### 3.15.3.6. FAQ
##### 3.15.3.6.1. Setup
##### 3.15.3.6.2. Troubleshooting
##### 3.15.3.6.3. Usage
### 3.15.4. Overview
### 3.15.5. Definitions




## 3.16. 日志

### 3.16.1. 一个简单的日志例子

Django使用Python的内置日志模块来执行系统日志记录。在Python自己的文档中详细讨论了这个模块的用法。但是，如果您从未使用过Python的日志框架(即使您使用过)，这里有一个快速入门教程。

#### 3.16.1.1. 概念一览

Python 日志配置包含四个部分：

- Loggers

- Handlers

- Filters

- Formatters

##### 3.16.1.1.1. Loggers

日志记录器是日志系统的入口点。每个日志记录器都是一个命名桶，可以将消息写入其中进行处理。

日志程序被配置为具有`log level`。此`log level`描述日志程序将处理的消息的严重性。Python定义了以下日志级别:

- DEBUG：用于调试目的的低层系统信息

- INFO：一般系统信息

- WARNING：描述发生的小问题的信息。

- ERROR：描述发生的主要问题的信息。

- CRITICAL：描述已经发生的关键问题的信息。

写入日志程序的每条消息都是一条日志记录。每个日志记录还有一个日志级别，指示特定消息的严重性。日志记录还可以包含描述所记录事件的有用元数据。

这可以包括堆栈跟踪或错误代码等细节。

当向日志程序提供一条消息时，将把消息的日志级别与日志程序的日志级别进行比较。如果消息的日志级别达到或超过日志记录器本身的日志级别，则将对消息进行进一步处理。如果没有，消息将被忽略。

一旦日志记录器确定需要处理消息，就将其传递给处理程序。

##### 3.16.1.1.2. Handlers

处理程序是引擎，它确定日志记录器中的每个消息发生了什么。它描述了特定的日志记录行为，例如向屏幕、文件或网络套接字写入消息。

与日志记录器一样，处理程序也具有日志级别。如果日志记录的日志级别不满足或超过处理程序的级别，则处理程序将忽略该消息。

一个日志程序可以有多个处理程序，每个处理程序可以有不同的日志级别。通过这种方式，可以根据消息的重要性提供不同形式的通知。例如，可以安装一个处理程序将错误和关键消息转发到分页服务，而另一个处理程序将所有消息(包括错误和关键消息)记录到一个文件中，以便稍后进行分析。

##### 3.16.1.1.3. Filters

过滤器用于提供额外的控制，以便将日志记录从记录器传递给处理程序。

默认情况下，任何满足日志级别要求的日志消息都将被处理。但是，通过安装过滤器，您可以在日志记录过程中添加额外的条件。例如，您可以安装一个过滤器，它只允许发出来自特定源的错误消息。

过滤器还可以用于在发出日志记录之前修改日志记录。例如，您可以编写一个过滤器，如果满足一组特定的条件，则将错误日志记录降级为警告记录。

过滤器可以安装在日志记录器或处理程序上;可以在一个链中使用多个过滤器来执行多个过滤操作。

##### 3.16.1.1.4. Formatters

最后，需要将日志记录呈现为文本。格式化程序描述该文本的确切格式。格式化程序通常由包含`LogRecord`属性的 Python 格式化字符串组成;不过，您也可以编写自定义格式化程序来实现特定的格式化行为。

### 3.16.2. 记录日志

一旦配置了日志记录程序、处理程序、过滤器和格式化程序，就需要将日志记录调用放入代码中。使用日志框架非常简单。这里有一个例子:

```python
import logging
logger = logging.getLogger(__name__)
def my_view(request, arg1, arg):
    ...
    if bad_mojo:
        # 记录一条日志消息
        logger.error('Something went wrong!')
```

齐活。每次激活`bad_mojo`条件时，都会写一条错误日志记录。

#### 3.16.2.1. 命名日志器

`getlogger()`的调用获得(在必要时创建)一个日志程序实例。`logger`实例由名称标识。此名称用于标识用于配置目的的日志程序。

按照惯例，日志程序名称通常为`__name__`，这是包含日志程序的Python模块的名称。这允许您根据每个模块过滤和处理日志记录调用。然而，如果你有其他的方式来组织你的日志信息，你可以提供任何点分隔的名称来识别你的日志记录器:

```python
# Get an instance of a specific named logger
logger = logging.getLogger('project.interesting.stuff')
```

记录器名称的点路径定义了层次结构。有趣的日志记录器被认为是项目的父类。项目日志记录器是项目的父类。有趣的记录器。

为什么等级制度很重要?因为`logger`可以被设置为将它们的日志调用传播给它们的父进程。

通过这种方式，您可以在日志记录器树的根上定义一组处理程序，并在日志记录器的子树中捕获所有日志记录调用。在项目名称空间中定义的日志处理程序将捕获在`project.interesting`和`project.interest.stuff`日志记录器上发出的所有日志消息。

这种传播可以在每个日志记录器的基础上进行控制。如果不希望某个特定日志记录器传播给它的父进程，可以关闭此行为。


#### 3.16.2.2. 进行日志调用

`logger`实例包含每个默认日志级别的条目方法:

- logger.debug ()

- logger.info()

- logger.warning()

- logger.error()

- logger.critical()

还有另外两个日志调用可用:

- log()：手动发出具有特定日志级别的日志消息。

- exception()：创建包含当前异常堆栈的错误级别日志消息框架。

### 3.16.3. 配置日志

当然，仅仅在代码中放入日志调用是不够的。您还需要配置日志记录器、处理程序、过滤器和格式化程序，以确保日志输出以一种有用的方式输出。

Python的日志库提供了几种配置日志的技术，从编程接口到配置文件。默认情况下，Django使用`dictConfig`格式。

为了配置日志记录，您可以使用`LOGGING`来定义日志设置字典。这些设置描述了日志设置中需要的日志记录器、处理程序、过滤器和格式化程序，以及需要这些组件具有的日志级别和其他属性。

默认情况下，`LOGGING`设置使用以下方案与Django的默认日志配置合并。

如果`LOGGING`配置中的`disable_existing_loggers`键被设置为`True`(这是默认值)，那么默认配置中的所有日志记录器都将被禁用。禁用的日志记录器与删除的并不相同，日志程序仍然存在，但将无声地丢弃记录到它的任何内容，甚至不将条目传播到父日志程序。因此您应该非常小心使用`'disable_existing_loggers': True`，这可能不是你想要的。

相反，您可以将`disable_existing_loggers`设置为`False`，并重新定义一些或所有默认的日志记录器;或者您可以将`LOGGING_CONFIG`设置为`None`并自己处理日志配置。

日志记录被配置为一般Django `setup()`函数的一部分。因此，您可以确定日志记录器始终准备好在项目代码中使用。

#### 3.16.3.1. Examples

`dictConfig`格式的完整文档是关于日志配置字典的最佳信息来源。

然而，为了让您了解什么是可能的，这里有几个例子。

首先，这里有一个简单的配置，将django日志记录器中的所有日志写到一个本地文件中:

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': '/path/to/django/debug.log',
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'DEBUG',
            'propagate': True,
        },
    },
}
```

#### 3.16.3.2. Custom logging configuration
#### 3.16.3.3. Disabling logging configuration
### 3.16.4.  Django 's logging extensions
#### 3.16.4.1. Loggers
##### 3.16.4.1.1. django
##### 3.16.4.1.2. django.request
##### 3.16.4.1.3. django.server
##### 3.16.4.1.4. django.template
##### 3.16.4.1.5. django.db.backends
##### 3.16.4.1.6. django.security.*
##### 3.16.4.1.7. django.db.backends.schema
#### 3.16.4.2. Handlers
#### 3.16.4.3. Filters
### 3.16.5.  Django 's default logging configuration




## 3.17. 分页
### 3.17.1. Example
### 3.17.2. Using Paginator in a view
### 3.17.3. Paginator objects
#### 3.17.3.1. Required arguments
#### 3.17.3.2. Optional arguments
#### 3.17.3.3. Methods
#### 3.17.3.4. Attributes
### 3.17.4. InvalidPage exceptions
### 3.17.5. Page objects
#### 3.17.5.1. Methods
#### 3.17.5.2. Attributes




## 3.18. 安全性
### 3.18.1. Cross site scripting (XSS) protection
### 3.18.2. Cross site request forgery (CSRF) protection
### 3.18.3. SQL injection protection
### 3.18.4. Clickjacking protection
### 3.18.5. SSL/HTTPS
### 3.18.6. Host header validation
### 3.18.7. Session security
### 3.18.8. User-uploaded content
### 3.18.9. Additional security topics




## 3.19. 性能与优化
### 3.19.1. Introduction
### 3.19.2. General approaches
#### 3.19.2.1. What are you optimizing for?
#### 3.19.2.2. Performance benchmarking
##### 3.19.2.2.1. Django tools
##### 3.19.2.2.2. Third-party services
#### 3.19.2.3. Get things right from the start
##### 3.19.2.3.1. Work at the appropriate level
### 3.19.3. Caching
#### 3.19.3.1. The caching framework
#### 3.19.3.2. cached_property
### 3.19.4. Understanding laziness
#### 3.19.4.1. Laziness in  Django 
### 3.19.5. Databases
#### 3.19.5.1. Database optimization
#### 3.19.5.2. Other database-related tips
### 3.19.6. HTTP performance
#### 3.19.6.1. Middleware
##### 3.19.6.1.1. ConditionalGetMiddleware
##### 3.19.6.1.2. GZipMiddleware
#### 3.19.6.2. Sessions
##### 3.19.6.2.1. Using cached sessions
#### 3.19.6.3. Static files
##### 3.19.6.3.1. CachedStaticFilesStorage
##### 3.19.6.3.2. 'Minification'
### 3.19.7. Template performance
#### 3.19.7.1. The cached template loader
### 3.19.8. Using different versions of available software
#### 3.19.8.1. Newer is often - but not always - better
#### 3.19.8.2. Alternatives to  Django 's template language
#### 3.19.8.3. Alternative software implementations
##### 3.19.8.3.1. PyPy
##### 3.19.8.3.2. C implementations of Python libraries




## 3.20. 序列化
### 3.20.1. Serializing data
#### 3.20.1.1. Subset of fields
#### 3.20.1.2. Inherited models
### 3.20.2. Deserializing data
### 3.20.3. Serialization formats
#### 3.20.3.1. XML
#### 3.20.3.2. JSON
##### 3.20.3.2.1.  Django JSONEncoder
#### 3.20.3.3. YAML
### 3.20.4. Natural keys
#### 3.20.4.1. Deserialization of natural keys
#### 3.20.4.2. Serialization of natural keys
#### 3.20.4.3. Dependencies during serialization




## 3.21. 设置
### 3.21.1. The basics
### 3.21.2. Designating the settings
#### 3.21.2.1. The django-admin utility
#### 3.21.2.2. On the server (mod_wsgi)
### 3.21.3. Default settings
#### 3.21.3.1. Seeing which settings you've changed
### 3.21.4. Using settings in Python code
### 3.21.5. Altering settings at runtime
### 3.21.6. Security
### 3.21.7. Available settings
### 3.21.8. Creating your own settings
### 3.21.9. Using settings without setting DJANGO_SETTINGS_MODULE
#### 3.21.9.1. Custom default settings
#### 3.21.9.2. Either configure() or DJANGO_SETTINGS_MODULE is required
#### 3.21.9.3. Calling django.setup() is required for 'standalone' Django usage




## 3.22. 信号
### 3.22.1. Listening to signals
#### 3.22.1.1. Receiver functions
#### 3.22.1.2. Connecting receiver functions
#### 3.22.1.3. Connecting to signals sent by specific senders
#### 3.22.1.4. Preventing duplicate signals
#### 3.22.1.5. Preventing duplicate signals
### 3.22.2. Defining and sending signals
#### 3.22.2.1. Defining signals
#### 3.22.2.2. Sending signals
### 3.22.3. Disconnecting signals




## 3.23. 系统校验
### 3.23.1. Writing your own checks
#### 3.23.1.1. Messages
#### 3.23.1.2. Registering and labeling checks
#### 3.23.1.3. Field, model, manager, and database checks
#### 3.23.1.4. Writing tests




## 3.24. 外部包
### 3.24.1. Localflavor
### 3.24.2. Comments
### 3.24.3. Formtools
