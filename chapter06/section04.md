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

花点时间添加和修改记录，以填充数据库。 如果你跟着第五章的例子一起创建`Publisher`对象的话（并且没有删除），你会在列表中看到那些记录。 
这里需要提到的一个特性是，管理工具处理外键和多对多关系（这两种关系可以在`Book`模块中找到）的方法。 作为提醒，这里有个`Book` 模块的例子： 
```python
    class Book(models.Model):
    title = models.CharField(max_length=100)
    authors = models.ManyToManyField(Author)
    publisher = models.ForeignKey(Publisher)
    publication_date = models.DateField()

    def __unicode__(self):
        return self.title
```
在`Add book`页面中（`http://127.0.0.1:8000/admin/books/book/add/`），`外键``publisher`用一个选择框显示，`多对多`字段`author`用一个多选框显示。 点击两个字段后面的绿色加号，可以让你添加相关的记录。举个例子，如果你点击`Publisher`后面的加号，你将会得到一个弹出窗口来添加一个`publisher`。 当你在那个窗口中成功创建了一个`publisher`后，`Add book`表单会自动把它更新到字段上去。