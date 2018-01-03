==============================
ruby 2.4 install by rpm
==============================

ruby
^^^^

相关 sh 脚本，可见 https://gitee.com/tomt/tom_ruby_install_by_rpm

去 源 http://mirrors.aliyun.com/centos/7/os/x86_64/Packages/ 下载 必要的包 

::

    [jlch@check ruby]$ cat t.sh 
    #!/bin/bash
    for rpm_packages in `cat packages.txt`
    do
        echo $rpm_packages
        wget http://mirrors.aliyun.com/centos/7/os/x86_64/Packages/$rpm_packages
    done
    [jlch@check ruby]$ cat packages.txt 
    libyaml-0.1.4-11.el7_0.x86_64.rpm
    ruby-2.0.0.648-30.el7.x86_64.rpm
    ruby-libs-2.0.0.648-30.el7.x86_64.rpm
    rubygem-io-console-0.4.2-30.el7.x86_64.rpm
    rubygem-rdoc-4.0.0-30.el7.noarch.rpm
    rubygem-bigdecimal-1.2.0-30.el7.x86_64.rpm
    rubygem-psych-2.0.0-30.el7.x86_64.rpm
    rubygems-2.0.14.1-30.el7.noarch.rpm
    ruby-irb-2.0.0.648-30.el7.noarch.rpm
    rubygem-json-1.7.7-30.el7.x86_64.rpm
    [jlch@check ruby]$ 


安装 ruby, gem

::

	rpm -ivh libyaml-0.1.4-11.el7_0.x86_64.rpm
	rpm -ivh ruby-2.0.0.648-30.el7.x86_64.rpm ruby-libs-2.0.0.648-30.el7.x86_64.rpm rubygem-io-console-0.4.2-30.el7.x86_64.rpm rubygem-rdoc-4.0.0-30.el7.noarch.rpm rubygem-bigdecimal-1.2.0-30.el7.x86_64.rpm rubygem-psych-2.0.0-30.el7.x86_64.rpm rubygems-2.0.14.1-30.el7.noarch.rpm ruby-irb-2.0.0.648-30.el7.noarch.rpm rubygem-json-1.7.7-30.el7.x86_64.rpm


加载 环境变量

::

    [root@localhost rh-ruby24]# find / -name ruby
    [root@localhost rh-ruby24]# pwd
    /opt/rh/rh-ruby24
    [root@localhost rh-ruby24]# ls
    enable  root
    [root@localhost rh-ruby24]# cat /opt/rh/rh-ruby24/enable >> /etc/profile
    [root@localhost rh-ruby24]# source /etc/profile

验证

::

	which ruby  # 这个时候, 可能还是没有 ruby的, 因为,没有加载 环境变量
	ruby help
	ruby --help
	ruby gems ## 这个本身就不能运行的。
	gem 
	gem list --local
	ruby -v

::

    [root@localhost rh-ruby24]# ruby --help

卸载 ruby 

::

    [root@localhost ruby-install-by-rpm]# cat rm.sh
    rpm -ef rubygem-io-console-0.4.2-30.el7.x86_64 rubygem-psych-2.0.0-30.el7.x86_64 rubygems-2.0.14.1-30.el7.noarch ruby-irb-2.0.0.648-30.el7.noarch ruby-libs-2.0.0.648-30.el7.x86_64 rubygem-bigdecimal-1.2.0-30.el7.x86_64 rubygem-json-1.7.7-30.el7.x86_64 ruby-2.0.0.648-30.el7.x86_64 rubygem-rdoc-4.0.0-30.el7.noarch



---

draft:

中间，为了环境变量，有一次用过

::

    export RUBYLIB=/opt/jlch/ruby-2.4.2/lib/
