==========================================
通过 wget 获得本机IP
==========================================


有的时候需要知道自己的通过哪个外网IP出去

env
==========================================

与 中泰张锐 远程, 发现对方机器访问不了我们的服务, 那么, 想通过我们的机器来抓取中泰发过来的数据包, 那么就需要中泰过来的IP.

step
==========================================

方法有下列几种:

* 远程ssh登陆, 看ssh日志得IP
* 访问服务, 通过其它日志(防火墙, nginx日志)得到IP
* 通过访问其它服务(如 返回IP服务), 得IP

现在我们介绍 "通过访问其它服务" 的方法

因为我们正常访问 http://ip.chinaz.com/ 可以直接得到我们自己的外网IP, 所以, 我们可以在机器上 wget 得到这个IP.


::

    [tom@jlch_web_001 ~]$ wget ip.chinaz.com
    --2018-01-16 15:12:07--  http://ip.chinaz.com/
    Resolving ip.chinaz.com (ip.chinaz.com)... 122.228.242.37, 117.25.139.23
    Connecting to ip.chinaz.com (ip.chinaz.com)|122.228.242.37|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 33569 (33K) [text/html]
    Saving to: ‘index.html.4’

    100%[==================================================================================================================================================================>] 33,569       178KB/s   in 0.2s

    2018-01-16 15:12:07 (178 KB/s) - ‘index.html.4’ saved [33569/33569]

    [tom@jlch_web_001 ~]$ cat index.html.4  | grep fz24
                    <p class="getlist pl10"><span>您来自：</span>120.25.204.216 	<span class="pl10">所在区域：</span>浙江省杭州市 阿里云BGP数据中心<a href="http://tool.chinaz.com/contact" target="_blank" class="col-blue02 pl5">(纠错)</a></p>
            <dd class="fz24">120.25.204.216</dd>
                <p class="info"><span>© CopyRight 2002-2018, CHINAZ.COM, Inc.All Rights Reserved.</span><span>闽ICP备08105208号</span><span>增值电信业务经营许可证闽B2-20120007号</span><a href="http://www.wy.cn" rel="nofollow" target="_blank" class="col-gray02">服务器资源由唯一网络赞助</a></p>
    [tom@jlch_web_001 ~]$

完美, 这样我们就得到了IP.
