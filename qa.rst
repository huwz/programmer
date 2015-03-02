琐碎的问题
==========

**1. nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)**

80 号端口被其他的进程使用了。

仅以 Windows 系统说明，可以在 cmd 上执行：``netstat -aton|findstr "80"`` 获取进程的 ID。
本人主机上的结果为：`TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 9864`。

通过指令 ``tasklist|findstr "9864"`` 获取进程的名称或ID。
之后可以通过指令 taskkill 来杀死进程：``taskkill /PID 9864 /T /F``。

**2. httpd: (OS 5) Access is denied ...**

``httpd`` 程序处在系统盘中，受到访问限制。

使用 ``Administator`` 身份打开 ``cmd``，再启动 ``httpd``。

**3. 在关闭 Apache 服务器之后，历史上访问过的网页还可以继续访问**

浏览器有缓冲作用，清空浏览器缓存即可。