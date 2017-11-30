=============================================================================
救援模式挂载lvm出错-mount unknown filesystem type 'lvm2_member'解决
=============================================================================

http://blog.51cto.com/tonychiu/769934


rhel6救援模式挂载lvm出错：

::

    mount unknown filesystem type 'lvm2_member'

解决办法：

1、从fdisk 信息不难看出，已经加密的 “*” ,肯定是我的引导分区，而我的真实/ 安装在/dev/sda2上的。

2、挂载出错

3、查看物理卷、逻辑卷：pvs

::

    pvs
    vgs

4、查看逻辑卷：lvdisplay


::

    lvdisplay

5、如未激活，需要激活逻辑卷

::

    vgchange -ay /dev/centos_miwifi-r1cm


备注：步骤3-5的命令有时报command not found，解决

::

    bash-4.1#lvm
    lvm >

这样再执行如上命令

6、挂载逻辑卷

::

    mkdir /mnt/sdaroot
    mount /dev/centos_miwifi-r1cm/root /mnt/sdaroot
    ls /mnt/sdaroot/var/log/

原来系统 的 /var/log/ 转移

::

    sh-4.1# mount /dev/sdc4 /mnt/upan/log/
    sh-4.1# cp -a /mnt/sdaroot/var/log/ /mnt/upan/log/

这样就把 原来系统 的 /var/log/ 转移到了 /mnt/upan/log/ ，也就是U盘的 log 目录下。

如果说做一个 chroot, 则如下：

::

    sh-4.1# chroot /mnt/sdaroot/
    bash-4.1# 


这里，在chroot之前 sh-4.1# 目录下的/ 是内存中的 / ，也叫伪根；chroot后 bash-4.1# 目录下的/ 才是系统真实的/ ，也叫真根



