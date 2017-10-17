
======================
install FAQ 
======================

Q1
=====

::


    ceph-deploy install ceph-admin ceph-osd1 ceph-osd2 mon1
    ...
    [ceph-osd1][DEBUG ] Get:40 https://download.ceph.com/debian-jewel xenial/main amd64 python-rbd amd64 10.2.10-1xenial [1,449 kB]
    [ceph-osd1][DEBUG ] Get:41 https://download.ceph.com/debian-jewel xenial/main amd64 ceph-common amd64 10.2.10-1xenial [15.5 MB]
    [ceph-osd1][DEBUG ] Get:42 https://download.ceph.com/debian-jewel xenial/main amd64 ceph-base amd64 10.2.10-1xenial [50.5 MB]
    [ceph-osd1][WARNIN] No data was received after 300 seconds, disconnecting...
    [ceph-osd1][INFO  ] Running command: sudo ceph --version
    [ceph-osd1][ERROR ] Traceback (most recent call last):
    [ceph-osd1][ERROR ]   File "/usr/lib/python2.7/dist-packages/ceph_deploy/lib/vendor/remoto/process.py", line 119, in run
    [ceph-osd1][ERROR ]     reporting(conn, result, timeout)
    [ceph-osd1][ERROR ]   File "/usr/lib/python2.7/dist-packages/ceph_deploy/lib/vendor/remoto/log.py", line 13, in reporting
    [ceph-osd1][ERROR ]     received = result.receive(timeout)
    [ceph-osd1][ERROR ]   File "/usr/lib/python2.7/dist-packages/ceph_deploy/lib/vendor/remoto/lib/vendor/execnet/gateway_base.py", line 704, in receive
    [ceph-osd1][ERROR ]     raise self._getremoteerror() or EOFError()
    [ceph-osd1][ERROR ] RemoteError: Traceback (most recent call last):
    [ceph-osd1][ERROR ]   File "<string>", line 1036, in executetask
    [ceph-osd1][ERROR ]   File "<remote exec>", line 12, in _remote_run
    [ceph-osd1][ERROR ]   File "/usr/lib/python2.7/subprocess.py", line 711, in __init__
    [ceph-osd1][ERROR ]     errread, errwrite)
    [ceph-osd1][ERROR ]   File "/usr/lib/python2.7/subprocess.py", line 1343, in _execute_child
    [ceph-osd1][ERROR ]     raise child_exception
    [ceph-osd1][ERROR ] OSError: [Errno 2] No such file or directory
    [ceph-osd1][ERROR ]
    [ceph-osd1][ERROR ]
    [ceph_deploy][ERROR ] RuntimeError: Failed to execute command: ceph --version

    cephuser@cephfs5:~/my-cluster$ 

https://blog.54im.com/2016/12/15/centos-install-ceph-doc/
超时问题

Q2
=====

https://serverfault.com/questions/659308/unable-to-add-initial-monitor-to-ceph-in-ubuntu

::

    [global]
    fsid = 33cb5c76-a685-469e-8cdd-fee7c98c3f4d
    mon_initial_members = ceph1,ceph2
    mon_host = 192.168.61.39,192.168.61.40
    auth_cluster_required = cephx
    auth_service_required = cephx
    auth_client_required = cephx
    filestore_xattr_use_omap = true
    public_network = 192.168.61.0/24
    And the running the command:

    $ ceph-deploy --overwrite-conf mon create <ceph-node>