## 第三个视图 动态URL 

在我们的`current_datetime`视图范例中，尽管内容是动态的，但是URL （ /time/ ）是静态的。 在 大多数动态web应用程序，URL通常都包含有相关的参数。 举个例子，一家在线书店会为每一本书提供一个URL，如：/books/243/、/books/81196/。 

让我们创建第三个视图来显示当前时间和加上时间偏差量的时间，设计是这样的： /time/plus/1/ 显示当前时间＋1个小时的页面 /time/plus/2/ 显示当前时间＋2个小时的页面 /time/plus/3/ 显示当前时间＋3个小时的页面，以此类推。 
新手可能会考虑写不同的视图函数来处理每个时间偏差量，URL配置看起来就象这样：
```python
    urlpatterns = [
        path('time/', current_datetime),
        path('time/plus/1/', one_hour_ahead),
        path('time/plus/2/', two_hours_ahead),
        path('time/plus/3/', three_hours_ahead),
        path('time/plus/4/', four_hours_ahead),
    ]
```
很明显，这样处理是不太妥当的。 不但有很多冗余的视图函数，而且整个应用也被限制了只支持 预先定义好的时间段，2小时，3小时，或者4小时。 如果哪天我们要实现 5 小时，我们就 不得不再单独创建新的视图函数和配置URL，既重复又混乱。 我们需要在这里做一点抽象，提取 一些共同的东西出来。 

### 关于漂亮URL的一点建议 

如果你有其它web平台的开发经验（如PHP或Java），你可能会想：嘿！让我们用查询字符串参数吧！ 就像/time/plus?hours=3里面的小时应该在查询字符串中被参数hours指定（问号后面的是参数）。 
你 可以 在Django里也这样做 (如果你真的想要这样做，我们稍后会告诉你怎么做）， 但是Django的一个核心理念就是URL必须看起来漂亮。 URL /time/plus/3/ 更加清晰， 更简单，也更有可读性，可以很容易的大声念出来，因为它是纯文本，没有查询字符串那么 复杂。 漂亮的URL就像是高质量的Web应用的一个标志。 

Django的URL配置系统可以使你很容易的设置漂亮的URL，而尽量不要考虑它的 反面 。 
那么，我们如何设计程序来处理任意数量的时差？ 答案是：使用通配符（wildcard URLpatterns）。正如我们之前提到过，一个URL模式就是一个正则表达式。因此，这里可以使用d+来匹配1个以上的数字。
```python
    urlpatterns = [
        re_path(r'^time/plus/\d+/$',hours_ahead),

    ]
```

这个URL模式将匹配类似 /time/plus/2/ , /time/plus/25/ ,甚至 /time/plus/100000000000/ 的任何URL。 更进一步，让我们把它限制在最大允许99个小时， 这样我们就只允许一个或两个数字，正则表达式的语法就是 \d{1,2} : 
```python
    re_path(r^time/plus/\d{1,2}/$', hours_ahead),
```

### 备注 

在建造Web应用的时候，尽可能多考虑可能的数据输入是很重要的，然后决定哪些我们可以接受。 在这里我们就设置了99个小时的时间段限制。 

另外一个重点，正则表达式字符串的开头字母“r”。 它告诉Python这是个原始字符串，不需要处理里面的反斜杠（转义字符）。 在普通Python字符串中，反斜杠用于特殊字符的转义。比如n转义成一个换行符。 当你用r把它标示为一个原始字符串后，Python不再视其中的反斜杠为转义字符。也就是说，“n”是两个字符串：“”和“n”。由于反斜杠在Python代码和正则表达式中有冲突，因此建议你在Python定义正则表达式时都使用原始字符串。 从现在开始，本文所有URL模式都用原始字符串。 
现在我们已经设计了一个带通配符的URL，我们需要一个方法把它传递到视图函数里去，这样 我们只用一个视图函数就可以处理所有的时间段了。 我们使用圆括号把参数在URL模式里标识 出来。 在这个例子中，我们想要把这些数字作为参数，用圆括号把 \d{1,2} 包围起来： 
```python
    re_path(r'^time/plus/(\d{1,2})/$', hours_ahead),
```
如果你熟悉正则表达式，那么你应该已经了解，正则表达式也是用圆括号来从文本里 提取 数据的。

最终的URLconf包含上面两个视图，如：
```python
    from django.contrib import admin
    from django.urls import path,re_path
    from mysite.views import hello,current_datetime,hours_ahead
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('hello/',hello),
        path('time/',current_datetime),
        path('another-time-page/',current_datetime),
        re_path(r'^time/plus/(\d{1,2})/$', hours_ahead),
    ]
```

现在开始写 hours_ahead 视图。 

编码次序 

这个例子中，我们先写了URLpattern ，然后是视图，但是在前面的例子中， 我们先写了视图，然后是URLpattern 。 哪一种方式比较好？ 
嗯，怎么说呢，每个开发者是不一样的。 
如果你是喜欢从总体上来把握事物（注： 或译为“大局观”）类型的人，你应该会想在项目开始 的时候就写下所有的URL配置。 
如果你从更像是一个自底向上的开发者，你可能更喜欢先写视图， 然后把它们挂接到URL上。 这同样是可以的。 
最后，取决与你喜欢哪种技术，两种方法都是可以的。

hours_ahead 和我们以前写的 current_datetime 很象，关键的区别在于： 它多了一个额外参数，时间差。以下是view代码： 
```python
    def hours_ahead(request,offset):
    try:
        offset = int(offset)
    except ValueError:
        raise Http404

    dt = datetime.datetime.now() + datetime.timedelta(hours=offset)
    html = "<html><body>In %s hour(s), it will be %s.</body></html>" % (offset, dt)
    return HttpResponse(html)
```
让我们逐行分析一下代码： 
视图函数, hours_ahead , 有 两个 参数: request 和 offset。
request 是一个 HttpRequest 对象, 就像在 current_datetime 中一样. 再说一次好了: 每一个视图 总是 以一个 HttpRequest 对象作为 它的第一个参数。
offset 是从匹配的URL里提取出来的。 例如：如果请求URL是/time/plus/3/，那么offset将会是3；如果请求URL是/time/plus/21/，那么offset将会是21。请注意：捕获值永远都是字符串（string）类型，而不会是整数（integer）类型，即使这个字符串全由数字构成（如：“21”）。 
（从技术上来说，捕获值总是Unicode objects，而不是简单的Python字节串，但目前不需要担心这些差别。） 
在这里我们命名变量为 offset ，你也可以任意命名它，只要符合Python 的语法。 变量名是无关紧要的，重要的是它的位置，它是这个函数的第二个 参数 (在 request 的后面）。 你还可以使用关键字来定义它，而不是用 位置。 

我们在这个函数中要做的第一件事情就是在 offset 上调用 int() . 这会把这个字符串值转换为整数。 
请留意：如果你在一个不能转换成整数类型的值上调用int()，Python将抛出一个ValueError异常。如：int(‘foo’)。在这个例子中，如果我们遇到ValueError异常，我们将转为抛出django.http.Http404异常——正如你想象的那样：最终显示404页面（提示信息：页面不存在）。 

机灵的读者可能会问： 我们在URL模式中用正则表达式(d{1,2})约束它，仅接受数字怎么样？这样无论如何，offset都是由数字构成的。 答案是：我们不会这么做，因为URLpattern提供的是“适度但有用”级别的输入校验。万一这个视图函数被其它方式调用，我们仍需自行检查ValueError。实践证明，在实现视图函数时，不臆测参数值的做法是比较好的。 松散耦合，还记得么？

下一行，计算当前日期/时间，然后加上适当的小时数。 在current_datetime视图中，我们已经见过datetime.datetime.now()。这里新的概念是执行日期/时间的算术操作。我们需要创建一个datetime.timedelta对象和增加一个datetime.datetime对象。 结果保存在变量dt中。 
这一行还说明了，我们为什么在offset上调用int()——datetime.timedelta函数要求hours参数必须为整数类型。 
这行和前面的那行的的一个微小差别就是，它使用带有两个值的Python的格式化字符串功能， 而不仅仅是一个值。 因此，在字符串中有两个 %s 符号和一个以进行插入的值的元组： (offset, dt) 。 
最终，返回一个HTML的HttpResponse。 如今，这种方式已经过时了。

在完成视图函数和URL配置编写后，启动Django开发服务器，用浏览器访问 http://127.0.0.1:8000/time/plus/3/ 来确认它工作正常。 然后是 http://127.0.0.1:8000/time/plus/5/ 。再然后是 http://127.0.0.1:8000/time/plus/24/ 。最后，访问 http://127.0.0.1:8000/time/plus/100/ 来检验URL配置里设置的模式是否只 接受一个或两个数字；Django会显示一个 Page not found error 页面, 和以前看到的 404 错误一样。 访问URL http://127.0.0.1:8000/time/plus/ (没有 定义时间差) 也会抛出404错误。



