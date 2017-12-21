===========================================
linux 技巧：使用 screen 管理你的远程会话
===========================================



https://www.ibm.com/developerworks/cn/linux/l-cn-screen/

::

    [jlch@udvbct-ekl-a ~]$ ps -xj
    PPID   PID  PGID   SID TTY      TPGID STAT   UID   TIME COMMAND
    8217  8219  8217  8217 ?           -1 S     1000   0:00 sshd: jlch@pts/5
    8219  8220  8220  8220 pts/5     8278 Ss    1000   0:00 -bash
    8220  8278  8278  8220 pts/5     8278 R+    1000   0:00 ps -xj
    [jlch@udvbct-ekl-a ~]$

ps -xj 可以看到 这些个  PPID, PID, PGID, SID, TTY, TPGID 这些概念.