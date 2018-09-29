## 将你的Model加入到Admin管理中

有一个关键步骤我们还没做。 让我们将自己的模块加入管理工具中，这样我们就能够通过这个漂亮的界面添加、修改和删除数据库中的对象了。 我们将继续第五章中的`book` 例子。在其中，我们定义了三个模块： `Publisher`、 `Author`和 `Book`。 

在`books` 目录下(`mysite/books`)，创建一个文件：`admin.py` ，然后输入以下代码：
```python
    from django.contrib import admin
    from mysite.books.models import Publisher, Author, Book
    
    admin.site.register(Publisher)
    admin.site.register(Author)
    admin.site.register(Book)
```
这些代码通知管理工具为这些模块逐一提供界面。

完成后，打开页面 `http://127.0.0.1:8000/admin/` ，你会看到一个`Books`区域，其中包含`Authors`、`Books`和`Publishers`。 （你可能需要先停止，然后再启动服务(`python manage.py runserver`)，才能使其生效。） 

现在你拥有一个功能完整的管理界面来管理这三个模块了。很简单吧！
