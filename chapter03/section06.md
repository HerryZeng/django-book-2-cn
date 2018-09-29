## 第二个视图： 动态内容

我们的Hello world视图是用来演示基本的Django是如何工作的，但是它不是一个动态网页的例子，因为网页的内容一直是一样的. 每次去查看/hello/，你将会看到相同的内容，它类似一个静态HTML文件。 

我们的第二个视图，将更多的放些动态的东西例如当前日期和时间显示在网页上 这将非常好，简单的下一步，因为它不引入了数据库或者任何用户的输入，仅仅是输出显示你的服务器的内部时钟. 它仅仅有限度的比Helloworld刺激一些，但是它将演示一些新的概念 
```python
    >>> import datetime
    >>> now = datetime.datetime.now()
    >>> now
    datetime.datetime(2008, 12, 13, 14, 9, 39, 2731)
    >>> print now
    2008-12-13 14:09:39.002731
```
这个视图需要做两件事情： 计算当前日期和时间，并返回包含这些值的HttpResponse 如果你对python很有经验，那肯定知道在python中需要利用datetime模块去计算时间 下面演示如何去使用它：

以上代码很简单，并没有涉及Django。 它仅仅是Python代码。需要强调的是，你应该意识到哪些是纯Python代码，哪些是Django特性代码。 （见上） 因为你学习了Django，希望你能将Django的知识应用在那些不一定需要使用Django的项目上。 
为了让Django视图显示当前日期和时间，我们仅需要把语句：datetime.datetime.now()放入视图函数，然后返回一个HttpResponse对象即可。代码如下：
```python
    from django.http import HttpResponse
    import datetime
    
    def current_datetime(request):
        now = datetime.datetime.now()
        html = "<html><body>It is now %s.</body></html>" % now
        return HttpResponse(html)
```

让我们分析一下改动后的views.py： 
在文件顶端，我们添加了一条语句：import datetime。这样就可以计算日期了。 
函数中的第一行代码计算当前日期和时间，并以 datetime.datetime 对象的形式保存为局部变量 now 。 
函数的第二行代码用 Python 的格式化字符串（format-string）功能构造了一段 HTML 响应。 字符串中的%s是占位符，字符串后面的百分号表示用它后面的变量now的值来代替%s。变量%s是一个datetime.datetime对象。它虽然不是一个字符串，但是%s（格式化字符串）会把它转换成字符串，如：2008-12-13 14:09:39.002731。这将导致HTML的输出字符串为：It is now 2008-12-13 14:09:39.002731。 （目前HTML是有错误的，但我们这样做是为了保持例子的简短。） 

最后，正如我们刚才写的hello函数一样，视图返回一个HttpResponse对象，它包含生成的响应。

添加上述代码之后，还要在urls.py中添加URL模式，以告诉Django由哪一个URL来处理这个视图。 用/time/之类的字眼易于理解：
```python
    from django.contrib import admin
    from django.urls import path
    from mysite.views import hello,current_datetime
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('hello/',hello),
        path('time/',current_datetime),
    ]
```
这里，我们修改了两个地方。 首先，在顶部导入current_datetime函数； 其次，也是比较重要的：添加URL模式来映射URL中的/time/和新视图。 理解了么？ 
写好视图并且更新URLconf之后，运行命令python manage.py runserver以启动服务，在浏览器中输入http://127.0.0.1:8000/time/。 你将看到当前的日期和时间。
