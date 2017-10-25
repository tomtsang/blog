==================================
Block Device Quick Start
==================================

env
========

192.168.31.172 ceph-client

修改 hostname 为 ceph-client

INSTALL CEPH
=============

admin 节点：

::

    ceph-deploy install ceph-client
    ceph-deploy admin ceph-client

ceph-client 节点：

::

    sudo chmod +r /etc/ceph/ceph.client.admin.keyring

CREATE A BLOCK DEVICE POOL
===========================

admin 节点：

原来在 admin 节点，是没有这个 rbd 命令的。

要通过 sudo apt install ceph-common 之后，才会有。所以先运行一下。

::

    cephu@cephadmin:~/my-cluster$ sudo apt install ceph-common -y
    cephu@cephadmin:~/my-cluster$ rbd pool init jlch
    2017-10-24 15:57:45.951917 7fde4fa6e0c0 -1 auth: unable to find a keyring on /etc/ceph/ceph.client.admin.keyring,/etc/ceph/ceph.keyring,/etc/ceph/keyring,/etc/ceph/keyring.bin,: (2) No such file or directory
    2017-10-24 15:57:45.951937 7fde4fa6e0c0 -1 monclient: ERROR: missing keyring, cannot use cephx for authentication
    2017-10-24 15:57:45.951942 7fde4fa6e0c0  0 librados: client.admin initialization error (2) No such file or directory
    rbd: couldn't connect to the cluster!
    cephu@cephadmin:~/my-cluster$

ceph-client 节点：

::

    jlch@k-m:/etc/apt/sources.list.d$ sudo ls /etc/ceph/* -l
    [sudo] password for jlch:
    -rw-r--r-- 1 root root  63 Oct 24 15:32 /etc/ceph/ceph.client.admin.keyring
    -rw-r--r-- 1 root root 249 Oct 24 15:32 /etc/ceph/ceph.conf
    -rw-r--r-- 1 root root  92 Apr 21  2017 /etc/ceph/rbdmap
    -rw------- 1 root root   0 Oct 24 11:20 /etc/ceph/tmp2IJh4C
    jlch@k-m:/etc/apt/sources.list.d$

我去，明明有的，为什么说 unable to find a keyring on *******，哪里出的问题?

哈哈，这个问题的原因找到了。需要把 上一小节的 最后一个部分，执行一下。

而且，要按上一小节的方式，osd create 一个 pool 出来，才能在这里 pool init .

比如之前就是创建了 pool mytest. 这里就是 

::

    rbd pool init mytest

CONFIGURE A BLOCK DEVICE
===========================

On the ceph-client node

::

    cephu@ceph-client:~$ rbd create mytest --size 4096 -m mon1 -k /etc/ceph/ceph.client.admin.keyring
    rbd: error opening default pool 'rbd'
    Ensure that the default pool has been created or specify an alternate pool name.
    cephu@ceph-client:~$ 

报错，因为，这里说了，opening default pool 'rbd' 出错，为啥？因为之前没有建立过 pool rbd 呀，之前，只有 pool mytest 呀。
怎么办？2个方法。1）重新新建立一个 pool rbd, 2）指向到已建立的pool, 如 pool mytest 。

我们走 方法2.

方法1

回 ceph-admin 节点：

:: 

    cephu@cephadmin:~/my-cluster$ ceph osd pool create rbd 8
    pool 'rbd' created
    cephu@cephadmin:~/my-cluster$ rbd pool init rbd
    cephu@cephadmin:~/my-cluster$

回 ceph-client 节点：

::

    cephu@ceph-client:~$ rbd create foo --size 4096 -m mon1 -k /etc/ceph/ceph.client.admin.keyring 
    

方法2

::

    cephu@ceph-client:~$ rbd help create # 这里会发现是加 -p 参数来指定 pool
    cephu@ceph-client:~$ rbd create foo --size 4096 -m mon1 -k /etc/ceph/ceph.client.admin.keyring -p mytest
    cephu@ceph-client:~$


好了，可以走下一步了。
我们继续。


On the ceph-client node, map the image to a block device.

::

    cephu@ceph-client:~$ sudo rbd map foo --image client.admin -m mon1  -p mytest
    rbd: sysfs write failed
    rbd: error opening image client.admin: (2) No such file or directory
    In some cases useful info is found in syslog - try "dmesg | tail".
    rbd: map failed: (110) Connection timed out
    cephu@ceph-client:~$

报错了。

::

    cephu@ceph-client:~$ dmesg | tail -n 100
    ...
    [692522.117250] libceph: mon0 192.168.31.114:6789 missing required protocol features
    [692532.096436] libceph: mon0 192.168.31.114:6789 feature set mismatch, my 106b84a842a42 < server's 40106b84a842a42, missing 400000000000000
    [692532.099897] libceph: mon0 192.168.31.114:6789 missing required protocol features
    [692542.111938] libceph: mon0 192.168.31.114:6789 feature set mismatch, my 106b84a842a42 < server's 40106b84a842a42, missing 400000000000000
    [692542.115603] libceph: mon0 192.168.31.114:6789 missing required protocol features


http://www.hl10502.com/2017/08/01/ceph-rbdmap-error-1/

上面有一些详细的原理

又要农总出马了。

http://blog.csdn.net/lk142500/article/details/78275910

这里指出了解决方法。来吧。

admin 节点

::

    cephu@cephadmin:~/my-cluster$ ceph -v
    ceph version 12.2.1 (3e7492b9ada8bdc9a5cd0feafd42fbca27f9c38e) luminous (stable)
    cephu@cephadmin:~/my-cluster$ ceph osd crush tunables optimal
    adjusted tunables profile to optimal
    cephu@cephadmin:~/my-cluster$ ceph osd crush rule ls
    replicated_rule
    cephu@cephadmin:~/my-cluster$ ceph osd crush rule dump
    [
        {
            "rule_id": 0,
            "rule_name": "replicated_rule",
            "ruleset": 0,
            "type": 1,
            "min_size": 1,
            "max_size": 10,
            "steps": [
                {
                    "op": "take",
                    "item": -1,
                    "item_name": "default"
                },
                {
                    "op": "chooseleaf_firstn",
                    "num": 0,
                    "type": "host"
                },
                {
                    "op": "emit"
                }
            ]
        }
    ]

    cephu@cephadmin:~/my-cluster$ ceph osd crush show-tunables
    {
        "choose_local_tries": 0,
        "choose_local_fallback_tries": 0,
        "choose_total_tries": 50,
        "chooseleaf_descend_once": 1,
        "chooseleaf_vary_r": 1,
        "chooseleaf_stable": 1,
        "straw_calc_version": 1,
        "allowed_bucket_algs": 54,
        "profile": "jewel",
        "optimal_tunables": 1,
        "legacy_tunables": 0,
        "minimum_required_version": "jewel",
        "require_feature_tunables": 1,
        "require_feature_tunables2": 1,
        "has_v2_rules": 0,
        "require_feature_tunables3": 1,
        "has_v3_rules": 0,
        "has_v4_buckets": 1,
        "require_feature_tunables5": 1,
        "has_v5_rules": 0
    }

    cephu@cephadmin:~/my-cluster$ ceph osd crush -h

    General usage:
    ==============
    usage: ceph [-h] [-c CEPHCONF] [-i INPUT_FILE] [-o OUTPUT_FILE]
                [--id CLIENT_ID] [--name CLIENT_NAME] [--cluster CLUSTER]
                [--admin-daemon ADMIN_SOCKET] [-s] [-w] [--watch-debug]
                [--watch-info] [--watch-sec] [--watch-warn] [--watch-error]
                [--watch-channel WATCH_CHANNEL] [--version] [--verbose]
                [--concise] [-f {json,json-pretty,xml,xml-pretty,plain}]
                [--connect-timeout CLUSTER_TIMEOUT]

    Ceph administration tool

    optional arguments:
    -h, --help            request mon help
    -c CEPHCONF, --conf CEPHCONF
                            ceph configuration file
    -i INPUT_FILE, --in-file INPUT_FILE
                            input file, or "-" for stdin
    -o OUTPUT_FILE, --out-file OUTPUT_FILE
                            output file, or "-" for stdout
    --id CLIENT_ID, --user CLIENT_ID
                            client id for authentication
    --name CLIENT_NAME, -n CLIENT_NAME
                            client name for authentication
    --cluster CLUSTER     cluster name
    --admin-daemon ADMIN_SOCKET
                            submit admin-socket commands ("help" for help
    -s, --status          show cluster status
    -w, --watch           watch live cluster changes
    --watch-debug         watch debug events
    --watch-info          watch info events
    --watch-sec           watch security events
    --watch-warn          watch warn events
    --watch-error         watch error events
    --watch-channel WATCH_CHANNEL
                            which log channel to follow when using -w/--watch. One
                            of ['cluster', 'audit', '*'
    --version, -v         display version
    --verbose             make verbose
    --concise             make less verbose
    -f {json,json-pretty,xml,xml-pretty,plain}, --format {json,json-pretty,xml,xml-pretty,plain}
    --connect-timeout CLUSTER_TIMEOUT
                            set a timeout for connecting to the cluster

    Local commands:
    ===============

    ping <mon.id>           Send simple presence/life test to a mon
                            <mon.id> may be 'mon.*' for all mons
    daemon {type.id|path} <cmd>
                            Same as --admin-daemon, but auto-find admin socket
    daemonperf {type.id | path} [stat-pats] [priority] [<interval>] [<count>]
    daemonperf {type.id | path} list|ls [stat-pats] [priority]
                            Get selected perf stats from daemon/admin socket
                            Optional shell-glob comma-delim match string stat-pats
                            Optional selection priority (can abbreviate name):
                            critical, interesting, useful, noninteresting, debug
                            List shows a table of all available stats
                            Run <count> times (default forever),
                            once per <interval> seconds (default 1)


    Monitor commands:
    =================
    osd crush add <osdname (id|osd.id)> <float[0.0-]> <args> [<args>...]                                                 add or update crushmap position and weight for <name> with <weight> and location <args>
    osd crush add-bucket <name> <type>                                                                                   add no-parent (probably root) crush bucket <name> of type <type>
    osd crush class ls                                                                                                   list all crush device classes
    osd crush class ls-osd <class>                                                                                       list all osds belonging to the specific <class>
    osd crush class rename <srcname> <dstname>                                                                           rename crush device class <srcname> to <dstname>
    osd crush create-or-move <osdname (id|osd.id)> <float[0.0-]> <args> [<args>...]                                      create entry or move existing entry for <name> <weight> at/to location <args>
    osd crush dump                                                                                                       dump crush map
    osd crush get-tunable straw_calc_version                                                                             get crush tunable <tunable>
    osd crush link <name> <args> [<args>...]                                                                             link existing entry for <name> under location <args>
    osd crush ls <node>                                                                                                  list items beneath a node in the CRUSH tree
    osd crush move <name> <args> [<args>...]                                                                             move existing entry for <name> to location <args>
    osd crush remove <name> {<ancestor>}                                                                                 remove <name> from crush map (everywhere, or just at <ancestor>)
    osd crush rename-bucket <srcname> <dstname>                                                                          rename bucket <srcname> to <dstname>
    osd crush reweight <name> <float[0.0-]>                                                                              change <name>'s weight to <weight> in crush map
    osd crush reweight-all                                                                                               recalculate the weights for the tree to ensure they sum correctly
    osd crush reweight-subtree <name> <float[0.0-]>                                                                      change all leaf items beneath <name> to <weight> in crush map
    osd crush rm <name> {<ancestor>}                                                                                     remove <name> from crush map (everywhere, or just at <ancestor>)
    osd crush rm-device-class <ids> [<ids>...]                                                                           remove class of the osd(s) <id> [<id>...],or use <all|any|*> to remove all.
    osd crush rule create-erasure <name> {<profile>}                                                                     create crush rule <name> for erasure coded pool created with <profile> (default default)
    osd crush rule create-replicated <name> <root> <type> {<class>}                                                      create crush rule <name> for replicated pool to start from <root>, replicate across buckets of type <type>, using a
                                                                                                                        choose mode of <firstn|indep> (default firstn; indep best for erasure pools)
    osd crush rule create-simple <name> <root> <type> {firstn|indep}                                                     create crush rule <name> to start from <root>, replicate across buckets of type <type>, using a choose mode of
                                                                                                                        <firstn|indep> (default firstn; indep best for erasure pools)
    osd crush rule dump {<name>}                                                                                         dump crush rule <name> (default all)
    osd crush rule ls                                                                                                    list crush rules
    osd crush rule ls-by-class <class>                                                                                   list all crush rules that reference the same <class>
    osd crush rule rename <srcname> <dstname>                                                                            rename crush rule <srcname> to <dstname>
    osd crush rule rm <name>                                                                                             remove crush rule <name>
    osd crush set <osdname (id|osd.id)> <float[0.0-]> <args> [<args>...]                                                 update crushmap position and weight for <name> to <weight> with location <args>
    osd crush set {<int>}                                                                                                set crush map from input file
    osd crush set-device-class <class> <ids> [<ids>...]                                                                  set the <class> of the osd(s) <id> [<id>...],or use <all|any|*> to set all.
    osd crush set-tunable straw_calc_version <int>                                                                       set crush tunable <tunable> to <value>
    osd crush show-tunables                                                                                              show current crush tunables
    osd crush swap-bucket <source> <dest> {--yes-i-really-mean-it}                                                       swap existing bucket contents from (orphan) bucket <source> and <target>
    osd crush tree {--show-shadow}                                                                                       dump crush buckets and items in a tree view
    osd crush tunables legacy|argonaut|bobtail|firefly|hammer|jewel|optimal|default                                      set crush tunables values to <profile>
    osd crush unlink <name> {<ancestor>}                                                                                 unlink <name> from crush map (everywhere, or just at <ancestor>)
    osd crush weight-set create <poolname> flat|positional                                                               create a weight-set for a given pool
    osd crush weight-set create-compat                                                                                   create a default backward-compatible weight-set
    osd crush weight-set dump                                                                                            dump crush weight sets
    osd crush weight-set ls                                                                                              list crush weight sets
    osd crush weight-set reweight <poolname> <item> <float[0.0-]> [<float[0.0-]>...]                                     set weight for an item (bucket or osd) in a pool's weight-set
    osd crush weight-set reweight-compat <item> <float[0.0-]> [<float[0.0-]>...]                                         set weight for an item (bucket or osd) in the backward-compatible weight-set
    osd crush weight-set rm <poolname>                                                                                   remove the weight-set for a given pool
    osd crush weight-set rm-compat                                                                                       remove the backward-compatible weight-set
    cephu@cephadmin:~/my-cluster$ ceph osd crush tunables hammer
    adjusted tunables profile to hammer
    cephu@cephadmin:~/my-cluster$ ceph osd crush show-tunables
    {
        "choose_local_tries": 0,
        "choose_local_fallback_tries": 0,
        "choose_total_tries": 50,
        "chooseleaf_descend_once": 1,
        "chooseleaf_vary_r": 1,
        "chooseleaf_stable": 0,
        "straw_calc_version": 1,
        "allowed_bucket_algs": 54,
        "profile": "hammer",
        "optimal_tunables": 0,
        "legacy_tunables": 0,
        "minimum_required_version": "hammer",
        "require_feature_tunables": 1,
        "require_feature_tunables2": 1,
        "has_v2_rules": 0,
        "require_feature_tunables3": 1,
        "has_v3_rules": 0,
        "has_v4_buckets": 1,
        "require_feature_tunables5": 0,
        "has_v5_rules": 0
    }

    cephu@cephadmin:~/my-cluster$ 

回 ceph-client 节点：

::

    cephu@ceph-client:~$ ceph -v
    ceph version 12.2.1 (3e7492b9ada8bdc9a5cd0feafd42fbca27f9c38e) luminous (stable)
    cephu@ceph-client:~$ rbd ls
    foo
    cephu@ceph-client:~$ sudo rbd map foo --name client.admin
    /dev/rbd0
    cephu@ceph-client:~$ ls /dev/rbd
    rbd/  rbd0
    cephu@ceph-client:~$ ls /dev/rbd/rbd/foo
    /dev/rbd/rbd/foo
    cephu@ceph-client:~$ ls /dev/rbd0
    /dev/rbd0
    cephu@ceph-client:~$ ls /dev/rbd0 -l
    brw-rw---- 1 root disk 251, 0 Oct 25 12:03 /dev/rbd0
    cephu@ceph-client:~$ 


成功了。。继续。。

Use the block device by creating a file system on the ceph-client node.

::

    cephu@ceph-client:~$ sudo mkfs.ext4 -m0 /dev/rbd/rbd/foo
    mke2fs 1.42.13 (17-May-2015)
    Discarding device blocks: done
    Creating filesystem with 1048576 4k blocks and 262144 inodes
    Filesystem UUID: d83ebc8d-1956-4d81-b9db-391f939634ac
    Superblock backups stored on blocks:
            32768, 98304, 163840, 229376, 294912, 819200, 884736

    Allocating group tables: done
    Writing inode tables: done
    Creating journal (32768 blocks): done
    Writing superblocks and filesystem accounting information: done

    cephu@ceph-client:~$ 

Mount the file system on the ceph-client node.

::

    cephu@ceph-client:~$ sudo mkdir /mnt/ceph-block-device
    cephu@ceph-client:~$ sudo mount /dev/rbd/rbd/foo /mnt/ceph-block-device
    cephu@ceph-client:~$ cd /mnt/ceph-block-device
    cephu@ceph-client:/mnt/ceph-block-device$ ls
    lost+found
    cephu@ceph-client:/mnt/ceph-block-device$ 




