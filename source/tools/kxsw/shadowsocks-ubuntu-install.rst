====================================
ubuntu install shadowsocks
====================================

http://abc.36fy.com/knowledgebase/9/Linux.html


本文将以Ubuntu系统为例，介绍下Linux系统设置使用加速度帐号教程；
更多linux操作系统使用教程请参考：
https://github.com/shadowsocks/shadowsocks-qt5/wiki
第1步：下载安装客户端shadowsocks-qt5：
客户端需要通过PPA源安装，仅支持Ubuntu 14.04或更高版本；
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
安装过程如果遇到 libappindicator1 依赖问题（dependency problems），而 libappindicator1 又遇到 libindicator7 依赖的解决办法。一并安装 libappindicator1 libindicator7 依赖，再重新安装shadowsocks-qt5；
sudo apt-get-f install libappindicator1 libindicator7

第2步：配置客户端：
安装完成后，我们可以在应用程序里面找到 ShadowSocks-Qt5客户端，启动，在弹出的界面空白处右键add添加配置你的帐号信息，如下图：

配置方法与windows客户端配置是差不多的；
Server Address填写服务器ip地址；Server Port填写服务端口；Password填写连接密码；Encryption Method选择AES-256-CFB，其他选项均保持默认不变。
说明：服务器IP地址、服务端口、连接密码信息，购买了加速度帐号后由加速度提供！
注：有朋友反映安装后不知如何启动客户端，你可以直接在用命令行启动客户端：
sudo ss-qt5

或者试试直接win键呼出应用搜索，输入Shadowsocks就出来了。
第3步：配置下浏览器代理：
以大家常用的浏览器为例：
谷歌浏览器
有2种方案，我们更推荐方案2，
方案1：
a：打开“局域网LAN设置”选项（即：打开浏览器，依次点击设置 ，显示高级设置 ，更改代理服务器设置，在弹出的Internet选项窗口中，依次点击连接，局域网设置，弹出局域网LAN设置）；
b：在弹出的“局域网LAN设置”选项中，代理服务器的复选框打勾，设置地址：127.0.0.1，设置端口：1080，点击确定；
完成上面的a，b步骤，我们就可以安全愉快的上网了；
方案2：
方案1只能使用全局模式，没有pac模式，如果要实现pac模式和全局模式的切换，你可以使用谷歌浏览器+扩展的方法，具体教程请查看：
http://abc.36fy.com/knowledgebase/22/plus.html，这篇教程是windows下的教程，但和linux使用教程是完全一样的，自行下载linux系统的chrome浏览器即可，扩展插件是通用的。
firefox 浏览器：
有2种方案，我们同样推荐方案2，
方案1：
a：打开firefox浏览器代理连接设置选项（即：打开 firefox 浏览器，依次点击 编辑 ，首选项 ，高级 ，网络 ，设置）；
b：在打开的窗口中选择【手动配置代理】，默认有个http代理，这个一定要清空，在 socks主机中填写 127.0.0.1 ，端口填写1080，选择socks v5， 然后点击确定，如下图：



完成上面的a，b步骤，我们就可以安全愉快的上网了；
方案2：
方案1只能使用全局模式，没有pac模式，如果要实现pac模式和全局模式的切换，你可以使用Firefox+扩展的方法，具体教程请查看：
http://abc.36fy.com/knowledgebase/38/Firefoxplus.html，这篇教程是windows下的教程，但和linux使用教程是完全一样的，自行下载linux系统的Firefox浏览器即可，扩展插件是通用的。
其他浏览器
与谷歌浏览器的方案1是一样的：
打开“局域网LAN设置”选项，在弹出的“局域网LAN设置”选项中，代理服务器的复选框打勾，设置地址：127.0.0.1，设置端口：1080，点击确定即可，仅支持全局模式。

