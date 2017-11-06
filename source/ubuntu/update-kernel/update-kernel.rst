

==========================
ubuntu16.04 update-kernel
==========================


env
-----------

::

    192.168.31.118
    192.168.31.119

step
-----------

浏览器打开 http://kernel.ubuntu.com/~kernel-ppa/mainline/

找到适合的 内核版本（这时v4.12), 进入，

找到合适的内核文件(linux-image-4.12.0-041200-generic_4.12.0-041200.201707022031_amd64.deb)

::

    wget http://kernel.ubuntu.com/~kernel-ppa/mainline/v4.12/linux-image-4.12.0-041200-generic_4.12.0-041200.201707022031_amd64.deb

然后安装就可以了。

