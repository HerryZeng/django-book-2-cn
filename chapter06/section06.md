## 设置字段可选

在摆弄了一会之后，你或许会发现管理工具有个限制：编辑表单需要你填写每一个字段，然而在有些情况下，你想要某些字段是可选的。 

举个例子，我们想要`Author`模块中的`email`字段成为可选，即允许不填。 在现实世界中，你可能没有为每个作者登记邮箱地址。 
为了指定`email`字段为可选，你只要编辑`Book`模块（回想第五章，它在`mysite/books/models.py`文件里），在`email`字段上加上`blank=True`。代码如下： 
```python
    class Author(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=40)
    email = models.EmailField(blank=True)
```
这些代码告诉`Django`，作者的邮箱地址允许输入一个空值。 所有字段都默认`blank=False`，这使得它们不允许输入空值。

这里会发生一些有趣的事情。 直到现在，除了`__unicode__()`方法，我们的模块充当数据库中表定义的角色，即本质上是用`Python`的语法来写`CREATE TABLE`语句。 在添加`blank=True`过程中，我们已经开始在简单的定义数据表上扩展我们的模块了。 
现在，我们的模块类开始成为一个富含`Author`对象属性和行为的集合了。 email不但展现为一个数据库中的VARCHAR类型的字段，它还是页面中可选的字段，就像在管理工具中看到的那样。 当你添加`blank=True`以后，刷新页面`Add author edit form` [http://127.0.0.1:8000/admin/books/author/add/](http://127.0.0.1:8000/admin/books/author/add/)，将会发现Email的标签不再是粗体了。 这意味它不是一个必填字段。 现在你可以添加一个作者而不必输入邮箱地址，即使你为这个字段提交了一个空值，也再不会得到那刺眼的红色信息“This field is required”。