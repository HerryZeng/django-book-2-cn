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