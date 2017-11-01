


======================================
集群客户端命令（redis-cli -c -p port）
======================================

http://www.cnblogs.com/gossip/p/5993922.html


集群

::

    cluster info ：打印集群的信息
    cluster nodes ：列出集群当前已知的所有节点（ node），以及这些节点的相关信息。

节点

::

    cluster meet <ip> <port> ：将 ip 和 port 所指定的节点添加到集群当中，让它成为集群的一份子。
    cluster forget <node_id> ：从集群中移除 node_id 指定的节点。
    cluster replicate <node_id> ：将当前节点设置为 node_id 指定的节点的从节点。
    cluster saveconfig ：将节点的配置文件保存到硬盘里面。

槽(slot)

::

    cluster addslots <slot> [slot ...] ：将一个或多个槽（ slot）指派（ assign）给当前节点。
    cluster delslots <slot> [slot ...] ：移除一个或多个槽对当前节点的指派。
    cluster flushslots ：移除指派给当前节点的所有槽，让当前节点变成一个没有指派任何槽的节点。
    cluster setslot <slot> node <node_id> ：将槽 slot 指派给 node_id 指定的节点，如果槽已经指派给另一个节点，那么先让另一个节点删除该槽>，然后再进行指派。
    cluster setslot <slot> migrating <node_id> ：将本节点的槽 slot 迁移到 node_id 指定的节点中。
    cluster setslot <slot> importing <node_id> ：从 node_id 指定的节点中导入槽 slot 到本节点。
    cluster setslot <slot> stable ：取消对槽 slot 的导入（ import）或者迁移（ migrate）。

键

::

    cluster keyslot <key> ：计算键 key 应该被放置在哪个槽上。
    cluster countkeysinslot <slot> ：返回槽 slot 目前包含的键值对数量。
    cluster getkeysinslot <slot> <count> ：返回 count 个 slot 槽中的键  

