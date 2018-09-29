# Django站点管理

对于某一类网站， 管理界面 是基础设施中非常重要的一部分。 这是以网页和有限的可信任管理者为基础的界面，它可以让你添加，编辑和删除网站内容。一些常见的例子： 你可以用这个界面发布博客，后台的网站管理者用它来润色读者提交的内容，你的客户用你给他们建立的界面工具更新新闻并发布在网站上，这些都是使用管理界面的例子。

但是管理界面有一问题： 创建它太繁琐。 当你开发对公众的功能时，网页开发是有趣的，但是创建管理界面通常是千篇一律的。 你必须认证用户，显示并管理表格，验证输入的有效性诸如此类。 这很繁琐而且是重复劳动。

Django 在对这些繁琐和重复的工作进行了哪些改进？ 它用不能再少的代码为你做了所有的一切。 Django 中创建管理界面已经不是问题。 

这一章是关于 Django 的自动管理界面。 这个特性是这样起作用的： 它读取你模式中的元数据，然后提供给你一个强大而且可以使用的界面，网站管理者可以用它立即工作。

请注意我们建议你读这章，即使你不打算用admin。因为我们将介绍一些概念，这些概念可以应用到Django的所有方面，而不仅仅是admin。