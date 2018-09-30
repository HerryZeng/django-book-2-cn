## 模板系统基本知识

模板是一个文本，用于分离文档的表现形式和内容。 模板定义了**占位符**以及各种用于规范文档该如何显示的各部分**基本逻辑**（模板标签）。 模板通常用于产生HTML，但是Django的模板也能产生任何基于文本格式的文档。 

让我们从一个简单的例子模板开始。 该模板描述了一个向某个与公司签单人员致谢 HTML 页面。 可将其视为一个格式信函：
```html
    <html>
    <head><title>Ordering notice</title></head>
    
    <body>
    
    <h1>Ordering notice</h1>
    
    <p>Dear {{ person_name }},</p>
    
    <p>Thanks for placing an order from {{ company }}. It's scheduled to
    ship on {{ ship_date|date:"F j, Y" }}.</p>
    
    <p>Here are the items you've ordered:</p>
    
    <ul>
    {% for item in item_list %}
        <li>{{ item }}</li>
    {% endfor %}
    </ul>
    
    {% if ordered_warranty %}
        <p>Your warranty information will be included in the packaging.</p>
    {% else %}
        <p>You didn't order a warranty, so you're on your own when
        the products inevitably stop working.</p>
    {% endif %}
    
    <p>Sincerely,<br />{{ company }}</p>
    
    </body>
    </html>
```
该模板是一段添加了些许变量和模板标签的基础 HTML 。 让我们逐步分析一下： 
用两个大括号括起来的文字（例如 {{ person_name }} ）称为 **变量**(variable) 。这意味着在此处插入指定变量的值。 如何指定变量的值呢？ 稍后就会说明。 
被大括号和百分号包围的文本(例如 {% if ordered_warranty %} )是 **模板标签**(template tag) 。标签(tag)定义比较明确，即： 仅通知模板系统完成某些工作的标签。 
这个例子中的模板包含一个for标签（ {% for item in item_list %} ）和一个if 标签（{% if ordered_warranty %} ） 

for标签类似Python的for语句，可让你循环访问序列里的每一个项目。 if 标签，正如你所料，是用来执行逻辑判断的。 在这里，tag标签检查ordered_warranty值是否为True。如果是，模板系统将显示{% if ordered_warranty %}和{% else %}之间的内容；否则将显示{% else %}和{% endif %}之间的内容。{% else %}是可选的。 

最后，这个模板的第二段中有一个关于filter过滤器的例子，它是一种最便捷的转换变量输出格式的方式。 如这个例子中的{{ship_date|date:”F j, Y” }}，我们将变量ship_date传递给date过滤器，同时指定参数”F j,Y”。date过滤器根据参数进行格式输出。 过滤器是用管道符(|)来调用的，具体可以参见Unix管道符。 

Django 模板含有很多内置的tags和filters,我们将陆续进行学习. 附录F列出了很多的tags和filters的列表,熟悉这些列表对你来说是个好建议. 你依然可以利用它创建自己的tag和filters。这些我们在第9章会讲到。


