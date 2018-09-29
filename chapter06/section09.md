## 自定义编辑表单

正如自定义列表那样，编辑表单多方面也能自定义。 

### 自定义字段顺序

首先，我们先自定义字段顺序。 默认地，表单中的字段顺序是与模块中定义是一致的。我们可以通过使用ModelAdmin子类中的fields选项来改变它： 
```python
    class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'publisher', 'publication_date')
    list_filter = ('publication_date',)
    date_hierarchy = 'publication_date'
    ordering = ('-publication_date',)
    fields = ('title', 'authors', 'publisher', 'publication_date')
```
完成之后，编辑表单将按照指定的顺序显示各字段。 它看起来自然多了——作者排在书名之后。 字段顺序当然是与数据条目录入顺序有关， 每个表单都不一样。 

通过`fields`这个选项，你可以排除一些不想被其他人编辑的fields 只要不选上不想被编辑的field(s)即可。 当你的admin用户只是被信任可以更改你的某一部分数据时，或者，你的数据被一些外部的程序自动处理而改变了了，你就可以用这个功能。 例如，在book数据库中，我们可以隐藏publication_date，以防止它被编辑。
```python
    class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'publisher', 'publication_date')
    list_filter = ('publication_date',)
    date_hierarchy = 'publication_date'
    ordering = ('-publication_date',)
    fields = ('title', 'authors', 'publisher')
```

这样，在编辑页面就无法对`publication date`进行改动。 如果你是一个编辑，不希望作者推迟出版日期的话，这个功能就很有用。 （当然，这纯粹是一个假设的例子。）