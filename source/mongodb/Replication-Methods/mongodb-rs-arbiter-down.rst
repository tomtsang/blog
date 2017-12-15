=========================
mongodb rs arbiter down
=========================

env
=========================

10.10.13.12 master
10.10.13.11 slave
10.10.13.101 arbiter
10.10.12.18 arbiter


step
=========================


先 remote 掉 arbiter 节点, 然后
Adds a new arbiter to an existing replica set.

::

    mongo 10.10.13.12/test
    use admin;
    rs.status()
    rs.remove("10.10.13.101:27017")
    rs.addArb("10.10.12.18:27017")
    rs.status()



    