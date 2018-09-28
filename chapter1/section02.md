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
+ `views.py`:文件包含 页面的业务逻辑。latest_books()函数叫（_视图_）