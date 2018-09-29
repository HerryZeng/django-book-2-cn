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


### 设置日期型和数字型字段可选

虽然`blank=True`同样适用于日期型和数字型字段，但是这里需要详细讲解一些背景知识。 `SQL`有指定空值的独特方式，它把空值叫做`NULL`。`NULL`可以表示为未知的、非法的、或其它程序指定的含义。 在`SQL`中， `NULL`的值不同于空字符串，就像`Python`中`None`不同于空字符串（""）一样。这意味着某个字符型字段（如`VARCHAR`）的值不可能同时包含`NULL`和空字符串。

这会引起不必要的歧义或疑惑。 为什么这条记录有个`NULL`，而那条记录却有个空字符串？ 它们之间有区别，还是数据输入不一致？ 还有： 我怎样才能得到全部拥有空值的记录，应该按NULL和空字符串查找么？还是仅按字符串查找？ 为了消除歧义，`Django`生成`CREATE TABLE`语句**自动**为每个字段显式加上`NOT NULL`。 这里有个第五章中生成Author模块的例子：
```sql
    CREATE TABLE "books_author" (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(40) NOT NULL,
    "email" varchar(75) NOT NULL
    );
```
在大多数情况下，这种默认的行为对你的应用程序来说是最佳的，因为它可以使你不再因数据一致性而头痛。 而且它可以和`Django`的其它部分工作得很好。如在管理工具中，如果你留空一个字符型字段，它会为此插入一个空字符串（而**不是**`NULL`）。



