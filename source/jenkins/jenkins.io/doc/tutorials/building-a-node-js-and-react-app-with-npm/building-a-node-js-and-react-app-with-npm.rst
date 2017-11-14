

==================================================
using-jenkins-to-build-a-java-maven-project
==================================================

reference
=====================

https://jenkins.io/doc/tutorials/building-a-node-js-and-react-app-with-npm/

env
=====================

192.168.31.240

api-cm.zhangtl.com:8011

step
=====================

::

    docker pull jenkinsci/blueocean
    docker volume create jenkins-data-nodejs

    cd /home/tom/jenkins/test/nodejs/ # my workspace
    mkdir -p ./home/GitHub/
    cd ./home/GitHub/
    git clone https://github.com/tomtsang/simple-node-js-react-npm-app

    docker run \
    --rm \
    -u root \
    -p 8012:8080 \
    -v jenkins-data-nodejs:/var/jenkins_home \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /home/tom/jenkins/test/nodejs/home:/home \
    jenkinsci/blueocean

    # 输出一大串了
    # ...

    *************************************************************

    Jenkins initial setup is required. An admin user has been created and a password generated.
    Please use the following password to proceed to installation:

    f4509cc0cf6543b6a85896fcad11c7ad

    This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

    *************************************************************
    *************************************************************
    *************************************************************

    Oct 23, 2017 2:42:14 AM hudson.model.UpdateSite updateData

后面直接按照官方文档进行.

成功的.


