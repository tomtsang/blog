==============================
Object Storage Quick Start
==============================

INSTALLING CEPH OBJECT GATEWAY
===============================

ceph-client：

::

    cephu@ceph-client:~$ sudo ufw allow 7480
    Rule added
    Rule added (v6)
    cephu@ceph-client:~$ sudo ufw status
    Status: active

    To                         Action      From
    --                         ------      ----
    22                         ALLOW       Anywhere
    7480                       ALLOW       Anywhere
    22 (v6)                    ALLOW       Anywhere (v6)
    7480 (v6)                  ALLOW       Anywhere (v6)

    cephu@ceph-client:~$

ceph-admin:

::

    cephu@cephadmin:~/my-cluster$ ping ceph-client
    PING ceph-client (192.168.31.172) 56(84) bytes of data.
    64 bytes from ceph-client (192.168.31.172): icmp_seq=1 ttl=64 time=0.558 ms
    ^C
    --- ceph-client ping statistics ---
    1 packets transmitted, 1 received, 0% packet loss, time 0ms
    rtt min/avg/max/mdev = 0.558/0.558/0.558/0.000 ms
    cephu@cephadmin:~/my-cluster$ ceph-deploy install --rgw ceph-client
    [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
    [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /home/cephu/.local/bin/ceph-deploy install --rgw ceph-client
    [ceph_deploy.cli][INFO  ] ceph-deploy options:
    [ceph_deploy.cli][INFO  ]  verbose                       : False
    [ceph_deploy.cli][INFO  ]  testing                       : None
    [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7f61e87c4908>
    [ceph_deploy.cli][INFO  ]  cluster                       : ceph
    [ceph_deploy.cli][INFO  ]  dev_commit                    : None
    [ceph_deploy.cli][INFO  ]  install_mds                   : False
    [ceph_deploy.cli][INFO  ]  stable                        : None
    [ceph_deploy.cli][INFO  ]  default_release               : False
    [ceph_deploy.cli][INFO  ]  username                      : None
    [ceph_deploy.cli][INFO  ]  adjust_repos                  : True
    [ceph_deploy.cli][INFO  ]  func                          : <function install at 0x7f61e8e9b848>
    [ceph_deploy.cli][INFO  ]  install_mgr                   : False
    [ceph_deploy.cli][INFO  ]  install_all                   : False
    [ceph_deploy.cli][INFO  ]  repo                          : False
    [ceph_deploy.cli][INFO  ]  host                          : ['ceph-client']
    [ceph_deploy.cli][INFO  ]  install_rgw                   : True
    [ceph_deploy.cli][INFO  ]  install_tests                 : False
    [ceph_deploy.cli][INFO  ]  repo_url                      : None
    [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
    [ceph_deploy.cli][INFO  ]  install_osd                   : False
    [ceph_deploy.cli][INFO  ]  version_kind                  : stable
    [ceph_deploy.cli][INFO  ]  install_common                : False
    [ceph_deploy.cli][INFO  ]  overwrite_conf                : False
    [ceph_deploy.cli][INFO  ]  quiet                         : False
    [ceph_deploy.cli][INFO  ]  dev                           : master
    [ceph_deploy.cli][INFO  ]  nogpgcheck                    : False
    [ceph_deploy.cli][INFO  ]  local_mirror                  : None
    [ceph_deploy.cli][INFO  ]  release                       : None
    [ceph_deploy.cli][INFO  ]  install_mon                   : False
    [ceph_deploy.cli][INFO  ]  gpg_url                       : None
    [ceph_deploy.install][DEBUG ] Installing stable version jewel on cluster ceph hosts ceph-client
    [ceph_deploy.install][DEBUG ] Detecting platform for host ceph-client ...
    [ceph-client][DEBUG ] connection detected need for sudo
    [ceph-client][DEBUG ] connected to host: ceph-client
    [ceph-client][DEBUG ] detect platform information from remote host
    [ceph-client][DEBUG ] detect machine type
    [ceph_deploy.install][INFO  ] Distro info: Ubuntu 16.04 xenial
    [ceph-client][INFO  ] installing Ceph on ceph-client
    [ceph-client][INFO  ] Running command: sudo env DEBIAN_FRONTEND=noninteractive DEBIAN_PRIORITY=critical apt-get --assume-yes -q --no-install-recommends install ca-certificates apt-transport-https
    [ceph-client][DEBUG ] Reading package lists...
    [ceph-client][DEBUG ] Building dependency tree...
    [ceph-client][DEBUG ] Reading state information...
    [ceph-client][DEBUG ] apt-transport-https is already the newest version (1.2.24).
    [ceph-client][DEBUG ] ca-certificates is already the newest version (20170717~16.04.1).
    [ceph-client][DEBUG ] The following packages were automatically installed and are no longer required:
    [ceph-client][DEBUG ]   libboost-program-options1.58.0 libboost-random1.58.0 libboost-regex1.58.0
    [ceph-client][DEBUG ]   libcephfs1 libfcgi0ldbl libllvm3.8 libmircommon5 linux-headers-4.4.0-31
    [ceph-client][DEBUG ]   linux-headers-4.4.0-31-generic linux-image-4.4.0-31-generic
    [ceph-client][DEBUG ]   linux-image-extra-4.4.0-31-generic
    [ceph-client][DEBUG ] Use 'sudo apt autoremove' to remove them.
    [ceph-client][DEBUG ] 0 upgraded, 0 newly installed, 0 to remove and 48 not upgraded.
    [ceph-client][INFO  ] Running command: sudo wget -O release.asc https://download.ceph.com/keys/release.asc
    [ceph-client][WARNIN] --2017-10-25 14:45:28--  https://download.ceph.com/keys/release.asc
    [ceph-client][WARNIN] Resolving download.ceph.com (download.ceph.com)... 158.69.68.124, 2607:5300:201:2000::3:58a1
    [ceph-client][WARNIN] Connecting to download.ceph.com (download.ceph.com)|158.69.68.124|:443... connected.
    [ceph-client][WARNIN] HTTP request sent, awaiting response... 200 OK
    [ceph-client][WARNIN] Length: 1645 (1.6K) [application/octet-stream]
    [ceph-client][WARNIN] Saving to: ‘release.asc’
    [ceph-client][WARNIN]
    [ceph-client][WARNIN]      0K .                                                     100%  233M=0s
    [ceph-client][WARNIN]
    [ceph-client][WARNIN] 2017-10-25 14:45:35 (233 MB/s) - ‘release.asc’ saved [1645/1645]
    [ceph-client][WARNIN]
    [ceph-client][INFO  ] Running command: sudo apt-key add release.asc
    [ceph-client][DEBUG ] OK
    [ceph-client][DEBUG ] add deb repo to /etc/apt/sources.list.d/
    [ceph-client][INFO  ] Running command: sudo env DEBIAN_FRONTEND=noninteractive DEBIAN_PRIORITY=critical apt-get --assume-yes -q update
    [ceph-client][DEBUG ] Hit:1 http://cn.archive.ubuntu.com/ubuntu xenial InRelease
    [ceph-client][DEBUG ] Hit:2 http://cn.archive.ubuntu.com/ubuntu xenial-updates InRelease
    [ceph-client][DEBUG ] Hit:3 http://cn.archive.ubuntu.com/ubuntu xenial-backports InRelease
    [ceph-client][DEBUG ] Hit:4 http://www.rabbitmq.com/debian testing InRelease
    [ceph-client][DEBUG ] Get:5 http://security.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
    [ceph-client][DEBUG ] Hit:6 https://download.docker.com/linux/ubuntu xenial InRelease
    [ceph-client][DEBUG ] Get:7 http://security.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [7,472 B]
    [ceph-client][DEBUG ] Get:8 http://security.ubuntu.com/ubuntu xenial-security/restricted i386 Packages [7,472 B]
    [ceph-client][DEBUG ] Get:9 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [3,208 B]
    [ceph-client][DEBUG ] Get:10 http://security.ubuntu.com/ubuntu xenial-security/multiverse i386 Packages [3,384 B]
    [ceph-client][DEBUG ] Hit:11 https://download.ceph.com/debian-jewel xenial InRelease
    [ceph-client][DEBUG ] Fetched 124 kB in 7s (16.8 kB/s)
    [ceph-client][DEBUG ] Reading package lists...
    [ceph-client][INFO  ] Running command: sudo env DEBIAN_FRONTEND=noninteractive DEBIAN_PRIORITY=critical apt-get --assume-yes -q --no-install-recommends install -o Dpkg::Options::=--force-confnew radosgw
    [ceph-client][DEBUG ] Reading package lists...
    [ceph-client][DEBUG ] Building dependency tree...
    [ceph-client][DEBUG ] Reading state information...
    [ceph-client][DEBUG ] radosgw is already the newest version (12.2.1-1xenial).
    [ceph-client][DEBUG ] The following packages were automatically installed and are no longer required:
    [ceph-client][DEBUG ]   libboost-program-options1.58.0 libboost-random1.58.0 libboost-regex1.58.0
    [ceph-client][DEBUG ]   libcephfs1 libfcgi0ldbl libllvm3.8 libmircommon5 linux-headers-4.4.0-31
    [ceph-client][DEBUG ]   linux-headers-4.4.0-31-generic linux-image-4.4.0-31-generic
    [ceph-client][DEBUG ]   linux-image-extra-4.4.0-31-generic
    [ceph-client][DEBUG ] Use 'sudo apt autoremove' to remove them.
    [ceph-client][DEBUG ] 0 upgraded, 0 newly installed, 0 to remove and 48 not upgraded.
    [ceph-client][INFO  ] Running command: sudo ceph --version
    [ceph-client][DEBUG ] ceph version 12.2.1 (3e7492b9ada8bdc9a5cd0feafd42fbca27f9c38e) luminous (stable)
    cephu@cephadmin:~/my-cluster$

看状态

::

    cephu@cephadmin:~/my-cluster$ ceph -s
    cluster:
        id:     d0aa5af1-4f8e-4953-9448-7f1b2448b8a5
        health: HEALTH_OK

    services:
        mon: 3 daemons, quorum cephfsn2,mon1,cephfsn3
        mgr: mon1(active), standbys: node2, node3
        mds: cephfs-jlch-1/1/1 up  {0=node1=up:active}
        osd: 3 osds: 3 up, 3 in
        rgw: 1 daemon active

    data:
        pools:   8 pools, 64 pgs
        objects: 296 objects, 134 MB
        usage:   4286 MB used, 79378 MB / 83664 MB avail
        pgs:     64 active+clean

    cephu@cephadmin:~/my-cluster$



CREATING THE CEPH OBJECT GATEWAY INSTANCE
============================================

admin:

::

    cephu@cephadmin:~/my-cluster$ ceph-deploy rgw create ceph-client
    [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
    [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /home/cephu/.local/bin/ceph-deploy rgw create ceph-client
    [ceph_deploy.cli][INFO  ] ceph-deploy options:
    [ceph_deploy.cli][INFO  ]  username                      : None
    [ceph_deploy.cli][INFO  ]  verbose                       : False
    [ceph_deploy.cli][INFO  ]  rgw                           : [('ceph-client', 'rgw.ceph-client')]
    [ceph_deploy.cli][INFO  ]  overwrite_conf                : False
    [ceph_deploy.cli][INFO  ]  subcommand                    : create
    [ceph_deploy.cli][INFO  ]  quiet                         : False
    [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7f1dc4631bd8>
    [ceph_deploy.cli][INFO  ]  cluster                       : ceph
    [ceph_deploy.cli][INFO  ]  func                          : <function rgw at 0x7f1dc4c7e140>
    [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
    [ceph_deploy.cli][INFO  ]  default_release               : False
    [ceph_deploy.rgw][DEBUG ] Deploying rgw, cluster ceph hosts ceph-client:rgw.ceph-client
    [ceph-client][DEBUG ] connection detected need for sudo
    [ceph-client][DEBUG ] connected to host: ceph-client
    [ceph-client][DEBUG ] detect platform information from remote host
    [ceph-client][DEBUG ] detect machine type
    [ceph_deploy.rgw][INFO  ] Distro info: Ubuntu 16.04 xenial
    [ceph_deploy.rgw][DEBUG ] remote host will use systemd
    [ceph_deploy.rgw][DEBUG ] deploying rgw bootstrap to ceph-client
    [ceph-client][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
    [ceph_deploy.rgw][ERROR ] RuntimeError: config file /etc/ceph/ceph.conf exists with different content; use --overwrite-conf to overwrite
    [ceph_deploy][ERROR ] GenericError: Failed to create 1 RGWs

    cephu@cephadmin:~/my-cluster$ ceph-deploy --overwrite-conf rgw create ceph-client
    [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
    [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /home/cephu/.local/bin/ceph-deploy --overwrite-conf rgw create ceph-client
    [ceph_deploy.cli][INFO  ] ceph-deploy options:
    [ceph_deploy.cli][INFO  ]  username                      : None
    [ceph_deploy.cli][INFO  ]  verbose                       : False
    [ceph_deploy.cli][INFO  ]  rgw                           : [('ceph-client', 'rgw.ceph-client')]
    [ceph_deploy.cli][INFO  ]  overwrite_conf                : True
    [ceph_deploy.cli][INFO  ]  subcommand                    : create
    [ceph_deploy.cli][INFO  ]  quiet                         : False
    [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7fb62d844bd8>
    [ceph_deploy.cli][INFO  ]  cluster                       : ceph
    [ceph_deploy.cli][INFO  ]  func                          : <function rgw at 0x7fb62de91140>
    [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
    [ceph_deploy.cli][INFO  ]  default_release               : False
    [ceph_deploy.rgw][DEBUG ] Deploying rgw, cluster ceph hosts ceph-client:rgw.ceph-client
    [ceph-client][DEBUG ] connection detected need for sudo
    [ceph-client][DEBUG ] connected to host: ceph-client
    [ceph-client][DEBUG ] detect platform information from remote host
    [ceph-client][DEBUG ] detect machine type
    [ceph_deploy.rgw][INFO  ] Distro info: Ubuntu 16.04 xenial
    [ceph_deploy.rgw][DEBUG ] remote host will use systemd
    [ceph_deploy.rgw][DEBUG ] deploying rgw bootstrap to ceph-client
    [ceph-client][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
    [ceph-client][WARNIN] rgw keyring does not exist yet, creating one
    [ceph-client][DEBUG ] create a keyring file
    [ceph-client][DEBUG ] create path recursively if it doesn't exist
    [ceph-client][INFO  ] Running command: sudo ceph --cluster ceph --name client.bootstrap-rgw --keyring /var/lib/ceph/bootstrap-rgw/ceph.keyring auth get-or-create client.rgw.ceph-client osd allow rwx mon allow rw -o /var/lib/ceph/radosgw/ceph-rgw.ceph-client/keyring
    [ceph-client][INFO  ] Running command: sudo systemctl enable ceph-radosgw@rgw.ceph-client
    [ceph-client][WARNIN] Created symlink from /etc/systemd/system/ceph-radosgw.target.wants/ceph-radosgw@rgw.ceph-client.service to /lib/systemd/system/ceph-radosgw@.service.
    [ceph-client][INFO  ] Running command: sudo systemctl start ceph-radosgw@rgw.ceph-client
    [ceph-client][INFO  ] Running command: sudo systemctl enable ceph.target
    [ceph_deploy.rgw][INFO  ] The Ceph Object Gateway (RGW) is now running on host ceph-client and default port 7480
    cephu@cephadmin:~/my-cluster$

CONFIGURING THE CEPH OBJECT GATEWAY INSTANCE
=============================================

这里是配置web端口成 80 .

这里我就不做了，我没必要。修改也简单，看一下就明白了。

我的 ceph-client 的IP是 192.168.31.172，则：
我就直接到浏览器下打开 http://192.168.31.172:7480/

输出大体如下：

::

    This XML file does not appear to have any style information associated with it. The document tree is shown below.
    <ListAllMyBucketsResult xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
        <Owner>
            <ID>anonymous</ID>
            <DisplayName/>
        </Owner>
        <Buckets/>
    </ListAllMyBucketsResult>

好了，这一小节就到这里了。