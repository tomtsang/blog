
================================================================
Error: Cannot find module '***pm2/lib/ProcessContainerFork.js'
================================================================

报错信息类似：

::

    Error: Cannot find module '/Users/wyb/.nvm/versions/io.js/v3.0.0/lib/node_modules/pm2/lib/ProcessContainerFork.js'

参考：

https://stackoverflow.com/questions/36690442/error-cannot-find-module-users-wyb-nvm-versions-io-js-v3-0-0-lib-node-module


The reason is you pm2 env is modified, please try run command below to delete your pm2 env dir from your home dir.

::

    rm -rf ~/.pm2

Now rerun your pm2-dev server.js, pm2 will refresh env.


确实有效的。

有一次，我在给 蛟龙出海 的客户 海通证券，做 股票时光机 系统的部署时，就遇到了这个问题。

当时是，把 pm2 直接压缩过去，放在 node/lib/node_modules/ 下，然后，用 

::
    
    ln -s ***/node/lib/node_modules/pm2/bin/pm2 /usr/bin/pm2

做出来的 pm2 .

这时，只要pm2 一启动，就出错。

然后用上面的方法，解决了。
