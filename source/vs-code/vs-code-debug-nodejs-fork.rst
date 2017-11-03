=============================
vs code 调试 nodejs fork
=============================


如 OpenX/OpenQuote2/tick_signaller.js 中，用了 fork 子进程。这个时候要使用 如下的方式。


首次调试
------------

父进程加参数
^^^^^^^^^^^^

::

	this._procChild = cp.fork(__dirname + this.TSFile);
	// this._procChild = cp.fork(__dirname + this.TSFile, [], {execArgv: ['--debug=5859']});

启动父进程
^^^^^^^^^^^^^

然后 终端上 node tick_signaller.js SZ

launch.json添加配置
^^^^^^^^^^^^^^^^^^^^^

然后，在 vs code 上, 打开 launch.json 后选择，调试/添加配置/  ，选择 "nodejs: 附加", 然后在 添加配置

::
    "configurations": [
        {
            "type": "node",
            "request": "attach",
            "name": "Attach",
            "port": 5859  # 这里的 5859 必须是上面的 --debug=5859 一致。
        },

F5 调试
^^^^^^^^^^^^^^^^^^^^^

然后 F5 调试吧。


再次调试
------------

调试线束后，再次调试需要先开 父进程。

