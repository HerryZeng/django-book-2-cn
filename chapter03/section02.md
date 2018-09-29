## 你的第一个URLconf

现在，如果你再运行：`python manage.py runserver`，你还将看到Django的欢迎页面，而看不到我们刚才写的Hello world显示页面。那是因为我们的mysite项目还对hello视图一无所知。我们需要通过一个详细描述的URL来显式的告诉它并且激活这个视图。（继续我们刚才类似发布静态HTML文件的例子。现在我们已经创建了HTML文件，但还没有把它上传至服务器的目录。）为了绑定视图函数和URL，我们使用URLconf。 

`URLconf`就像是 Django 所支撑网站的目录。它的本质是 URL 模式以及要为该 URL 模式调用的视图函数之间的映射表。 你就是以这种方式告诉 Django，对于这个 URL 调用这段代码，对于那个 URL 调用那段代码。 例如，当用户访问/foo/时，调用视图函数foo_view()，这个视图函数存在于Python模块文件view.py中。 

前一章中执行 django-admin.py startproject 时，该脚本会自动为你建了一份 URLconf（即 urls.py 文件）。 默认的urls.py会像下面这个样子：
```python
    """mysite URL Configuration

    The `urlpatterns` list routes URLs to views. For more information please see:
        https://docs.djangoproject.com/en/2.1/topics/http/urls/
    Examples:
    Function views
        1. Add an import:  from my_app import views
        2. Add a URL to urlpatterns:  path('', views.home, name='home')
    Class-based views
        1. Add an import:  from other_app.views import Home
        2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
    Including another URLconf
        1. Import the include() function: from django.urls import include, path
        2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
    """
    from django.contrib import admin
    from django.urls import path
    from mysite.views import hello
    
    urlpatterns = [
        # path('admin/', admin.site.urls),
        path('hello/',hello),
    ]
```
让我们逐行解释一下代码：
+ 第一行导入`jango.contrib.admin`模块。
+ 第二行导入`django.urls.path`模块。
+ 第三行设置变量`urlpatterns `,此变量为列表，其中第一项称为URLpattern,每项是一个path()或re_path()的项。path函数的第一个参数是request实例的path路径，第二个参数是处理此path路径的视图函数名。即 URL /hello/ 的请求都应由 hello 这个视图函数来处理。 re_path函数跟path一样，只不过第一个参数支持正则表达式，后面的参数跟path函数一样。

### Python 搜索路径 

如果你想看Python搜索路径的值，运行Python交互解释器，然后输入：
```python
    import sys
    print sys.path
```
通常，你不必关心 Python 搜索路径的设置。 Python 和 Django 会在后台自动帮你处理好。 


启动Django开发服务器来测试修改好的 URLconf, 运行命令行 python manage.py runserver 。 (如果你让它一直运行也可以，开发服务器会自动监测代码改动并自动重新载入，所以不需要手工重启） 开发服务器的地址是 http://127.0.0.1:8000/ ，打开你的浏览器访问 http://127.0.0.1:8000/hello/ 。 你就可以看到输出结果了。 开发服务器将自动检测Python代码的更改来做必要的重新加载， 所以你不需要重启Server在代码更改之后。服务器运行地址`` http://127.0.0.1:8000/`` ，所以打开浏览器直接输入`` http://127.0.0.1:8000/hello/`` ，你将看到由你的Django视图输出的Hello world。 

万岁！ 你已经创建了第一个Django的web页面。 

### 正则表达式

正则表达式 (或 regexes ) 是通用的文本模式匹配的方法。 Django URLconfs 允许你 使用任意的正则表达式来做强有力的URL映射，不过通常你实际上可能只需要使用很少的一 部分功能。 这里是一些基本的语法。

<table>
    <thead>
        <th>符号</th>
        <th>匹配</th>
    </thead>
    <tbody>
        <tr>
            <td>. (dot)</td>
            <td>任意单一字符</td>
        </tr>
        <tr>
            <td>\d</td>
            <td>任意一位数字</td>
        </tr>
        <tr>
            <td>[A-Z]</td>
            <td>A 到 Z中任意一个字符（大写）</td>
        </tr>
        <tr>
            <td>[a-z]</td>
            <td>a 到 z中任意一个字符（小写）</td>
        </tr>
        <tr>
            <td>[A-Za-z]</td>
            <td>a 到 z中任意一个字符（不区分大小写）</td>
        </tr>
        <tr>
            <td>+</td>
            <td>匹配一个或更多 (例如, \d+ 匹配一个或 多个数字字符)</td>
        </tr>
        <tr>
            <td>[^/]+</td>
            <td>一个或多个不为‘/’的字符</td>
        </tr>
        <tr>
            <td>?</td>
            <td>零个或一个之前的表达式（例如：\d? 匹配零个或一个数字）</td>
        </tr>
        <tr>
            <td>*</td>
            <td>匹配0个或更多 (例如, \d* 匹配0个 或更多数字字符)</td>
        </tr>
        <tr>
            <td>{1,3}</td>
            <td>介于一个和三个（包含）之前的表达式（例如，\d{1,3}匹配一个或两个或三个数字）
</td>
        </tr>
    </tbody>
</table>

有关正则表达式的更多内容，请访问 [https://docs.python.org/3/library/re.html?highlight=regexes](https://docs.python.org/3/library/re.html?highlight=regexes)

