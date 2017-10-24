
=================================
Storage Cluster Quick Start
=================================

# http://docs.ceph.com/docs/master/start/quick-ceph-deploy/

2.1 Create the cluster.

        ceph-deploy new node1 不生效的，要写成 ceph-deploy new mon1

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy new mon1

2.2 - 2.3

::

        cephu@cephadmin:~/my-cluster$ cat ~/my-cluster/ceph.conf
        [global]
        fsid = d0aa5af1-4f8e-4953-9448-7f1b2448b8a5
        mon_initial_members = mon1
        mon_host = 192.168.31.114
        auth_cluster_required = cephx
        auth_service_required = cephx
        auth_client_required = cephx

        public network = 192.168.31.1/24
        ms bind ipv6 = true
        cephu@cephadmin:~/my-cluster$

2.4 

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy install node1 node2 node3

2.5 

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mon create-initial

报错，然后，经过 农总在 https://my.oschina.net/u/2475751/blog/647777 查到要运行下面

::

        cephu@cephadmin:~/my-cluster$ ssh mon1 sudo ceph-create-keys --id mon1

再来一次

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mon create-initial

3.1 

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy --overwrite-conf admin node1 node2 node3
        [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
        [ceph_deploy.cli][INFO  ] Invoked (1.5.32): /usr/bin/ceph-deploy --overwrite-conf admin node1 node2 node3
        [ceph_deploy.cli][INFO  ] ceph-deploy options:
        [ceph_deploy.cli][INFO  ]  username                      : None
        [ceph_deploy.cli][INFO  ]  verbose                       : False
        [ceph_deploy.cli][INFO  ]  overwrite_conf                : True
        [ceph_deploy.cli][INFO  ]  quiet                         : False
        [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7f2c1fbe2440>
        [ceph_deploy.cli][INFO  ]  cluster                       : ceph
        [ceph_deploy.cli][INFO  ]  client                        : ['node1', 'node2', 'node3']
        [ceph_deploy.cli][INFO  ]  func                          : <function admin at 0x7f2c20489b18>
        [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
        [ceph_deploy.cli][INFO  ]  default_release               : False
        [ceph_deploy.admin][DEBUG ] Pushing admin keys and conf to node1
        [node1][DEBUG ] connection detected need for sudo
        [node1][DEBUG ] connected to host: node1
        [node1][DEBUG ] detect platform information from remote host
        [node1][DEBUG ] detect machine type
        [node1][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        [ceph_deploy.admin][DEBUG ] Pushing admin keys and conf to node2
        [node2][DEBUG ] connection detected need for sudo
        [node2][DEBUG ] connected to host: node2
        [node2][DEBUG ] detect platform information from remote host
        [node2][DEBUG ] detect machine type
        [node2][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        [ceph_deploy.admin][DEBUG ] Pushing admin keys and conf to node3
        [node3][DEBUG ] connection detected need for sudo
        [node3][DEBUG ] connected to host: node3
        [node3][DEBUG ] detect platform information from remote host
        [node3][DEBUG ] detect machine type
        [node3][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        cephu@cephadmin:~/my-cluster$ 

3.2

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mgr create node1
        usage: ceph-deploy [-h] [-v | -q] [--version] [--username USERNAME]
                        [--overwrite-conf] [--cluster NAME] [--ceph-conf CEPH_CONF]
                        COMMAND ...
        ceph-deploy: error: argument COMMAND: invalid choice: 'mgr' (choose from 'new', 'install', 'rgw', 'mon', 'mds', '                                                                                                                         gatherkeys', 'disk', 'osd', 'admin', 'repo', 'config', 'uninstall', 'purge', 'purgedata', 'calamari', 'forgetkeys                                                                                                                         ', 'pkg')
        cephu@cephadmin:~/my-cluster$ 

报错

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy -h
        usage: ceph-deploy [-h] [-v | -q] [--version] [--username USERNAME]
                        [--overwrite-conf] [--cluster NAME] [--ceph-conf CEPH_CONF]
                        COMMAND ...

        Easy Ceph deployment

        -^-
        /   \
        |O o|  ceph-deploy v1.5.32
        ).-.(
        '/|||\`
        | '|` |
        '|`

        Full documentation can be found at: http://ceph.com/ceph-deploy/docs

        optional arguments:
        -h, --help            show this help message and exit
        -v, --verbose         be more verbose
        -q, --quiet           be less verbose
        --version             the current installed version of ceph-deploy
        --username USERNAME   the username to connect to the remote host
        --overwrite-conf      overwrite an existing conf file on remote host (if
                                present)
        --cluster NAME        name of the cluster
        --ceph-conf CEPH_CONF
                                use (or reuse) a given ceph.conf file

        commands:
        COMMAND               description
        new                 Start deploying a new cluster, and write a
                                CLUSTER.conf and keyring for it.
        install             Install Ceph packages on remote hosts.
        rgw                 Ceph RGW daemon management
        mon                 Ceph MON Daemon management
        mds                 Ceph MDS daemon management
        gatherkeys          Gather authentication keys for provisioning new nodes.
        disk                Manage disks on a remote host.
        osd                 Prepare a data disk on remote host.
        admin               Push configuration and client.admin key to a remote
                                host.
        repo                Repo definition management
        config              Copy ceph.conf to/from remote host(s)
        uninstall           Remove Ceph packages from remote hosts.
        purge               Remove Ceph packages from remote hosts and purge all
                                data.
        purgedata           Purge (delete, destroy, discard, shred) any Ceph data
                                from /var/lib/ceph
        calamari            Install and configure Calamari nodes. Assumes that a
                                repository with Calamari packages is already
                                configured. Refer to the docs for examples
                                (http://ceph.com/ceph-deploy/docs/conf.html)
        forgetkeys          Remove authentication keys from the local directory.
        pkg                 Manage packages on remote hosts.
        cephu@cephadmin:~/my-cluster$ 

我去，真的没有 mgr 命令。

版本升级吧

::

        cephu@cephadmin:~/my-cluster$ pip install ceph-deploy
        Collecting ceph-deploy
        Downloading ceph-deploy-1.5.39.tar.gz (114kB)
        100% |████████████████████████████████| 122kB 292kB/s
        Collecting setuptools (from ceph-deploy)
        Downloading setuptools-36.6.0-py2.py3-none-any.whl (481kB)
        100% |████████████████████████████████| 481kB 968kB/s
        Building wheels for collected packages: ceph-deploy
        Running setup.py bdist_wheel for ceph-deploy ... done
        Stored in directory: /home/cephu/.cache/pip/wheels/5e/4a/c5/5759b04fedf1eaa17d4453b562ab28a2142dbf93ced0c37e5d
        Successfully built ceph-deploy
        Installing collected packages: setuptools, ceph-deploy
        Successfully installed ceph-deploy-1.5.32 setuptools-20.7.0
        You are using pip version 8.1.1, however version 9.0.1 is available.
        You should consider upgrading via the 'pip install --upgrade pip' command.
        cephu@cephadmin:~/my-cluster$ ceph-deploy --version
        1.5.39
        cephu@cephadmin:~/my-cluster$ ceph-deploy -h

这下有了。重新来一下

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mgr create node1
        [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
        [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /usr/bin/ceph-deploy mgr create node1
        [ceph_deploy.cli][INFO  ] ceph-deploy options:
        [ceph_deploy.cli][INFO  ]  username                      : None
        [ceph_deploy.cli][INFO  ]  verbose                       : False
        [ceph_deploy.cli][INFO  ]  mgr                           : [('node1', 'node1')]
        [ceph_deploy.cli][INFO  ]  overwrite_conf                : False
        [ceph_deploy.cli][INFO  ]  subcommand                    : create
        [ceph_deploy.cli][INFO  ]  quiet                         : False
        [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7fa24a321ab8>
        [ceph_deploy.cli][INFO  ]  cluster                       : ceph
        [ceph_deploy.cli][INFO  ]  func                          : <function mgr at 0x7fa24a993578>
        [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
        [ceph_deploy.cli][INFO  ]  default_release               : False
        [ceph_deploy.mgr][DEBUG ] Deploying mgr, cluster ceph hosts node1:node1
        [ceph_deploy][ERROR ] RuntimeError: bootstrap-mgr keyring not found; run 'gatherkeys'

哇，又来错误。 

提示了 run 'gatherkeys' 。

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy gatherkeys mon1
        [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
        [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /usr/bin/ceph-deploy gatherkeys mon1
        [ceph_deploy.cli][INFO  ] ceph-deploy options:
        [ceph_deploy.cli][INFO  ]  username                      : None
        [ceph_deploy.cli][INFO  ]  verbose                       : False
        [ceph_deploy.cli][INFO  ]  overwrite_conf                : False
        [ceph_deploy.cli][INFO  ]  quiet                         : False
        [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7fbfda5f8a70>
        [ceph_deploy.cli][INFO  ]  cluster                       : ceph
        [ceph_deploy.cli][INFO  ]  mon                           : ['mon1']
        [ceph_deploy.cli][INFO  ]  func                          : <function gatherkeys at 0x7fbfda8570c8>
        [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
        [ceph_deploy.cli][INFO  ]  default_release               : False
        [ceph_deploy.gatherkeys][INFO  ] Storing keys in temp directory /tmp/tmpZ8THzr
        [mon1][DEBUG ] connection detected need for sudo
        [mon1][DEBUG ] connected to host: mon1
        [mon1][DEBUG ] detect platform information from remote host
        [mon1][DEBUG ] detect machine type
        [mon1][DEBUG ] get remote short hostname
        [mon1][DEBUG ] fetch remote file
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --admin-daemon=/var/run/ceph/ceph-mon.mon1.asok mon_status
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-mon1/keyring auth get client.admin
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-mon1/keyring auth get client.bootstrap-mds
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-mon1/keyring auth get client.bootstrap-mgr
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-mon1/keyring auth get-or-create client.bootstrap-mgr mon allow profile bootstrap-mgr
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-mon1/keyring auth get client.bootstrap-osd
        [mon1][INFO  ] Running command: sudo /usr/bin/ceph --connect-timeout=25 --cluster=ceph --name mon. --keyring=/var/lib/ceph/mon/ceph-mon1/keyring auth get client.bootstrap-rgw
        [ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.client.admin.keyring' already exists
        [ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.bootstrap-mds.keyring' already exists
        [ceph_deploy.gatherkeys][INFO  ] Storing ceph.bootstrap-mgr.keyring
        [ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.mon.keyring' already exists
        [ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.bootstrap-osd.keyring' already exists
        [ceph_deploy.gatherkeys][INFO  ] keyring 'ceph.bootstrap-rgw.keyring' already exists
        [ceph_deploy.gatherkeys][INFO  ] Destroy temp directory /tmp/tmpZ8THzr
        cephu@cephadmin:~/my-cluster$ 

再走一个

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mgr create node1
        [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
        [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /usr/bin/ceph-deploy mgr create node1
        [ceph_deploy.cli][INFO  ] ceph-deploy options:
        [ceph_deploy.cli][INFO  ]  username                      : None
        [ceph_deploy.cli][INFO  ]  verbose                       : False
        [ceph_deploy.cli][INFO  ]  mgr                           : [('node1', 'node1')]
        [ceph_deploy.cli][INFO  ]  overwrite_conf                : False
        [ceph_deploy.cli][INFO  ]  subcommand                    : create
        [ceph_deploy.cli][INFO  ]  quiet                         : False
        [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7f5d02a1bab8>
        [ceph_deploy.cli][INFO  ]  cluster                       : ceph
        [ceph_deploy.cli][INFO  ]  func                          : <function mgr at 0x7f5d0308d578>
        [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
        [ceph_deploy.cli][INFO  ]  default_release               : False
        [ceph_deploy.mgr][DEBUG ] Deploying mgr, cluster ceph hosts node1:node1
        [node1][DEBUG ] connection detected need for sudo
        [node1][DEBUG ] connected to host: node1
        [node1][DEBUG ] detect platform information from remote host
        [node1][DEBUG ] detect machine type
        [ceph_deploy.mgr][INFO  ] Distro info: Ubuntu 16.04 xenial
        [ceph_deploy.mgr][DEBUG ] remote host will use systemd
        [ceph_deploy.mgr][DEBUG ] deploying mgr bootstrap to node1
        [node1][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        [ceph_deploy.mgr][ERROR ] RuntimeError: config file /etc/ceph/ceph.conf exists with different content; use --overwrite-conf to overwrite
        [ceph_deploy][ERROR ] GenericError: Failed to create 1 MGRs

提示了 use --overwrite-conf 

再来

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy --overwrite-conf mgr create mon1
        [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
        [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /usr/bin/ceph-deploy --overwrite-conf mgr create mon1
        [ceph_deploy.cli][INFO  ] ceph-deploy options:
        [ceph_deploy.cli][INFO  ]  username                      : None
        [ceph_deploy.cli][INFO  ]  verbose                       : False
        [ceph_deploy.cli][INFO  ]  mgr                           : [('mon1', 'mon1')]
        [ceph_deploy.cli][INFO  ]  overwrite_conf                : True
        [ceph_deploy.cli][INFO  ]  subcommand                    : create
        [ceph_deploy.cli][INFO  ]  quiet                         : False
        [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7fe415166ab8>
        [ceph_deploy.cli][INFO  ]  cluster                       : ceph
        [ceph_deploy.cli][INFO  ]  func                          : <function mgr at 0x7fe4157d8578>
        [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
        [ceph_deploy.cli][INFO  ]  default_release               : False
        [ceph_deploy.mgr][DEBUG ] Deploying mgr, cluster ceph hosts mon1:mon1
        [mon1][DEBUG ] connection detected need for sudo
        [mon1][DEBUG ] connected to host: mon1
        [mon1][DEBUG ] detect platform information from remote host
        [mon1][DEBUG ] detect machine type
        [ceph_deploy.mgr][INFO  ] Distro info: Ubuntu 16.04 xenial
        [ceph_deploy.mgr][DEBUG ] remote host will use systemd
        [ceph_deploy.mgr][DEBUG ] deploying mgr bootstrap to mon1
        [mon1][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        [mon1][WARNIN] mgr keyring does not exist yet, creating one
        [mon1][DEBUG ] create a keyring file
        [mon1][DEBUG ] create path if it doesn't exist
        [mon1][INFO  ] Running command: sudo ceph --cluster ceph --name client.bootstrap-mgr --keyring /var/lib/ceph/bootstrap-mgr/ceph.keyring auth get-or-create mgr.mon1 mon allow profile mgr osd allow * mds allow * -o /var/lib/ceph/mgr/ceph-mon1/keyring
        [mon1][INFO  ] Running command: sudo systemctl enable ceph-mgr@mon1
        [mon1][WARNIN] Created symlink from /etc/systemd/system/ceph-mgr.target.wants/ceph-mgr@mon1.service to /lib/systemd/system/ceph-mgr@.service.
        [mon1][INFO  ] Running command: sudo systemctl start ceph-mgr@mon1
        [mon1][INFO  ] Running command: sudo systemctl enable ceph.target
        cephu@cephadmin:~/my-cluster$ 

终于成功了。

3.3

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy --overwrite-conf osd create node1:sdb node2:sdb node3:sdc
        ...
        [node3][DEBUG ] Warning: The kernel is still using the old partition table.
        [node3][DEBUG ] The new table will be used at the next reboot or after you
        [node3][DEBUG ] run partprobe(8) or kpartx(8)
        [node3][DEBUG ] The operation has completed successfully.
        [node3][WARNIN] update_partition: Calling partprobe on prepared device /dev/sdc
        [node3][WARNIN] command_check_call: Running command: /sbin/udevadm settle --timeout=600
        [node3][WARNIN] command: Running command: /usr/bin/flock -s /dev/sdc /sbin/partprobe /dev/sdc
        [node3][WARNIN] command_check_call: Running command: /sbin/udevadm settle --timeout=600
        [node3][WARNIN] command_check_call: Running command: /sbin/udevadm trigger --action=add --sysname-match sdc1
        [node3][INFO  ] Running command: sudo systemctl enable ceph.target
        [node3][INFO  ] checking OSD status...
        [node3][DEBUG ] find the location of an executable
        [node3][INFO  ] Running command: sudo /usr/bin/ceph --cluster=ceph osd stat --format=json
        [node3][WARNIN] there is 1 OSD down
        [node3][WARNIN] there is 1 OSD out
        [ceph_deploy.osd][DEBUG ] Host node3 is now ready for osd use.
        cephu@cephadmin:~/my-cluster$ 

成功

3.4

::

        cephu@cephadmin:~/my-cluster$ ssh node1 sudo ceph health
        HEALTH_OK
        cephu@cephadmin:~/my-cluster$ ssh node2 sudo ceph health
        HEALTH_OK
        cephu@cephadmin:~/my-cluster$ ssh node3 sudo ceph health
        HEALTH_OK
        cephu@cephadmin:~/my-cluster$ ssh node3 sudo ceph -s
        cluster:
        id:     d0aa5af1-4f8e-4953-9448-7f1b2448b8a5
        health: HEALTH_OK

        services:
        mon: 1 daemons, quorum mon1
        mgr: mon1(active)
        osd: 3 osds: 3 up, 3 in

        data:
        pools:   0 pools, 0 pgs
        objects: 0 objects, 0 bytes
        usage:   3164 MB used, 80500 MB / 83664 MB avail
        pgs:

        cephu@cephadmin:~/my-cluster$

到这里，我们应该是完成了基础的搭建


附加项：expanding your cluster
============================================

ADD A METADATA SERVER
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ceph-deploy mds create node1

ADDING MONITORS
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

ceph-deploy mon add node2 node3 会报错

换成：

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mon add cephfsn2
        cephu@cephadmin:~/my-cluster$ ceph-deploy mon add cephfsn3

验证

::

        cephu@cephadmin:~/my-cluster$ ssh cephfsn2 ceph quorum_status --format json-pretty
        2017-10-24 10:40:24.959942 7f261141a700 -1 auth: unable to find a keyring on /etc/ceph/ceph.client.admin.keyring,/etc/ceph/ceph.keyring,/etc/ceph/keyring,/etc/ceph/keyring.bin,: (2) No such file or directory
        2017-10-24 10:40:24.959973 7f261141a700 -1 monclient: ERROR: missing keyring, cannot use cephx for authentication
        2017-10-24 10:40:24.959975 7f261141a700  0 librados: client.admin initialization error (2) No such file or directory
        [errno 2] error connecting to the cluster
        cephu@cephadmin:~/my-cluster$ ssh cephfsn2 sudo ceph quorum_status --format json-pretty

        {
        "election_epoch": 14,
        "quorum": [
                0,
                1,
                2
        ],
        "quorum_names": [
                "cephfsn2",
                "mon1",
                "cephfsn3"
        ],
        "quorum_leader_name": "cephfsn2",
        "monmap": {
                "epoch": 3,
                "fsid": "d0aa5af1-4f8e-4953-9448-7f1b2448b8a5",
                "modified": "2017-10-24 10:32:19.273831",
                "created": "2017-10-23 15:22:32.766470",
                "features": {
                "persistent": [
                        "kraken",
                        "luminous"
                ],
                "optional": []
                },
                "mons": [
                {
                        "rank": 0,
                        "name": "cephfsn2",
                        "addr": "192.168.31.113:6789/0",
                        "public_addr": "192.168.31.113:6789/0"
                },
                {
                        "rank": 1,
                        "name": "mon1",
                        "addr": "192.168.31.114:6789/0",
                        "public_addr": "192.168.31.114:6789/0"
                },
                {
                        "rank": 2,
                        "name": "cephfsn3",
                        "addr": "192.168.31.173:6789/0",
                        "public_addr": "192.168.31.173:6789/0"
                }
                ]
        }
        }
        cephu@cephadmin:~/my-cluster$

ADDING MANAGERS
^^^^^^^^^^^^^^^^^^^^

::

        cephu@cephadmin:~/my-cluster$ ceph-deploy mgr create node2 node3
        [ceph_deploy.conf][DEBUG ] found configuration file at: /home/cephu/.cephdeploy.conf
        [ceph_deploy.cli][INFO  ] Invoked (1.5.39): /usr/bin/ceph-deploy mgr create node2 node3
        [ceph_deploy.cli][INFO  ] ceph-deploy options:
        [ceph_deploy.cli][INFO  ]  username                      : None
        [ceph_deploy.cli][INFO  ]  verbose                       : False
        [ceph_deploy.cli][INFO  ]  mgr                           : [('node2', 'node2'), ('node3', 'node3')]
        [ceph_deploy.cli][INFO  ]  overwrite_conf                : False
        [ceph_deploy.cli][INFO  ]  subcommand                    : create
        [ceph_deploy.cli][INFO  ]  quiet                         : False
        [ceph_deploy.cli][INFO  ]  cd_conf                       : <ceph_deploy.conf.cephdeploy.Conf instance at 0x7fb9043a4ab8>
        [ceph_deploy.cli][INFO  ]  cluster                       : ceph
        [ceph_deploy.cli][INFO  ]  func                          : <function mgr at 0x7fb904a16578>
        [ceph_deploy.cli][INFO  ]  ceph_conf                     : None
        [ceph_deploy.cli][INFO  ]  default_release               : False
        [ceph_deploy.mgr][DEBUG ] Deploying mgr, cluster ceph hosts node2:node2 node3:node3
        [node2][DEBUG ] connection detected need for sudo
        [node2][DEBUG ] connected to host: node2
        [node2][DEBUG ] detect platform information from remote host
        [node2][DEBUG ] detect machine type
        [ceph_deploy.mgr][INFO  ] Distro info: Ubuntu 16.04 xenial
        [ceph_deploy.mgr][DEBUG ] remote host will use systemd
        [ceph_deploy.mgr][DEBUG ] deploying mgr bootstrap to node2
        [node2][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        [node2][WARNIN] mgr keyring does not exist yet, creating one
        [node2][DEBUG ] create a keyring file
        [node2][DEBUG ] create path if it doesn't exist
        [node2][INFO  ] Running command: sudo ceph --cluster ceph --name client.bootstrap-mgr --keyring /var/lib/ceph/bootstrap-mgr/ceph.keyring auth get-or-create mgr.node2 mon allow profile mgr osd allow * mds allow * -o /var/lib/ceph/mgr/ceph-node2/keyring
        [node2][INFO  ] Running command: sudo systemctl enable ceph-mgr@node2
        [node2][WARNIN] Created symlink from /etc/systemd/system/ceph-mgr.target.wants/ceph-mgr@node2.service to /lib/systemd/system/ceph-mgr@.service.
        [node2][INFO  ] Running command: sudo systemctl start ceph-mgr@node2
        [node2][INFO  ] Running command: sudo systemctl enable ceph.target
        [node3][DEBUG ] connection detected need for sudo
        [node3][DEBUG ] connected to host: node3
        [node3][DEBUG ] detect platform information from remote host
        [node3][DEBUG ] detect machine type
        [ceph_deploy.mgr][INFO  ] Distro info: Ubuntu 16.04 xenial
        [ceph_deploy.mgr][DEBUG ] remote host will use systemd
        [ceph_deploy.mgr][DEBUG ] deploying mgr bootstrap to node3
        [node3][DEBUG ] write cluster configuration to /etc/ceph/{cluster}.conf
        [node3][WARNIN] mgr keyring does not exist yet, creating one
        [node3][DEBUG ] create a keyring file
        [node3][DEBUG ] create path if it doesn't exist
        [node3][INFO  ] Running command: sudo ceph --cluster ceph --name client.bootstrap-mgr --keyring /var/lib/ceph/bootstrap-mgr/ceph.keyring auth get-or-create mgr.node3 mon allow profile mgr osd allow * mds allow * -o /var/lib/ceph/mgr/ceph-node3/keyring
        [node3][INFO  ] Running command: sudo systemctl enable ceph-mgr@node3
        [node3][WARNIN] Created symlink from /etc/systemd/system/ceph-mgr.target.wants/ceph-mgr@node3.service to /lib/systemd/system/ceph-mgr@.service.
        [node3][INFO  ] Running command: sudo systemctl start ceph-mgr@node3
        [node3][INFO  ] Running command: sudo systemctl enable ceph.target
        cephu@cephadmin:~/my-cluster$

验证

::

        cephu@cephadmin:~/my-cluster$ ssh node1 sudo ceph -s
        cluster:
        id:     d0aa5af1-4f8e-4953-9448-7f1b2448b8a5
        health: HEALTH_WARN
                clock skew detected on mon.cephfsn3

        services:
        mon: 3 daemons, quorum cephfsn2,mon1,cephfsn3
        mgr: mon1(active), standbys: node2, node3
        osd: 3 osds: 3 up, 3 in

        data:
        pools:   0 pools, 0 pgs
        objects: 0 objects, 0 bytes
        usage:   3164 MB used, 80500 MB / 83664 MB avail
        pgs:

        cephu@cephadmin:~/my-cluster$ ssh node3 sudo ceph -s
        cluster:
        id:     d0aa5af1-4f8e-4953-9448-7f1b2448b8a5
        health: HEALTH_WARN
                clock skew detected on mon.cephfsn3

        services:
        mon: 3 daemons, quorum cephfsn2,mon1,cephfsn3
        mgr: mon1(active), standbys: node2, node3
        osd: 3 osds: 3 up, 3 in

        data:
        pools:   0 pools, 0 pgs
        objects: 0 objects, 0 bytes
        usage:   3164 MB used, 80500 MB / 83664 MB avail
        pgs:

        cephu@cephadmin:~/my-cluster$


ADD AN RGW INSTANCE
^^^^^^^^^^^^^^^^^^^^^^^

::

        ceph-deploy rgw create node1

好了。

STORING/RETRIEVING OBJECT DATA
===================================

这一个小节，现在先不动。

