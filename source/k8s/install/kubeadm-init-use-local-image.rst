================================================
kubeadm-init-use-local-image
================================================

因为 kubeadm 安装 要提前下载好docker images, 并使用 这些个 docker images.
但是，我们公司的网络FQ的下载速度太慢，时不时会断了。所以，我们考虑使用本地下载好的这些 images。

要使用 local images, 那就要去修改 kubeadm 的代码，并重新build。好吧，我们build 吧。


env
====================================

::

    192.168.31.120 km master
    192.168.31.119 kn1 node
    192.168.31.118 kn2 node

kubeadm-build
==================================================

见 kubeadm-build.rst


加代理
==================================================

::

    root@km:/etc/kubernetes/manifests# export
    declare -x HOME="/root"
    declare -x LANG="en_US.UTF-8"
    declare -x LANGUAGE="en_US:en"
    declare -x LESSCLOSE="/usr/bin/lesspipe %s %s"
    declare -x LESSOPEN="| /usr/bin/lesspipe %s"
    declare -x LOGNAME="root"
    declare -x LS_COLORS="rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.Z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.jpg=01;35:*.jpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:"
    declare -x MAIL="/var/mail/root"
    declare -x NO_PROXY="localhost,127.0.0.1/8,192.168.31.1/24"
    declare -x OLDPWD="/etc/kubernetes"
    declare -x PATH="/home/jlch/.ana/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
    declare -x PWD="/etc/kubernetes/manifests"
    declare -x SHELL="/bin/bash"
    declare -x SHLVL="1"
    declare -x SUDO_COMMAND="/bin/su"
    declare -x SUDO_GID="1000"
    declare -x SUDO_UID="1000"
    declare -x SUDO_USER="jlch"
    declare -x TERM="xterm"
    declare -x USER="root"
    declare -x USERNAME="root"
    declare -x http_proxy="http://192.168.31.10:1080"
    declare -x https_proxy="http://192.168.31.10:1080"
    declare -x no_proxy="localhost,127.0.0.1,192.168.31.120,192.168.0.0/16,10.96.0.0/16,github.com"


kubeadm init
==================================================

把这个 kubeadm 放到 km 下

::
    root@km:/etc/kubernetes/manifests# chmod a+rx /usr/bin/kubeadm
    root@km:/etc/kubernetes/manifests# kubeadm version
    kubeadm version: &version.Info{Major:"1", Minor:"8+", GitVersion:"v1.8.3-dirty", GitCommit:"f0efb3cb883751c5ffdbe6d515f3cb4fbe7b7acd", GitTreeState:"dirty", BuildDate:"2017-11-20T07:05:42Z", GoVersion:"go1.9.2", Compiler:"gc", Platform:"linux/amd64"}
    root@km:/etc/kubernetes/manifests# 
    root@km:/etc/kubernetes/manifests# kubeadm init --pod-network-cidr=10.244.0.0/16 --skip-preflight-checks 
    [kubeadm] WARNING: kubeadm is in beta, please do not use it for production clusters.
    unable to get URL "https://dl.k8s.io/release/stable-1.8.txt": Get https://dl.k8s.io/release/stable-1.8.txt: net/http: TLS handshake timeout
    root@km:/etc/kubernetes/manifests# 

不成功呀。连接上了代理，还是会 pull image


其它测试
==================================================

::

    root@km:/etc/kubernetes/manifests# kubeadm alpha phase etcd local
    unable to get URL "https://dl.k8s.io/release/stable-1.8.txt": Get https://dl.k8s.io/release/stable-1.8.txt: net/http: TLS handshake timeout
    root@km:/etc/kubernetes/manifests#


