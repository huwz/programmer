Appache 指令
============

``Options``
-----------

功能：配置目录特性

语法：``Options [+|-] option [[+|-] option] ...``

默认：``Options FollowSymlinks``

场景：server config, virtual host, directory, .htacess

覆盖：Options

模块：``core``

+----------------------+----------------------------------------------------------------+---------------------+
| ``option``           | description                                                    | 执行模块            |
+======================+================================================================+=====================+
| None                 | 无                                                             | ``-----``           |
+----------------------+----------------------------------------------------------------+---------------------+
| All                  | 除 ``MutiViews`` 之外的所有选项                                | ``-----``           |
+----------------------+----------------------------------------------------------------+---------------------+
| ExecCGI              | 允许运行 ``CGI`` 脚本                                          | ``mod_cgi``         |
+----------------------+----------------------------------------------------------------+---------------------+
| FollowSymlinks       | 该目录下服务器使用符号链接 [1]_                                | ``-----``           |
+----------------------+----------------------------------------------------------------+---------------------+
| Includes             | 允许 :ref:`SSI<server_side_includes>`                          | ``mod_include``     |
+----------------------+----------------------------------------------------------------+---------------------+
| IncludesNOEXEC       | 允许 :ref:`SSI<server_side_includes>`；                        | ``-----``           |
|                      | 禁用 ``exec cmd`` 和 ``exec cgi`` 指令                         |                     |
+----------------------+----------------------------------------------------------------+---------------------+
| Indexes              | url 对应目录下若没有资源列表文件(如 ``index.html``)，          | ``mod_autoindex``   |
|                      | 模块 ``mod_autoindex`` 会返回该目录下所有文件和目录的列表 [2]_ |                     |
+----------------------+----------------------------------------------------------------+---------------------+
| MutiViews            | 允许内容协商的 ``MutiViews`` 选项                              | ``mod_negotiation`` |
+----------------------+----------------------------------------------------------------+---------------------+
| SymLinksIfOwnerMatch | 允许特定用户身份下，服务器访问该用户拥有的文件/目录时，        | ``-----``           |
|                      | 只使用符号链接                                                 |                     |
+----------------------+----------------------------------------------------------------+---------------------+

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

.. [1] 尽管服务器使用符号链接，但匹配 ``<Directory>`` 节点的路径名不会改变。
       ``FollowSymlinks`` 和 ``SymLinksIfOwnerMatch`` 只能用在 ``<Directory>`` 或者 ``.htacess`` 文件中。
       省略这些选项也不是一个安全策略，因为导致服务器宕机的 ``race condition`` 会影响符号链接测试。
.. [2] 如图：
.. image:: images/index.png