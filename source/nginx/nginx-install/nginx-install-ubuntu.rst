=========================
nginx install ubuntu
=========================

ubuntu16.04 install ubuntu

:: 

    cd 
    wget http://nginx.org/keys/nginx_signing.key
    sudo apt-key add nginx_signing.key

    jlch@ceph-client:~$ cat /etc/apt/sources.list.d/nginx.list
    deb http://nginx.org/packages/debian/ xenial nginx
    deb-src http://nginx.org/packages/debian/ xenial nginx

    echo "deb http://nginx.org/packages/debian/ xenial nginx
    deb-src http://nginx.org/packages/debian/ xenial nginx" > /etc/apt/sources.list.d/nginx.list 

    sudo apt-get update 
    sudo apt install nginx -y