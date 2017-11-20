==========



mysqldump --all-databases  --no-data > test.dump

jlch
=========

使用 mysqldump.sh 吧

::

    [jlch@check mariadb.dump]$ cat mysqldump.sh
    #!/bin/bash
    mysqldump -h $1 -u root -p --all-databases --no-data > $1.mariadb.dump
    [jlch@check mariadb.dump]$


必须本机完成
--------------------------------

10.10.13.100, 密码1

跑起来

::

    [jlch@mysql1 ~]$ mysqldump -u root -p --all-databases  --no-data > 10.10.13.100.mariadb.dump
    Enter password:
    [jlch@mysql1 ~]$

可在 jlch@192.168.31.181 完成
--------------------------------

::

    192.168.31.181.mariadb.dump, 密码1
    192.168.31.240.mariadb.dump, 密码1
    192.168.31.11.mariadb.dump, 密码1
    10.10.12.13.mariadb.dump, 密码1

    192.168.31.99.mariadb.dump, 密码2
    192.168.31.249.mariadb.dump, 密码2
    192.168.31.105.mariadb.dump, 密码2
    192.168.31.201.mariadb.dump, 密码2

跑起来

::

    [jlch@check ~]$ mysqldump -h 10.10.12.13 -u fish -p --all-databases  --no-data > 10.10.12.13.mariadb.dump
    Enter password:
    [jlch@check ~]$




