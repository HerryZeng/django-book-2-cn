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
