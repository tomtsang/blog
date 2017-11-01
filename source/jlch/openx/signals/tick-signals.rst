
==================
tick signals debug
==================

配置 config.json
====================

让 redis 指向  测试环境 192.168.31.240

回放行情
================

::

    node quote_player.js 201710311448

redis 查看效果
===================

2 个 terminal

::

    psubscribe Topics.signals.quote.1Min.S*

::

    psubscribe Topics.market.snapshot.SZ

生成信号
===============

::

    node tick-signals.js SZ

测试程序
==================

::

    node quote_test.js


