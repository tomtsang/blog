
=========================
Filesystem Quick Start
=========================

http://docs.ceph.com/docs/master/start/quick-cephfs/

PREREQUISITES
^^^^^^^^^^^^^^^^^^

admin 节点：

::

    ceph -s 

看一下，是不是都正常。
这里是看不到 mds 的，要看，要去  moniter 节点

moniter 节点：
我们环境的是 192.168.31.114 

::

    cephu@mon1:~$ ps -ef | grep ceph 
    root      7541  7227  0 Oct23 pts/0    00:00:00 su - cephu
    cephu     7542  7541  0 Oct23 pts/0    00:00:00 -su
    cephu    20793  7542  0 12:39 pts/0    00:00:00 ps -ef
    cephu    20794  7542  0 12:39 pts/0    00:00:00 grep --color=auto ceph
    ceph     21275     1  0 Oct23 ?        00:07:50 /usr/bin/ceph-mon -f --cluster ceph --id mon1 --setuser ceph --setgroup ceph
    ceph     22756     1  0 Oct24 ?        00:03:30 /usr/bin/ceph-mgr -f --cluster ceph --id mon1 --setuser ceph --setgroup ceph
    ceph     25536     1  0 Oct24 ?        00:04:25 /usr/bin/ceph-osd -f --cluster ceph --id 0 --setuser ceph --setgroup ceph
    ceph     26870     1  0 Oct24 ?        00:00:30 /usr/bin/ceph-mds -f --cluster ceph --id node1 --setuser ceph --setgroup ceph
    ceph     29545     1  0 Oct24 ?        00:03:06 /usr/bin/radosgw -f --cluster ceph --name client.rgw.node1 --setuser ceph --setgroup ceph
    cephu@mon1:~$
    cephu@mon1:~$ ps -ef | grep ceph | grep mds
    cephu    21152  7542  0 12:45 pts/0    00:00:00 grep --color=auto mds
    ceph     26870     1  0 Oct24 ?        00:00:30 /usr/bin/ceph-mds -f --cluster ceph --id node1 --setuser ceph --setgroup ceph
    cephu@mon1:~$

CREATE A FILESYSTEM
^^^^^^^^^^^^^^^^^^^^

admin 节点：

::

    cephu@cephadmin:~/my-cluster$ ceph osd pool create cephfs_data 8
    pool 'cephfs_data' created
    cephu@cephadmin:~/my-cluster$ ceph osd pool create cephfs_metadata 8
    pool 'cephfs_metadata' created
    cephu@cephadmin:~/my-cluster$ ceph fs new cephfs-jlch cephfs_metadata cephfs_data
    new fs with metadata pool 8 and data pool 7
    cephu@cephadmin:~/my-cluster$ ceph fs ls
    name: cephfs-jlch, metadata pool: cephfs_metadata, data pools: [cephfs_data ]
    cephu@cephadmin:~/my-cluster$
    cephu@cephadmin:~/my-cluster$ ceph mds stat
    cephfs-jlch-1/1/1 up  {0=node1=up:active}
    cephu@cephadmin:~/my-cluster$

CREATE A SECRET FILE
^^^^^^^^^^^^^^^^^^^^^^^


admin 节点：

::

    cephu@cephadmin:~/my-cluster$ cat ceph.client.admin.keyring
    [client.admin]
            key = AQCtj+5ZnNTvGRAA2RxAGcIQZJnaJSPEz4jdGw==
    cephu@cephadmin:~/my-cluster$ echo "AQCtj+5ZnNTvGRAA2RxAGcIQZJnaJSPEz4jdGw==" > admin.secret
    cephu@cephadmin:~/my-cluster$ ls
    admin.secret                ceph.bootstrap-osd.keyring  ceph.conf             testfile.txt
    ceph.bootstrap-mds.keyring  ceph.bootstrap-rgw.keyring  ceph-deploy-ceph.log
    ceph.bootstrap-mgr.keyring  ceph.client.admin.keyring   ceph.mon.keyring
    cephu@cephadmin:~/my-cluster$ cat admin.secret
    AQCtj+5ZnNTvGRAA2RxAGcIQZJnaJSPEz4jdGw==
    cephu@cephadmin:~/my-cluster$

KERNEL DRIVER
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    cephu@cephadmin:~/my-cluster$ sudo mkdir /mnt/mycephfs
    cephu@cephadmin:~/my-cluster$ sudo mount -t ceph 192.168.31.114:6789:/ /mnt/mycephfs
    mount error 22 = Invalid argument
    cephu@cephadmin:~/my-cluster$ sudo mount -t ceph 192.168.31.114:6789:/ /mnt/mycephfs -o name=admin,secretfile=admin.secret
    cephu@cephadmin:~/my-cluster$ 

如下可以看出， cephfs 将3个物理节点上的磁盘全部空间（82G = 16G + 16G + 50G）作为了自己的空间。

::

    cephu@cephadmin:~/my-cluster$ df -h
    Filesystem                   Size  Used Avail Use% Mounted on
    udev                         3.9G     0  3.9G   0% /dev
    tmpfs                        799M   79M  720M  10% /run
    /dev/mapper/ubuntu--vg-root   71G  4.7G   62G   8% /
    tmpfs                        3.9G     0  3.9G   0% /dev/shm
    tmpfs                        5.0M     0  5.0M   0% /run/lock
    tmpfs                        3.9G     0  3.9G   0% /sys/fs/cgroup
    /dev/loop0                    84M   84M     0 100% /snap/core/3017
    /dev/loop2                    82M   82M     0 100% /snap/core/2898
    /dev/sda1                    472M   58M  391M  13% /boot
    tmpfs                        799M     0  799M   0% /run/user/1000
    /dev/loop3                    84M   84M     0 100% /snap/core/3247
    192.168.31.114:6789:/         82G  4.2G   78G   6% /mnt/mycephfs
    cephu@cephadmin:~/my-cluster$


FILESYSTEM IN USER SPACE (FUSE)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    cephu@cephadmin:~/my-cluster$ sudo ceph-fuse -k ./ceph.client.admin.keyring -m 192.168.31.114:6789 ~/mycephfs
    sudo: ceph-fuse: command not found
    cephu@cephadmin:~/my-cluster$ 

没有这个命令。

农总这个时候，让我去 ceph-client 节点

ceph-client 节点：

::

    cephu@ceph-client:~$ which ceph-fuse
    cephu@ceph-client:~$ apt search ceph-fuse
    Sorting... Done
    Full Text Search... Done
    ceph-fuse/stable 10.2.10-1xenial amd64
    FUSE-based client for the Ceph distributed file system

    ceph-fuse-dbg/stable 10.2.10-1xenial amd64
    debugging symbols for ceph-fuse

    cephu@ceph-client:~$ sudo apt install ceph-fuse
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    The following packages were automatically installed and are no longer required:
    libboost-program-options1.58.0 libboost-random1.58.0 libboost-regex1.58.0 libcephfs1 libfcgi0ldbl libllvm3.8
    libmircommon5 linux-headers-4.4.0-31 linux-headers-4.4.0-31-generic linux-image-4.4.0-31-generic
    linux-image-extra-4.4.0-31-generic
    Use 'sudo apt autoremove' to remove them.
    The following NEW packages will be installed:
    ceph-fuse
    0 upgraded, 1 newly installed, 0 to remove and 48 not upgraded.
    Need to get 2,926 kB of archives.
    After this operation, 7,873 kB of additional disk space will be used.
    Get:1 https://download.ceph.com/debian-jewel xenial/main amd64 ceph-fuse amd64 10.2.10-1xenial [2,926 kB]
    Fetched 2,926 kB in 20s (145 kB/s)
    Selecting previously unselected package ceph-fuse.
    (Reading database ... 152232 files and directories currently installed.)
    Preparing to unpack .../ceph-fuse_10.2.10-1xenial_amd64.deb ...
    Unpacking ceph-fuse (10.2.10-1xenial) ...
    Processing triggers for man-db (2.7.5-1) ...
    Setting up ceph-fuse (10.2.10-1xenial) ...

    cephu@ceph-client:~$ sudo mkdir ~/mycephfs
    cephu@ceph-client:~$ sudo ceph-fuse  -m 192.168.31.114:6789 ~/mycephfs
    ceph-fuse[28422]: starting ceph client
    2017-10-25 14:10:35.041539 7fb1f6430f00 -1 init, newargv = 0x5608ad4acf60 newargc=11
    ceph-fuse[28422]: starting fuse
    cephu@ceph-client:~$ 

可以了，成功了。

看一下状态

::

    cephu@ceph-client:~$ ceph mds stat
    cephfs-jlch-1/1/1 up  {0=node1=up:active}
    cephu@ceph-client:~$ df -h
    Filesystem                   Size  Used Avail Use% Mounted on
    udev                         3.9G     0  3.9G   0% /dev
    tmpfs                        799M  9.0M  790M   2% /run
    /dev/mapper/ubuntu--vg-root   35G  4.2G   29G  13% /
    tmpfs                        3.9G  528K  3.9G   1% /dev/shm
    tmpfs                        5.0M     0  5.0M   0% /run/lock
    tmpfs                        3.9G     0  3.9G   0% /sys/fs/cgroup
    /dev/sda1                    472M  153M  295M  35% /boot
    tmpfs                        799M     0  799M   0% /run/user/113
    tmpfs                        799M     0  799M   0% /run/user/1000
    /dev/rbd0                    3.9G  8.0M  3.8G   1% /mnt/ceph-block-device
    ceph-fuse                     82G  4.2G   78G   6% /home/cephu/mycephfs
    cephu@ceph-client:~$


ADDITIONAL INFORMATION
^^^^^^^^^^^^^^^^^^^^^^^^^^

这里没有什么了。


测试一下
^^^^^^^^^^^^^^

回到 admin 节点吧。

::

    cephu@cephadmin:~/my-cluster$ df -h
    Filesystem                   Size  Used Avail Use% Mounted on
    udev                         3.9G     0  3.9G   0% /dev
    tmpfs                        799M   79M  720M  10% /run
    /dev/mapper/ubuntu--vg-root   71G  4.7G   62G   8% /
    tmpfs                        3.9G     0  3.9G   0% /dev/shm
    tmpfs                        5.0M     0  5.0M   0% /run/lock
    tmpfs                        3.9G     0  3.9G   0% /sys/fs/cgroup
    /dev/loop0                    84M   84M     0 100% /snap/core/3017
    /dev/loop2                    82M   82M     0 100% /snap/core/2898
    /dev/sda1                    472M   58M  391M  13% /boot
    tmpfs                        799M     0  799M   0% /run/user/1000
    /dev/loop3                    84M   84M     0 100% /snap/core/3247
    192.168.31.114:6789:/         82G  4.2G   78G   6% /mnt/mycephfs
    cephu@cephadmin:~/my-cluster$ touch /mnt/mycephfs/h.txt
    touch: cannot touch '/mnt/mycephfs/h.txt': Permission denied
    cephu@cephadmin:~/my-cluster$

为什么这样子？？？
Permission denied
这不是玩我么？

::

    cephu@cephadmin:~/my-cluster$ sudo touch /mnt/mycephfs/h.txt
    cephu@cephadmin:~/my-cluster$ sudo vi /mnt/mycephfs/h.txt
    cephu@cephadmin:~/my-cluster$ sudo ls -l /mnt/mycephfs/
    total 1
    -rw-r--r-- 1 root root 14 Oct 25 14:33 h.txt

那直接修改目录权限。

::

    cephu@cephadmin:~/my-cluster$ sudo chown -R cephu:cephu /mnt/mycephfs/
    cephu@cephadmin:~/my-cluster$ touch /mnt/mycephfs/a.txt
    cephu@cephadmin:~/my-cluster$ echo "hello, world" > /mnt/mycephfs/b.txt
    cephu@cephadmin:~/my-cluster$ cat /mnt/mycephfs/b.txt
    hello, world
    cephu@cephadmin:~/my-cluster$ sudo ls -l /mnt/mycephfs/
    total 1
    -rw-rw-r-- 1 cephu cephu  0 Oct 25 14:34 a.txt
    -rw-rw-r-- 1 cephu cephu  2 Oct 25 14:35 b.txt
    -rw-r--r-- 1 cephu cephu 14 Oct 25 14:33 h.txt
    cephu@cephadmin:~/my-cluster$

好了。这下成功了。

再回到 ceph-client 下测试吧（因为其下的 mount 方式不同，是 ceph-fuse，也测试一下咯）。

ceph-client节点：

::

    cephu@ceph-client:~$ ls mycephfs/
    a.txt  b.txt  h.txt

文件是看到了。
创建一下。

    cephu@ceph-client:~$ echo "c" > mycephfs/b.txt
    cephu@ceph-client:~$ cat mycephfs/b.txt
    c
    cephu@ceph-client:~$

OK。成功。

umount
^^^^^^^^^^^

到我们这个文档结束的时候，记得要 umount 一下哟。（这里只写 admin节点了，ceph-client节点是一样样的。）

admin:

::

    cephu@cephadmin:~/my-cluster$ sudo umount /mnt/mycephfs

检查一下：

::

    cephu@cephadmin:~/my-cluster$ mount
    sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
    proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
    udev on /dev type devtmpfs (rw,nosuid,relatime,size=4067352k,nr_inodes=1016838,mode=755)
    devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
    tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=817512k,mode=755)
    /dev/mapper/ubuntu--vg-root on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
    securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
    tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
    tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
    tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
    cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd)
    pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
    cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
    cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
    cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
    cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
    cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
    cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
    cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
    cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
    cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
    cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
    systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=27,pgrp=1,timeout=0,minproto=5,maxproto=5,direct)
    mqueue on /dev/mqueue type mqueue (rw,relatime)
    hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime)
    debugfs on /sys/kernel/debug type debugfs (rw,relatime)
    fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
    /var/lib/snapd/snaps/core_3017.snap on /snap/core/3017 type squashfs (ro,nodev,relatime)
    /var/lib/snapd/snaps/core_2898.snap on /snap/core/2898 type squashfs (ro,nodev,relatime)
    /dev/sda1 on /boot type ext2 (rw,relatime,block_validity,barrier,user_xattr,acl)
    lxcfs on /var/lib/lxcfs type fuse.lxcfs (rw,nosuid,nodev,relatime,user_id=0,group_id=0,allow_other)
    /dev/mapper/ubuntu--vg-root on /var/lib/docker/aufs type ext4 (rw,relatime,errors=remount-ro,data=ordered)
    tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=817512k,mode=700,uid=1000,gid=1000)
    /var/lib/snapd/snaps/core_3247.snap on /snap/core/3247 type squashfs (ro,nodev,relatime)
    tmpfs on /run/snapd/ns type tmpfs (rw,nosuid,noexec,relatime,size=817512k,mode=755)
    nsfs on /run/snapd/ns/core.mnt type nsfs (rw)
    cephu@cephadmin:~/my-cluster$

确实没有了。安心。


到此，应该这一小节结束。
喝杯水，压压惊！~~~~~~~~~~






