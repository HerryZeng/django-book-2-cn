## 自定义ModelAdmin类

让我们更深一步：自定义`Author`模块的列表中的显示字段。 列表默认地显示查询结果中对象的`__unicode__()`。 在第五章中，我们定义`Author`对象的__unicode__()方法，用以同时显示作者的姓和名。 
```python
    class Author(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=40)
        email = models.EmailField(blank=True, verbose_name='e-mail')
    
        def __unicode__(self):
            return u'%s %s' % (self.first_name, self.last_name)

```
列表中显示的是每个作者的姓名。

我们可以在这基础上改进，添加其它字段，从而改变列表的显示。 这个页面应该提供便利，比如说：在这个列表中可以看到作者的邮箱地址。如果能按照姓氏或名字来排序，那就更好了。 

为了达到这个目的，我们将为`Author`模块定义一个`ModelAdmin`类。 这个类是自定义管理工具的关键，其中最基本的一件事情是允许你指定列表中的字段。 打开`admin.py`并修改： 
```python
    from django.contrib import admin
    from mysite.books.models import Publisher, Author, Book
    
    class AuthorAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'email')
    
    admin.site.register(Publisher)
    admin.site.register(Author, AuthorAdmin)
    admin.site.register(Book)
```