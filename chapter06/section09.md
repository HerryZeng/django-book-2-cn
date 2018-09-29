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

当一个用户用这个不包含完整信息的表单添加一本新书时，Django会简单地将`publication_date`设置为`None`，以确保这个字段满足`null=True`的条件。

### 自定义多对多字段

另一个常用的编辑页面自定义是针对多对多字段的。 真如我们在`book`编辑页面看到的那样，`多对多字段`被展现成多选框。虽然多选框在逻辑上是最适合的HTML控件，但它却不那么好用。 如果你想选择多项，你必须还要按下Ctrl键（苹果机是command键）。 虽然管理工具因此添加了注释（help_text），但是当它有几百个选项时，它依然显得笨拙。
 
更好的办法是使用`filter_horizontal`。让我们把它添加到`BookAdmin`中，然后看看它的效果。 
```python
    class BookAdmin(admin.ModelAdmin):
        list_display = ('title', 'publisher', 'publication_date')
        list_filter = ('publication_date',)
        date_hierarchy = 'publication_date'
        ordering = ('-publication_date',)
        filter_horizontal = ('authors',)
```
（如果你一着跟着做练习，请注意移除fields选项，以使得编辑页面包含所有字段。） 

刷新book编辑页面，你会看到Author区中有一个精巧的JavaScript过滤器，它允许你检索选项，然后将选中的authors从Available框移到Chosen框，还可以移回来。

我们强烈建议针对那些拥有十个以上选项的`多对多字段`使用`filter_horizontal`。 这比多选框好用多了。 你可以在多个字段上使用filter_horizontal，只需在这个元组中指定每个字段的名字。 
`ModelAdmin`类还支持`filter_vertical`选项。 它像`filter_horizontal`那样工作，除了控件都是垂直排列，而不是水平排列的。 至于使用哪个，只是个人喜好问题。 

`filter_horizontal`和`filter_vertical`选项只能用在多对多字段上, 而不能用于 `ForeignKey`字段。 默认地，管理工具使用`下拉框`来展现`外键`字段。但是，正如`多对多字段`那样，有时候你不想忍受因装载并显示这些选项而产生的大量开销。 例如，我们的book数据库膨胀到拥有数千条publishers的记录，以致于book的添加页面装载时间较久，因为它必须把每一个publishe都装载并显示在`下拉框`中。 

解决这个问题的办法是使用`raw_id_fields`选项。它是一个包含外键字段名称的元组，它包含的字段将被展现成`文本框`，而不再是`下拉框`。
```python
    class BookAdmin(admin.ModelAdmin):
    list_display = ('title', 'publisher', 'publication_date')
    list_filter = ('publication_date',)
    date_hierarchy = 'publication_date'
    ordering = ('-publication_date',)
    filter_horizontal = ('authors',)
    raw_id_fields = ('publisher',)
```

