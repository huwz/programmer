Appache 阿帕奇
==============

新手教学
--------

.. note::
 http://httpd.apache.org/docs/current/getting-started.html

* ``Clients, Server, URLs``

  网站上的地址是通过资源唯一标识符 URL [1]_ 描述的；
  一个完整 URL 包括协议（如 ``http``），服务器名（如 ``www.apache.org``），URL 路径以及可能的查询串（例如：``?arg=value``）。
  该查询串作为额外的参数传递给服务器。

  .. note:: URL 路径是指 URL 中除了服务器名和端口号之外，问号之前的内容。例如：
   http://127.0.0.1:8000/query?name=Appache，其 URL-path 为 `"/query"`
  
  
  客户端（例如：网站浏览器）连接服务器（例如：阿帕奇服务器），使用的是指定的协议；
  它通过 URL 路径发起一个请求向服务器索取资源。

  URL 路径指向的是服务器上的某种资源。它可能是文本文件（比如 ``HTML`` 文档），可执行程序，或者其他的程序文件（比如 ``index.php``）。

  .. note:: 你可以在本地创建 ``HTML`` 文件，然后通过 URL-path 链接该文件。
   这样浏览器就可以通过该 URL-path 访问到对应的 ``HTML`` 文件了。
   一般网站主页的都是通过这种方式实现。

  服务器给客户端发回它对请求的处理响应，包括响应状态码，和响应数据（``response body``）。
  状态码表明请求是否成功被服务器接受并处理；如果出错，则指定错误信息对应的错误码。
  服务器通过状态码提示客户端怎么进行后续处理。

* 主机名和 DNS
  
  .. note:: DNS 表示域名服务器
  
  为连接到服务器，客户端首先需要知道服务器的名称和 IP 地址，也就是服务器在因特网中的位置。
  要让你建立的服务器可以在因特网中被他人访问，你需要确保服务器名称在 DNS 中。

  一个 IP 地址可能对应多个服务器名称，而同一个物理服务器可能具有多个 IP 地址。
  因此在同一个物理服务器上可以建立多个网站，使用的是“虚拟主机”。

  如果你想测试一个本地服务器（不能被因特网访问），你可以把主机名写入 ``hosts`` 文件（如 ``C:\Windows\system32\drivers\etc\hosts``）。
  例如你想在 ``hosts`` 文件中添加记录，可以将请求 ``www.example.com`` 映射到本地主机，那么该记录可以写为：

  ``127.0.0.1 www.example.com``

* 配置文件和指令
  
  阿帕奇 HTTP 服务器通过文本文件进行配置。
  配置文件可以在服务器主机的任意位置，取决于组建服务器的方式。
  如果阿帕奇软件是通过源文件编译得到的，则默认的配置文件目录为：``/usr/local/apache2/conf``。
  默认文件名为 ``http.conf``。

  配置文件经常被分解为若干更小的子文件，易于管理。
  这些文件通过 ``Include`` 指令加载，子文件的名称或者位置没有特别之处::

    Include /usr/local/apache2/conf/ssl.conf
    Include /usr/local/apache2/conf/vhosts/*.conf


  管理和分解这些文件很有用；如果默认的管理方式对你没有吸引力，你可以随意更改。

  apache 服务器通过配置文件中的配置指令进行配置。
  一个配置指令由一个关键字和一个或者多个参数值构成。

  “我应该把指令放在哪？”，对于这个问题，通常的解答是想让指令在哪起作用。
  如果是全局的配置指令，则应该放在配置文件中，该配置文件不能处在以下节点中：

  * ``<Directory>``
  * ``<Location>``
  * ``<VirtualHost>``
  * ``<..>`` 其他节点
  
  另外，主配置文件，某些指令应该放在 ``.htaccess`` 文件中，这些文件处在内容目录里。
  ``.htaccess`` 文件主要是为无权访问主站配置文件的用户设置的。

配置节点
--------

.. note::
 http://httpd.apache.org/docs/current/sections.html

配置文件中的指令可能应用于整个服务器，也可能服务于某些特定的目录，文件，主机或者 URLs。
如何使用配置节点容器或者 ``.htaccess`` 文件改变其他配置指令的作用范围，即为本节的内容。

* 配置节点容器类型
  
  +-------------+-------------------+
  | 相关模块    | 相关指令          |
  +=============+===================+
  | core        | <Directory>       |
  +-------------+-------------------+
  | mod_version | <DirectoryMatch>  |
  +-------------+-------------------+
  | mod_proxy   | * <Files>         |
  |             | * <FilesMatch>    |
  |             | * <If>            |
  |             | * <IfDefine>      |
  |             | * <IfVerison>     |
  |             | * <IfModule>      |
  |             | * <Loaction>      |
  |             | * <LocationMatch> |
  |             | * <Proxy>         |
  |             | * <ProxyMatch>    |
  |             | * <VirtualHost>   |
  +-------------+-------------------+

  有两种基本的容器类型：

  * 多数的容器只对客户端请求起作用，处于容器中的指令只能作用于与该容器匹配的客户端请求。
  * ``<IfDefine>``，``<IfModule>`` 和 ``<IfVerion>`` 等容器仅在服务器启动和重启时生效。
    服务器启动时，如果条件为真，则处于容器中的指令应用所有请求，否则容器中的指令被忽略。

  ``<IfDefine>`` 中的指令只在执行带有合适参数的命令行 ``http`` 时起作用。
  例如，对于如下配置，服务器通过命令行 ``http -DClosedForNow`` 启动时，会将所有的请求进行重定向：

  .. code-block:: html
  
   <IfDefine ClosedForNow>
       Redirect / http://otherserver.example.com/
   </IfDefine>

  ``<IfModule>`` 中的指令也很类似，它只在服务器中的特定模块可访问时才起作用。
  特定模块必须是由服务器静态编译的，或者如果是动态编译的，则对应的 ``LoadModule`` 配置指令必须处在配置文件的最前面。
  ``<IfModule>`` 只在这种情况下使用，即无论某个模块是否安装，配置文件都可以工作。
  该容器不能包含一些永久有效的指令，因为当模块缺失时，会屏蔽有效的错误信息。

  在下面的例子中， ``MimeMagicFile`` 指令只有在 ``mod mime magic`` 模块可用的情况下起作用

  .. code-block:: html
  
   <IfModule mod_mime_magic.c>
       MimeMagicFile conf/magic
   </IfModule>

  ``<IfVerion>`` 和 ``<IfDefine>``， ``<IfModule>`` 相似，它只在某个服务器的版本执行的时候，才会起作用。
  这个容器用于测试和需要处理不同 ``httpd`` 版本和不同配置的大型网络。

  .. code-block:: html
   
   <IfVerion >= 2.4>
       # this happens only in versions greator or 
       # equal 2.4.0.
   </IfVerion>

  ``<IfDefine>``， ``<IfModule>`` 和 ``<IfVerion>`` 可以用 ``!`` 表示相反条件；
  还可以进行嵌套处理，以实现一些更复杂的限制。

* 文件系统，网站空间和布尔表达式
  
  可以改变文件系统或者网站空间特定位置的配置信息的容器类型是最常用的。
  首先，理解文件系统或者网站空间的不同，很重要。
  
  * 文件系统就是操作系统中看到的磁盘视图
    例如，对于默认的安装，阿帕奇的 ``http`` 工具在 ``/usr/local/Apache2`` 目录下 (Linux) 或者 ``c:/Program Files/Apache Group/Apache2`` (Windows)。
    
    .. note:: 
     反斜杠总是作为阿帕奇 ``httpd`` 配置文件的路径分隔符，无论是 Linux 还是 Windows OS。

  * 网站空间是由服务器发送的在客户端看到的视图
    
    因此网站空间中的 ``/dir/`` 对应于文件系统中 ``/usr/local/apache2/htdocs/dir/`` （在 Unix 中默认安装的情况下如此）。
    网站空间不直接映射到文件系统，因为网页可能是由数据库或者其他程序动态产生的

* 文件系统容器
   
  ``<Directory>``, ``<Files>`` 随同正则副本 ``<DirectoryMatch>``, ``<FilesMatch>`` 一起，作用于文件系统的某个部分。
  ``<Directory>`` 节点包围的指令作用于指定名称的目录及其所有子目录（包括目录中的所有文件）。
  使用 ``.htaccess`` 可以达到相同的效果。
  例如，以下配置，将会检索 ``/var/web/dir1`` 目录和所有子目录：

  .. code-block:: html
  
   <Directory /var/web/dir1>
       options +Indexes
   </Directory>

  ``<Files>`` 中的指令应用于指定的文件，无论文件处在哪个目录。
  如下的例子，如果这些指令放在配置文件的主节点中，则所有对 ``private.html`` 的访问都被禁止，无论该文件在哪：

  .. code-block:: html
   
   <Files private.html>
       Require all denied
   </Files>

  为检索文件系统某部分的某些文件，可以将 ``<Files>`` 和 ``<Directory>`` 联合使用。
  例如：

  .. code-block:: html

   <Directory /var/web/dir1>
       <Files private.html>
           Require all denied
       </Files private.html>
   </Directory>
        
  达到的效果是，在 ``/var/web/dir1`` 以及它的所有子目录下的 ``private.html`` 都不可访问。

* 网站空间容器
   
  ``<Loaction>`` 和它的正则副本 ``<LocationMatch>``，改变网站空间的配置信息。
  例如：

  .. code-block:: html

   <LocationMatch ^/private>
       Require all denied
   </LocationMatch>

  其作用是拒绝所有 URL-path 以 ``/private`` 开始的请求。

  ``<Location>`` 可以和文件系统毫无关系，例如：

  .. code-block:: html

   <Location /server-status>
       setHandler server-status
   </Location>

  没有一个名为 ``server-status`` 的文件，该指令的作用是将特殊 URL 映射到阿帕奇内部的 HTTP 请求处理器。
 
* 重叠网站空间
   
  若使用重叠的 URL，则需要考虑某些节点或者指令的执行顺序。

  如 ``<Location>``：

  .. code-block:: html
 
   <Loaction /foo>
   </Loaction>
   <Loaction /foo/bar>
   </Loaction>

  如 ``<Alias>``：

  .. code-block:: html
   
    Alias /foo/bar /srv/www/uncommon/bar
    Alias /foo /srv/www/common/foo

  如 ``ProxyPass`` 指令：

  .. code-block:: html
   
   ProxyPass /special-area http://special.example.com smax=5 max=10
   ProxyPass / balancer://mycluster/ stickysession=JSESSIONID|jessionid nofailover=On

* 通配符和正则表达式
    
  ``<Directory>``, ``<Files>`` 和 ``<Location>`` 指令可以使用通配符：

  * ``*`` 表示匹配任何字符串
  * ``?`` 表示匹配任何字符
  * ``[seq]`` 表示匹配 seq 中的任何字符
      
  如果需要更复杂的匹配规则，则需要使用正则副本 ``<*Match>``。
  如：``<DirectoryMatch>``, ``<FilesMatch>`` 以及 ``<LocationMatch>`` 允许使用 ``perl`` 兼容的正则表达式。

  不使用正则的节点如下：

  .. code-block:: html
     
   <Directory /home/*/public_html>
       Options Indexes
   </Directory>

  使用正则：

  .. code-block:: html
     
   <FilesMatch \.(?i:gif|je?g|png)$>
       Require all denied
   </FilesMatch>

  .. note:: 
   ``(?option pattern)`` is equivalent to ``pattern/option``;
   ``(?i:gif|je?g|png)$`` alternative: ``(?:gif|je?g|png)$/i`` indicates that the pattern matching is insensitive

  以上指令可以阻止访问许多类型的图片文件。

  正则表达式如果包含命名的分组和后向引用，则可以通过大写的组名添加到应用环境；
  允许模块如 ``mod_write`` 引用正则表达式中的文件路径和 URLs。

  如：

  .. code-block:: html

   <DirectoryMatch ^/var/www/combined/(?<SITENAME>[^/]+)>
       require ldap-group cn=%{env:MATCH_SITENAME},ou=combined,o=Example
   </DirectoryMatch>

* 布尔表达式
   
  ``<If>`` 指令修改配置信息，取决于布尔表达式的值。
  例如，如下配置的作用是，如果 ``HTTP Referer`` 不是以 ``http://www.example.com`` 开头，
  则拒绝访问。

  .. code-block:: html

   <If "!(%{HTTP_REFERER} -strmatch 'http://www.example.com/*')">
        Require all denied
   </If>

使用场合
--------

选择文件系统容器还是网站空间容器，很简单。
如果指令应用于文件系统中的对象，则使用 ``<Directory>`` 或者 ``<Files>``；
如果指令应用的对象不在文件系统中（如由数据库产生的网页），则使用 ``<Location>``。

如果限制访问文件系统中的对象，一定不能用 ``<Loaction>``。
因为许多不同的网站空间（URL）可能映射到同一个文件系统位置，
导致限制被绕过。
例如：

.. code-block:: html

 <Loaction /dir/>
     Require all denied
 </Location>

如果请求是 ``http://yoursite.example.com/dir/``，该配置正常工作。
但如果是一个大小写不敏感的文件系统呢？
你的限制将会被 ``http://yoursite.example.com/dir/`` 轻易绕开。
相反， ``<Directory>`` 指令，会应用到该位置处的任意文件，无论它是怎么调用的。
（只有文件系统链接是例外。
通过符号链接，相同目录可以放到文件系统的多个位置。
``<Directory>`` 会跟随符号链接，并不会更新路径名。
所以，在最高级别的安全设置下，符号链接应该被 ``<Options>`` 指令禁止。）

如果你会想，这些都不会对你产生映像，因为你使用的是一个大小写敏感的文件系统。
但记住并不只有一种方式将网站空间映射到文件系统的同一个位置。
所以还是推荐大家总是使用文件系统容器。
也有例外，限定配置放在 ``<Loaction>`` 中更安全，因为该节点应用于所有请求，而不是某个特定的 URLs。

节点嵌套
--------
  
一些节点类型可以在其他节点类型中嵌套使用：

* ``<Files>`` 可以在 ``<Directory>`` 中使用；
* ``<If>`` 可以在 ``<Directory>``， ``<Location>`` 和 ``<Files>`` 中使用。
* 正则副本也遵循上述规则

虚拟主机
--------

``<VirtualHost>`` 容器包含的指令应用于特地的主机。
这个指令对于在同一台机器上有多个主机，每个主机的配置信息不同。

代理
----

``<Proxy>`` 和 ``<ProxyMatch>`` 只应用于指定 URL 的 ``mod_proxy`` 代理服务器。
如：

.. code-block:: html

 <Proxy http://www.example.com/*>
     Require all granted
 </Proxy>

这些配置将阻止代理服务器访问 www.example.com 网站。  

指令限制
--------

对于不同类型的容器，哪些指令是允许的呢？

记住：凡是 ``<Directory>`` 中允许的指令都可以使用在 ``<DirectoryMatch>``， ``Files>``， ``<FilesMatch>``， ``<Location>``， ``<LocationMatch>``， ``<Proxy>`` 以及 ``<ProxyMatch>`` 中。
也有特例：

* ``<AllowOverride>`` 中的指令只能工作在 ``<Directory>``
* ``FollowSymLinks`` 和 ``SymLinksIfOwnerMatch`` 选项（``Options``）只能用在 ``<Directory>`` 或者 ``htaccess`` 文件中。
* ``Options`` 指令不能用在 ``<Files>`` 和 ``<FilesMatch>`` 中。

节点合并
--------

配置节点的应用顺序很独特。
由于这对配置指令的解析方法产生重大影响，因而有必要知道它的工作方式。

合并的顺序如下：

* ``<Directory>`` （除正则表达式）和 ``.htaccess`` 同时执行（如果可以的话，``.htaccess`` 覆盖 ``<Directory>``）
* ``<DirectoryMatch>`` (``<Directory ~>``)
* ``<Files>`` 和 ``<FilesMatch>`` 同时执行
* ``<Location>`` 和 ``<LocationMatch>`` 同时执行
* ``<If>``
  
除了 ``<Directory>`` 之外，每个组按照出现在配置文件中的位置处理。
``<Directory>`` （第一组）按照目录最短到最长的顺序处理。
如：``<Directory /var/web/dir>`` 将在 ``<Directory /var/wev/dir/subdir>`` 之前处理。
如果有多个 ``<Directory>`` 节点应用到用一个目录，则按照出现顺序处理。
通过 ``Include`` 指令包含的配置文件会被加载到指令位置。

在所有外部节点应用之后，``<VirtualHost>`` 内部节点开始应用。
该节点允许虚拟主机修改主服务器的配置。

如果请求由 ``mod_proxy`` 受理，则 ``<Proxy>`` 将取代 ``<Directory>`` 第一个应用。

后应用的节点可以改写先应用的节点，然而每个模块负责解析改写的采取的方式。
后应用的节点可能需要对某些指令进行“概念”合并。

.. [1] (`Uniform Resource Locators`) 

