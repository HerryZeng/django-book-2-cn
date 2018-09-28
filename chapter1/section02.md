让我们来研究一个简单的例子，通过该实例，你可以分辨出，通过`Web`框架来实现的功能与之前的方式有何不同。下面就是通过使用`Django`来写成以上功能的例子：首先，我们分成4个`Python`文件（`models.py`、`views.py`、`urls.py`）和`html`模板文件（latest_books.html）。
```python
# models.py (the database tables)

from django.db import models

class Book(models.Model):
    name = models.CharField(max_length=50)
    pub_date = models.DateField()


# views.py (the business logic)

from django.shortcuts import render_to_response
from models import Book

def latest_books(request):
    book_list = Book.objects.order_by('-pub_date')[:10]
    return render_to_response('latest_books.html', {'book_list': book_list})


# urls.py (the URL configuration)

from django.conf.urls.defaults import *
import views

urlpatterns = patterns('',
    (r'^latest/$', views.latest_books),
)
    
# latest_books.html (the template)

<html><head><title>Books</title></head>
<body>
<h1>Books</h1>
<ul>
{% for book in book_list %}
<li>{{ book.name }}</li>
{% endfor %}
</ul>
</body></html>

```
然后，不用关心语法细节；只要用心感觉整体的设计。这里只关注分割后的几个文件：
- `models.py`:文件主要用一个`Python`类来描述数据表。称为_模型（model）_。运用这个类，你可能通过简单的`Python`的代码来创建、查询、更新、删除数据库中的记录而不需写一条`SQL`语句。
+ `views.py`:文件包含 页面的业务逻辑。latest_books()函数叫视图
* `urls.py`: 指出了什么样的`URL`调用什么样的视图。在这个例子中`/latest/`将会调用`latest_books()`这个函数。换句话说，如果你的域名是`example.com`，任何人浏览网址`http://example.com/latest/`将会调用`latest_books()`这个函数。
- `latest_books`:是`html`模板，它描述了这个页面的设计是如何的。使用带基本逻辑声明的模板语言，如`{% for book in book_list %}`
结合起来，这些部分松散遵循的模式称为模型-视图-控制器（**MVC**）。简单的说，**MVC**是一种软件开发的方法，它把代码的定义和数据访问的方法（模型）与请求逻辑（控制器）还有用户接口（视图）分开来。我们将在第五章更深入地讨论**MVC**。

这种设计模式关键的优势在于各种组件都是 松散结合 的。这样，每个由 `Django`驱动 的`Web`应用都有着明确的目的，并且可独立更改而不影响到其它的部分。比如，开发者 更改一个应用程序中的 `URL`而不用影响到这个程序底层的实现。 设计师可以改变 `HTML` 页面 的样式而不用接触 `Python`代码。 数据库管理员可以重新命名数据表并且只需更改一个地方，无需从一大堆文件中进行查找和替换。

本书中，每个组件都有它自己的一个章节。 比如，第三章涵盖了视图，第四章是模板， 而第五章是模型。