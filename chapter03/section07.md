## URL配置和松耦合

现在是好时机来指出Django和URL配置背后的哲学： 松耦合 原则。 简单的说，松耦合是一个 重要的保证互换性的软件开发方法。 

Django的URL配置就是一个很好的例子。 在Django的应用程序中，URL的定义和视图函数之间是松耦合的，换句话说，决定URL返回哪个视图函数和实现这个视图函数是在两个不同的地方。 这使得 开发人员可以修改一块而不会影响另一块。 

例如，考虑一下current_datetime视图。 如果我们想把它的URL 从原来的 /time/ 改变到 /currenttime/ ，我们只需要快速的修改一下URL配置即可， 不用担心这个函数的内部实现。同样的，如果我们想要修改这个函数的内部实现也不用担心会影响 到对应的URL。 

此外，如果我们想要输出这个函数到一些 URL， 我们只需要修改URL配置而不用去改动视图的代码。 在这个例子里，current_datetime被两个URL使用。 这是一个故弄玄虚的例子，但这个方法迟早会用得上。 
```python
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('hello/',hello),
        path('time/',current_datetime),
        path('another-time-page/',current_datetime),
    ]
```
URLconf和视图是松耦合的。 我们将在本书中继续给出这一重要哲学的相关例子。 
