cgi 脚本
========

阿帕奇 HTTP 服务器通过配置 ``Options ExecCGI`` 执行 cgi 脚本。

cgi 脚本就是简单的文本文件，可以用任何文本编辑器编写。
对于 Windows 操作系统，可以用文本文档或者 ``Notepad``。

编写 cgi 脚本没有固定的脚本语言，只要满足 cgi 脚本相关格式即可。
你可以用 Python，也可以用 Perl，还可以用批处理脚本，根据个人情况而定。

本文选择 Python cgi 脚本进行说明。

脚本解析器路径
--------------

任何脚本都要通过特定的脚本解析器执行，Python cgi 脚本也不例外。
Python 脚本通过 ``python.exe`` 解释执行，所以脚本第一行需要给出解析器路径：

.. code-block:: python

 #!<your Python's path>

依据个人安装 Python 的情况不同，填写相应路径。
比如在 Windows 操作系统中，以默认路径安装 Python 2.7.2：``#!C:/Python27/python.exe``。
如果是 Linux 操作系统，则可能是：``!/usr/bin/python``。

.. note:: Perl 脚本也一样：``#!/usr/bin/perl``

``Content-Type`` 头
-------------------

在展示网页给客户端之前，需要告诉客户端网页的媒体类型。
使用 ``Content-Type`` 头即可。

HTTP 头是一些简单的文本行，放在服务器响应体前面发送给浏览器。
浏览器不会显示这些头信息，会依据头信息决定如何解析响应体，如何展示。

``Content-Type`` 头一般是 cgi 脚本输出的第一项内容，例如：

.. code-block:: python
 
 print 'Content-Type: text/html\n\n'

以上示例指定响应体以 HTML 文本格式展示。
需要注意的是，``Content-Type`` 行之后需要有一个空行。

打印网页内容
------------

对于 ``text/html`` 响应，只要将响应内容依据 HTML 格式打印即可。
例如：

.. code-block:: python

 print '<html>'
 print '<body>Hello,world</body>'
 print '</html>'

至此，一个简单的 Python cgi 脚本就完成了。

完整代码如下：

.. code-block:: python
 :linenos:

 #!C:/Python27/python.exe

 print 'Content-Type: text/html\n\n'
 print '<html>'
 print '<body>Hello,world</body>'
 print '</html>'

服务器配置
----------

完成 cgi 脚本之后，需要进行相应的配置，使阿帕奇服务器可以识别并执行该脚本。

1. 加载模块 ``cgi_module.so``，这是前提条件；
2. 让服务器能识别 cgi 脚本。
   
   利用 ``ScriptAlias``, ``AddHandler``, ``SetHandler``等指实现。   

   如：

   .. code-block:: html

    <Directory "F:/my_cgi_bin">
        SetHandler cgi-script
        Options ExecCGI
    </Directory>

   将 `hello_world.py` 放在目录 ``F:/my_cgi_bin/`` 下。

   等价于：

   .. code-block:: html

    <IfModule alias_module>
        ScriptAlias /cgi-bin/ "F:/my_cgi_bin/"
    </IfModule>

   如果要求 ``.py`` 文件才是 cgi 脚本，可以这样：
   
   .. code-block:: html

    <IfModule mime_module>
    <Directory "F:/my_cgi_bin">
        AddHandler cgi-script py
        Options ExecCGI
    </Directory>
    </IfModule>

    或者：

    .. code-block:: html

     <LoctionMatch "/.py$">
         SetHandler cgi-script
     </LoctionMatch>
     
3. 编写 cgi 脚本。

