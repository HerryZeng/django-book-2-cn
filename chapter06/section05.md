## Admin是如何工作的

在幕后，管理工具是如何工作的呢？ 其实很简单。 

1. 当服务启动时，`Django`从`url.py`引导`URLconf`，然后执行`admin.autodiscover()` 语句。 这个函数遍历`INSTALLED_APPS`配置，并且寻找相关的 `admin.py`文件。 如果在指定的`app`目录下找到`admin.py`，它就执行其中的代码。 在`books`应用程序目录下的`admin.py`文件中，每次调用`admin.site.register()`都将那个模块注册到管理工具中。 管理工具只为那些明确注册了的模块显示一个编辑/修改的界面。 

2. 应用程序`django.contrib.auth`包含自身的`admin.py` ，所以`Users`和`Groups`能在管理工具中自动显示。 其它的`django.contrib`应用程序，如`django.contrib.redirects`，其它从网上下在的第三方`Django`应用程序一样，都会自行添加到管理工具。 

3.综上所述，管理工具其实就是一个`Django`应用程序，包含自己的模块、模板、视图和`URLpatterns`。 你要像添加自己的视图一样，把它添加到`URLconf`里面。 你可以在`Django`基本代码中的`django/contrib/admin` 目录下，检查它的模板、视图和`URLpatterns`，但你不要尝试直接修改其中的任何代码，因为里面有很多地方可以让你自定义管理工具的工作方式。 （如果你确实想浏览`Django`管理工具的代码，请谨记它在读取关于模块的元数据过程中做了些不简单的工作，因此最好花些时间阅读和理解那些代码。） 
