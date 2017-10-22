

===========================
docker-registry
===========================

docker registry

::

    在 192.168.31.240 ， 10.10.12.17 中建立了 registry

    输入用户zimug，密码zimug_password


192.168.31.171 client

root 用户

::

    sudo mkdir -p /etc/docker/certs.d/reg.jlch.com:5000
    sudo echo 192.168.31.240 reg.jlch.com  >> /etc/hosts

    sudo scp -r tom@192.168.31.240:/home/tom/registry/certs/registry.crt /etc/docker/certs.d/reg.jlch.com:5000

::

    root@k-m:/home/jlch# docker login reg.jlch.com:5000
    Username: zimug
    Password:
    Login Succeeded
    root@k-m:/home/jlch# docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    ceph/daemon         latest              dc9781d1d530        20 hours ago        983MB
    hello-world         latest              1815c82652c0        4 months ago        1.84kB
    root@k-m:/home/jlch# docker tag ceph/daemon reg.jlch.com:5000/ceph/daemon:latest
    root@k-m:/home/jlch# docker push reg.jlch.com:5000/ceph/daemon:latest
