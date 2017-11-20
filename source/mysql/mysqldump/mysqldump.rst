==========



mysqldump --all-databases  --no-data > test.dump

jlch
=========

必须本机完成

10.10.13.100


::

    [jlch@mysql1 ~]$ mysqldump -u root -p --all-databases  --no-data > 10.10.13.100.mariadb.dump
    Enter password:
    [jlch@mysql1 ~]$

jlch@192.168.31.181 完成


192.168.31.181.mariadb.dump  
192.168.31.240.mariadb.dump
10.10.12.13.mariadb.dump  

::

    [jlch@check ~]$ mysqldump -h 10.10.12.13 -u fish -p --all-databases  --no-data > 10.10.12.13.mariadb.dump
    Enter password:
    [jlch@check ~]$




