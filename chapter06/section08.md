## 自定义ModelAdmin类

### 自定义显示字段名
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
解释一下代码： 我们新建了一个类`AuthorAdmin`，它是从`django.contrib.admin.ModelAdmin`派生出来的子类，保存着一个类的自定义配置，以供管理工具使用。 我们只自定义了一项：`list_display`， 它是一个字段名称的元组，用于列表显示。 当然，这些字段名称必须是模块中有的。我们修改了`admin.site.register()`调用，在`Author`后面添加了`AuthorAdmin`。你可以这样理解： 用`AuthorAdmin`选项注册`Author`模块。 

`admin.site.register()`函数接受一个`ModelAdmin`子类作为第二个参数。 如果你忽略第二个参数，`Django`将使用默认的选项。`Publisher`和`Book`的注册就属于这种情况。 弄好了这个东东，再刷新author列表页面，你会看到列表中有三列：姓氏、名字和邮箱地址。 另外，点击每个列的列头可以对那列进行排序。

接下来，让我们添加一个快速查询栏。 向`AuthorAdmin`追加`search_fields`，如：
```python
    class AuthorAdmin(admin.ModelAdmin):
        list_display = ('first_name', 'last_name', 'email')
        search_fields = ('first_name', 'last_name')
```
刷新浏览器，你会在页面顶端看到一个查询栏。我们刚才所作的修改列表页面，添加了一个根据姓名查询的查询框。正如用户所希望的那样，它是大小写敏感，并且对两个字段检索的查询框。如果查询"`bar`"，那么名字中含有Barney和姓氏中含有Hobarson的作者记录将被检索出来。 

接下来，让我们为Book列表页添加一些过滤器。
