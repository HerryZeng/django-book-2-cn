## 框架是什么？

`Django`在新一代的`Web`框架中非常出色，为什么这么说呢？

为了回答该问题，让我们考虑一下不使用框架，设计`Python`网页应用程序的情形。贯穿整本书，我们多次展示不使用框架实现网站基本功能的方法，让读者认识到框架开发的方便。（不使用框架，更多情况是没有合适的框架可用。重重要的是，理解实现的来龙去脉会使你成为一名优秀的`Web`开发者。）

使用`Python`开发`Web`，最简单，原始和直接的办法是使用CGI标准，在1998年这种方式很流行。现在从应用角度解释它是如何工作：
+ 首先做一个`Python`脚本，输出`HTML`代码；
+ 然后保存成`.cgi`扩展名的文件，通过浏览器访问此文件。

如下示例，用`Python CGI`脚本显示数据库中最新出版的10本书：不用关心语法细节，仅仅感觉一下基本的实现方法：
```python
    #!/usr/bin/env python

    import MySQLdb
    
    print "Content-Type: text/html\n"
    print "<html><head><title>Books</title></head>"
    print "<body>"
    print "<h1>Books</h1>"
    print "<ul>"
    connection = MySQLdb.connect(user='me', passwd='letmein', db='my_db')
    cursor = connection.cursor()
    cursor.execute("SELECT name FROM books ORDER BY pub_date DESC LIMIT 10")
    
    for row in cursor.fetchall():
        print "<li>%s</li>" % row[0]
    
    print "</ul>"
    print "</body></html>"
    
    connection.close()
```