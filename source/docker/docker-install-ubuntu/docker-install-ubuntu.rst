================================================
docker-install-ubuntu
================================================


https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-docker-ce-1

    root@km:~# sudo apt-get update
    ...
    Ign:56 http://cn.archive.ubuntu.com/ubuntu xenial-backports/multiverse Translation-en_US
    Fetched 3,967 kB in 37min 32s (1,761 B/s)
    Reading package lists... Done
    W: The repository 'http://cn.archive.ubuntu.com/ubuntu xenial-updates Release' does not have a Release file.
    N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
    N: See apt-secure(8) manpage for repository creation and user configuration details.
    W: The repository 'http://cn.archive.ubuntu.com/ubuntu xenial-backports Release' does not have a Release file.
    N: Data from such a repository can't be authenticated and is therefore potentially dangerous to use.
    N: See apt-secure(8) manpage for repository creation and user configuration details.
    root@km:~#  apt-cache madison docker-ce
    docker-ce | 17.09.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.06.2~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.06.1~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.06.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.03.2~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.03.1~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.03.0~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    root@km:~# sudo apt-get install docker-ce=v17.03 
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    E: Version 'v17.03' for 'docker-ce' was not found
    root@km:~# apt-cache madison docker-ce
    docker-ce | 17.09.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.06.2~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.06.1~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.06.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.03.2~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.03.1~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    docker-ce | 17.03.0~ce-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
    root@km:~# sudo apt-get install docker-ce=17.03.2~ce-0~ubuntu-xenial -y
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following additional packages will be installed:
    aufs-tools cgroupfs-mount libltdl7
    Suggested packages:
    mountall
    The following NEW packages will be installed:
    aufs-tools cgroupfs-mount docker-ce libltdl7
    0 upgraded, 4 newly installed, 0 to remove and 22 not upgraded.
    Need to get 38.3 kB/19.3 MB of archives.
    After this operation, 89.4 MB of additional disk space will be used.
    Get:1 http://cn.archive.ubuntu.com/ubuntu xenial/main amd64 libltdl7 amd64 2.4.6-0.1 [38.3 kB]
    Fetched 12.5 kB in 0s (446 kB/s)     
    Selecting previously unselected package aufs-tools.
    (Reading database ... 106140 files and directories currently installed.)
    Preparing to unpack .../aufs-tools_1%3a3.2+20130722-1.1ubuntu1_amd64.deb ...
    Unpacking aufs-tools (1:3.2+20130722-1.1ubuntu1) ...
    Selecting previously unselected package cgroupfs-mount.
    Preparing to unpack .../cgroupfs-mount_1.2_all.deb ...
    Unpacking cgroupfs-mount (1.2) ...
    Selecting previously unselected package libltdl7:amd64.
    Preparing to unpack .../libltdl7_2.4.6-0.1_amd64.deb ...
    Unpacking libltdl7:amd64 (2.4.6-0.1) ...
    Selecting previously unselected package docker-ce.
    Preparing to unpack .../docker-ce_17.03.2~ce-0~ubuntu-xenial_amd64.deb ...
    Unpacking docker-ce (17.03.2~ce-0~ubuntu-xenial) ...
    Processing triggers for libc-bin (2.23-0ubuntu9) ...
    Processing triggers for man-db (2.7.5-1) ...
    Processing triggers for ureadahead (0.100.0-19) ...
    Processing triggers for systemd (229-4ubuntu21) ...
    Setting up aufs-tools (1:3.2+20130722-1.1ubuntu1) ...
    Setting up cgroupfs-mount (1.2) ...
    Setting up libltdl7:amd64 (2.4.6-0.1) ...
    Setting up docker-ce (17.03.2~ce-0~ubuntu-xenial) ...
    Installing new version of config file /etc/init.d/docker ...
    Processing triggers for libc-bin (2.23-0ubuntu9) ...
    Processing triggers for systemd (229-4ubuntu21) ...
    Processing triggers for ureadahead (0.100.0-19) ...
    root@km:~# sudo docker run hello-world

    Hello from Docker!
    This message shows that your installation appears to be working correctly.

    To generate this message, Docker took the following steps:
    1. The Docker client contacted the Docker daemon.
    2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    3. The Docker daemon created a new container from that image which runs the
        executable that produces the output you are currently reading.
    4. The Docker daemon streamed that output to the Docker client, which sent it
        to your terminal.

    To try something more ambitious, you can run an Ubuntu container with:
    $ docker run -it ubuntu bash

    Share images, automate workflows, and more with a free Docker ID:
    https://cloud.docker.com/

    For more examples and ideas, visit:
    https://docs.docker.com/engine/userguide/

    root@km:~# docker ps
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    root@km:~# docker ps -a
    CONTAINER ID        IMAGE                                        COMMAND                  CREATED             STATUS                         PORTS                               NAMES
    e27a404beed2        hello-world                                  "/hello"                 8 seconds ago       Exited (0) 8 seconds ago                                        hopeful_hodgkin
    root@km:~# 
