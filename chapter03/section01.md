## 你的第一个基于Django的页面: Hello World

正如我们的第一个目标，创建一个网页，用来输出这个著名的示例信息：
**Hello world.**
如果你曾经发布过Hello world页面，但是没有使用网页框架，只是简单的在hello.html文本文件中输入Hello World，然后上传到任意的一个网页服务器上。 注意，在这个过程中，你已经说明了两个关于这个网页的关键信息： 它包括（字符串 "Hello world"）和它的URL( http://www.example.com/hello.html , 如果你把文件放在子目录，也可能是 http://www.example.com/files/hello.html)。 
使用Django，你会用不同的方法来说明这两件事 页面的内容是靠view function（视图函数） 来产生，URL定义在 URLconf 中。首先，我们先写一个Hello World视图函数。

### 第一份视图

