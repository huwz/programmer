Appache 指令
============

``Options``
-----------

功能：设定目录的服务器特性
语法：``Options [+|-] option [[+|-] option] ...``
默认：``Options FollowSymlinks``
应用：服务器配置，虚拟主机，文件目录，``.htacess`` 文件
模块：``core``

+----------------------+---------------------------------------------------------------+---------------------+
| ``option``           | description                                                   | 执行模块            |
+======================+===============================================================+=====================+
| All                  | 所有选项（``MultiViews`` 除外）                               | ``-----``           |
+----------------------+---------------------------------------------------------------+---------------------+
| ExecCGI              | 执行 ``CGI`` 脚本                                             | ``mod_cgi``         |
+----------------------+---------------------------------------------------------------+---------------------+
| FollowSymlinks       | 使用符号链接 [1]_                                             | ``-----``           |
+----------------------+---------------------------------------------------------------+---------------------+
| Includes             | 允许 :ref:`SSI<server_side_includes>`                         | ``mod_include``     |
+----------------------+---------------------------------------------------------------+---------------------+
| IncludesNOEXEC       | 允许 :ref:`SSI<server_side_includes>`；                       | ``-----``           |
|                      | 禁用 ``exec cmd`` 和 ``exec cgi`` 指令；                      |                     |
|                      | ``include virtual <CGI script>`` [1]_                         |                     |
+----------------------+---------------------------------------------------------------+---------------------+
| Indexes              | 若 URL 请求的是目录，而该目录下没有 ``DirectoryIndex``        | ``mod_autoindex``   |
|                      | （如 ``index.html`` )，``mod_autoindex`` 返回格式化的目录索引 |                     |
+----------------------+---------------------------------------------------------------+---------------------+
| MultiViews           | 允许 ``MultiViews`` [2]_                                      | ``mod_negotiation`` |
+----------------------+---------------------------------------------------------------+---------------------+
| SymLinksIfOwnerMatch | 只允许符号链接，对应文件/目录由相同用户 id 支配               | ``-----``           |
+----------------------+---------------------------------------------------------------+---------------------+

.. note::
 ``MultiViews``，``FollowSymlinks`` 和 ``SymLinksIfOwnerMatch`` 必须处于 ``<Directory>/.htacess`` 内

正常情况下，如果多个 ``Options`` 指令都应用于同一个目录，则只有一个生效，其余的会被忽略；选项不进行合并。
如果所有 ``Options`` 指令中的选项都跟一个 ``+`` 或者 ``-``，则选项会做合并。
有 ``+`` 的选项强制加入到现有选项中，而有 ``-`` 的选项则被剔除出当前的选项配置。

用 ``+`` 的好处是将一个 ``Options`` 长指令拆分为多个 ``Options`` 短指令。

.. warning:: ``+-`` 是非法的

举例说明：

.. code-block:: html
 
 <Directory "/web/docs">
     Options Indexes FollowSymlinks
 </Directory>

 <Directory "/web/docs/spec">
     Options Includes
 </Directory>

则只有针对目录 ``/web/docs/spec`` 的 ``Includes`` 会生效。

.. code-block:: html
 
 <Directory "/web/docs">
     Options Indexes FollowSymlinks
 </Directory>

 <Directory "/web/docs/spec">
     Options +Includes -Indexes
 </Directory>

即为 ``/web/docs/spec`` 目录设置了 ``FollowSymlinks`` 和 ``Includes``。

.. note:: 使用 ``-IncludesNOEXEC`` 或者 ``-Includes`` 禁用服务端的包含指令

``AddType``
-----------

将指定后缀的文件进行内容转换，产生新的 ``mime`` 类型的文件。
指令格式为： ``AddType media-type extension [extension]``，后缀可以省略 ``.``。

例如：

.. code-block:: html

 AddType text/html .shtml .shtm

该指令作用是解析 ``.shtml`` / ``.shtm`` 文件中的 ``SSI`` 指令，产生 ``text/html`` 文件。

``AddOutputFilter``
-------------------

将指定后缀的文件交给服务器过滤器进行处理，处理完之后再将结果传给客户端。
格式为：``AddOutputFilter filter[;filter...] extension [extension] ...``。

例如：

.. code-block:: html

 AddOutputFilter INCLUDES;DEFLATE shtml

将 ``.shtml`` 文件中的 ``SSI`` 指令进行处理，再用 ``mod_deflate`` 做压缩。

``MultiViewsMatch``
------------------

使用多视图格式匹配一个文件，加载文件的类型。
语法：``MultiViewsMatch Any|NegotiationOnly|Filters|handlers [handlers|Filters]

``MultiViewsMatch`` 在处理 ``mod_negotiation`` 的多视图格式时，存在三种不同的行为。
在请求文件 ``index.html`` 时允许匹配任何协商后缀满足基本请求的文件，如 ``index.html.en``, ``index.html.fr``。

.. [1] 不改变用于匹配 ``<Directory>`` 节点的 URL 路径名。
.. [2] 脚本目录为 ``ScriptAlias``
.. [3] 阿帕奇 ``HTTPD`` 工具支持 ``HTTP/1.1`` 协议说明书中描述的内容协商。
       基于浏览器更看重资源的媒体类型，语言，字符集和编码方式的特点，选择最好的资源表现方式；
       采取更为智能的方式解析协商信息不全的浏览器请求
