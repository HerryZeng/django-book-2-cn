## 自定义字段标签

在编辑页面中，每个字段的标签都是从模块的字段名称生成的。 规则很简单： **用空格替换下划线；首字母大写**。例如：`Book`模块中publication_date的标签是`Publication date`。 然而，字段名称并不总是贴切的。有些情况下，你可能想自定义一个标签。 你只需在模块中指定`verbose_name`。

  
举个例子，说明如何将Author.email的标签改为e-mail，中间有个横线。 
```python
    class Author(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=40)
        email = models.EmailField(blank=True, verbose_name='e-mail')
```
修改后重启服务器，你会在author编辑页面中看到这个新标签。

请注意，你不必把`verbose_name`的首字母大写，除非是连续大写（如："USA state"）。`Django`会自动适时将首字母大写，并且在其它不需要大写的地方使用`verbose_name`的精确值。 最后还需注意的是，为了使语法简洁，你可以把它当作固定位置的参数传递。 这个例子与上面那个的效果相同。 
```python
    class Author(models.Model):
        first_name = models.CharField(max_length=30)
        last_name = models.CharField(max_length=40)
        email = models.EmailField('e-mail',blank=True)
```
但这不适用于`ManyToManyField `和`ForeignKey`字段，因为它们第一个参数必须是模块类。 那种情形，必须显式使用`verbose_name`这个参数名称。
