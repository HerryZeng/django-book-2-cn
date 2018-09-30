## 如何使用模板系统

让我们深入研究模板系统，你将会明白它是如何工作的。但我们暂不打算将它与先前创建的视图结合在一起，因为我们现在的目的是了解它是如何独立工作的。 。 （换言之， 通常你会将模板和视图一起使用，但是我们只是想突出模板系统是一个Python库，你可以在任何地方使用它，而不仅仅是在Django视图中。） 

在Python代码中使用Django模板的最基本方式如下： 
1. 可以用原始的模板代码字符串创建一个 Template 对象， Django同样支持用指定模板文件路径的方式来创建 Template 对象; 
2. 调用模板对象的render方法，并且传入一套变量context。它将返回一个基于模板的展现字符串，模板中的变量和标签会被context值替换。
```python
    >>> from django import template
    >>> t = template.Template('My name is {{ name }}.')
    >>> c = template.Context({'name': 'Adrian'})
    >>> print t.render(c)
    My name is Adrian.
    >>> c = template.Context({'name': 'Fred'})
    >>> print t.render(c)
    My name is Fred.
```
以下部分逐步的详细介绍

### 创建模板对象

创建一个 Template 对象最简单的方法就是直接实例化它。 Template 类就在 django.template 模块中，构造函数接受一个参数，原始模板代码。 让我们深入挖掘一下 Python的解释器看看它是怎么工作的。 

转到project目录（在第二章由 django-admin.py startproject 命令创建）， 输入命令 python manage.py shell 启动交互界面。 

一个特殊的Python提示符 

如果你曾经使用过Python，你一定好奇，为什么我们运行python manage.py shell而不是python。这两个命令都会启动交互解释器，但是manage.py shell命令有一个重要的不同： 

在启动解释器之前，它告诉Django使用哪个设置文件。 Django框架的大部分子系统，包括模板系统，都依赖于配置文件；如果Django不知道使用哪个配置文件，这些系统将不能工作。 
如果你想知道，这里将向你解释它背后是如何工作的。 Django搜索DJANGO_SETTINGS_MODULE环境变量，它被设置在settings.py中。例如，假设mysite在你的Python搜索路径中，那么DJANGO_SETTINGS_MODULE应该被设置为：’mysite.settings’。

当你运行命令：python manage.py shell，它将自动帮你处理DJANGO_SETTINGS_MODULE。 在当前的这些示例中，我们鼓励你使用`python manage.py shell`这个方法，这样可以免去你大费周章地去配置那些你不熟悉的环境变量。 
随着你越来越熟悉Django，你可能会偏向于废弃使用`manage.py shell`，而是在你的配置文件.bash_profile中手动添加 DJANGO_SETTINGS_MODULE这个环境变量。

让我们来了解一些模板系统的基本知识：
```python
    >>> from django.template import Template
    >>> t = Template('My name is {{ name }}.')
    >>> print t
```
如果你跟我们一起做，你将会看到下面的内容：
```python
    <django.template.Template object at 0xb7d5f24c>
```
0xb7d5f24c 每次都会不一样，这没什么关系；这只是Python运行时 Template 对象的ID。 
当你创建一个 Template 对象，模板系统在内部编译这个模板到内部格式，并做优化，做好 渲染的准备。 如果你的模板语法有错误，那么在调用 Template() 时就会抛出 TemplateSyntaxError 异常：
```python
    >>> from django.template import Template
    >>> t = Template('{% notatag %}')
    Traceback (most recent call last):
    File "<stdin>", line 1, in ?
    ...
    django.template.TemplateSyntaxError: Invalid block tag: 'notatag'
```
这里，块标签(block tag)指向的是`` {% notatag %}``，块标签与模板标签是同义的。
系统会在下面的情形抛出 TemplateSyntaxError 异常：
    + 无效的tags 
    + 标签的参数无效
    + 无效的过滤器
    + 过滤器的参数无效
    - 无效的模板语法
    * 未封闭的块标签 （针对需要封闭的块标签）
    
### 模板渲染 

一旦你创建一个 Template 对象，你可以用 context 来传递数据给它。 一个context是一系列变量和它们值的集合。
 
context在Django里表现为 Context 类，在 django.template 模块里。 她的构造函数带有一个可选的参数： 一个字典映射变量和它们的值。 调用 Template 对象 的 render() 方法并传递context来填充模板：
```python
    >>> from django.template import Context, Template
    >>> t = Template('My name is {{ name }}.')
    >>> c = Context({'name': 'Stephane'})
    >>> t.render(c)
    u'My name is Stephane.'
```
我们必须指出的一点是，t.render(c)返回的值是一个Unicode对象，不是普通的Python字符串。 你可以通过字符串前的u来区分。 在框架中，Django会一直使用Unicode对象而不是普通的字符串。 如果你明白这样做给你带来了多大便利的话，尽可能地感激Django在幕后有条不紊地为你所做这这么多工作吧。 如果不明白你从中获益了什么，别担心。你只需要知道Django对Unicode的支持，将让你的应用程序轻松地处理各式各样的字符集，而不仅仅是基本的A-Z英文字符。

字典和Contexts

Python的字典数据类型就是关键字和它们值的一个映射。 Context 和字典很类似， Context 还提供更多的功能，请看第九章。

变量名必须由英文字符开始 （A-Z或a-z）并可以包含数字字符、下划线和小数点。 （小数点在这里有特别的用途，稍后我们会讲到）变量是大小写敏感的。 
下面是编写模板并渲染的示例：
```python
    >>> from django.template import Template, Context
    >>> raw_template = """<p>Dear {{ person_name }},</p>
    ...
    ... <p>Thanks for placing an order from {{ company }}. It's scheduled to
    ... ship on {{ ship_date|date:"F j, Y" }}.</p>
    ...
    ... {% if ordered_warranty %}
    ... <p>Your warranty information will be included in the packaging.</p>
    ... {% else %}
    ... <p>You didn't order a warranty, so you're on your own when
    ... the products inevitably stop working.</p>
    ... {% endif %}
    ...
    ... <p>Sincerely,<br />{{ company }}</p>"""
    >>> t = Template(raw_template)
    >>> import datetime
    >>> c = Context({'person_name': 'John Smith',
    ...     'company': 'Outdoor Equipment',
    ...     'ship_date': datetime.date(2009, 4, 2),
    ...     'ordered_warranty': False})
    >>> t.render(c)
    u"<p>Dear John Smith,</p>\n\n<p>Thanks for placing an order from Outdoor
    Equipment. It's scheduled to\nship on April 2, 2009.</p>\n\n\n<p>You
    didn't order a warranty, so you're on your own when\nthe products
    inevitably stop working.</p>\n\n\n<p>Sincerely,<br />Outdoor Equipment
    </p>"
```
让我们逐步来分析下这段代码： 

首先我们导入 （import）类 Template 和 Context ，它们都在模块 django.template 里。 
我们把模板原始文本保存到变量 raw_template 。注意到我们使用了三个引号来 标识这些文本，因为这样可以包含多行。 

接下来，我们创建了一个模板对象 t ，把 raw_template 作为 Template 类构造函数的参数。 
我们从Python的标准库导入 datetime 模块，以后我们将会使用它。 

然后，我们创建一个 Context 对象， c 。 Context 构造的参数是Python 字典数据类型。 在这里，我们指定参数 person_name 的值是 'John Smith' , 参数company 的值为 ‘Outdoor Equipment’ ，等等。 

最后，我们在模板对象上调用 render() 方法，传递 context参数给它。 这是返回渲染后的模板的方法，它会替换模板变量为真实的值和执行块标签。 

注意，warranty paragraph显示是因为 ordered_warranty 的值为 True . 注意时间的显示， April 2, 2009 , 它是按 'F j, Y' 格式显示的。 
如果你是Python初学者，你可能在想为什么输出里有回车换行的字符('\n' )而不是 显示回车换行？ 因为这是Python交互解释器的缘故： 调用 t.render(c) 返回字符串， 解释器缺省显示这些字符串的 真实内容呈现 ，而不是打印这个变量的值。 要显示换行而不是 '\n' ，使用 print 语句： print t.render(c) 。 
这就是使用Django模板系统的基本规则： **写模板，创建 Template 对象，创建 Context ， 调用 render() 方法**。 

### 同一模板，多个上下文

一旦有了`模板`对象，你就可以通过它渲染多个context， 例如：
```python
    >>> from django.template import Template, Context
    >>> t = Template('Hello, {{ name }}')
    >>> print t.render(Context({'name': 'John'}))
    Hello, John
    >>> print t.render(Context({'name': 'Julie'}))
    Hello, Julie
    >>> print t.render(Context({'name': 'Pat'}))
    Hello, Pat
```
无论何时我们都可以像这样使用同一模板源渲染多个context，只进行 一次模板创建然后多次调用render()方法渲染会更为高效： 
```python
    # Bad
    for name in ('John', 'Julie', 'Pat'):
        t = Template('Hello, {{ name }}')
        print t.render(Context({'name': name}))
    
    # Good
    t = Template('Hello, {{ name }}')
    for name in ('John', 'Julie', 'Pat'):
        print t.render(Context({'name': name}))
```
Django 模板解析非常快捷。 大部分的解析工作都是在后台通过对简短正则表达式一次性调用来完成。 这和基于 XML 的模板引擎形成鲜明对比，那些引擎承担了 XML 解析器的开销，且往往比 Django 模板渲染引擎要慢上几个数量级。 

### 深度变量的查找

在到目前为止的例子中，我们通过 context 传递的简单参数值主要是字符串，还有一个 datetime.date 范例。 然而，模板系统能够非常简洁地处理更加复杂的数据结构，例如list、dictionary和自定义的对象。

在 Django 模板中遍历复杂数据结构的关键是句点字符 (.)。 
最好是用几个例子来说明一下。 比如，假设你要向模板传递一个 Python 字典。 要通过字典键访问该字典的值，可使用一个句点：
```python
    >>> from django.template import Template, Context
    >>> person = {'name': 'Sally', 'age': '43'}
    >>> t = Template('{{ person.name }} is {{ person.age }} years old.')
    >>> c = Context({'person': person})
    >>> t.render(c)
    u'Sally is 43 years old.'
```
同样，也可以通过句点来访问对象的属性。 比方说， Python 的 datetime.date 对象有 year 、 month 和 day 几个属性，你同样可以在模板中使用句点来访问这些属性：
```python
    >>> from django.template import Template, Context
    >>> import datetime
    >>> d = datetime.date(1993, 5, 2)
    >>> d.year
    1993
    >>> d.month
    5
    >>> d.day
    2
    >>> t = Template('The month is {{ date.month }} and the year is {{ date.year }}.')
    >>> c = Context({'date': d})
    >>> t.render(c)
    u'The month is 5 and the year is 1993.'
```
这个例子使用了一个自定义的类，演示了通过实例变量加一点(dots)来访问它的属性，这个方法适用于任意的对象。
```python
    >>> from django.template import Template, Context
    >>> class Person(object):
    ...     def __init__(self, first_name, last_name):
    ...         self.first_name, self.last_name = first_name, last_name
    >>> t = Template('Hello, {{ person.first_name }} {{ person.last_name }}.')
    >>> c = Context({'person': Person('John', 'Smith')})
    >>> t.render(c)
    u'Hello, John Smith.'
```
点语法也可以用来引用对象的**方法**。 例如，每个 Python 字符串都有 upper() 和 isdigit() 方法，你在模板中可以使用同样的句点语法来调用它们：
```python
    >>> from django.template import Template, Context
    >>> t = Template('{{ var }} -- {{ var.upper }} -- {{ var.isdigit }}')
    >>> t.render(Context({'var': 'hello'}))
    u'hello -- HELLO -- False'
    >>> t.render(Context({'var': '123'}))
    u'123 -- 123 -- True'
```
注意这里调用方法时并**没有**使用圆括号 而且也无法给该方法传递参数；你只能调用不需参数的方法。 （我们将在本章稍后部分解释该设计观。） 
最后，句点也可用于访问列表索引，例如：

最后，句点也可用于访问列表索引，例如：
```python
    >>> from django.template import Template, Context
    >>> t = Template('Item 2 is {{ items.2 }}.')
    >>> c = Context({'items': ['apples', 'bananas', 'carrots']})
    >>> t.render(c)
    u'Item 2 is carrots.'
```
不允许使用负数列表索引。 像 {{ items.-1 }} 这样的模板变量将会引发`TemplateSyntaxError`。


