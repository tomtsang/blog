=================
ping ip
=================

通过ping, 实现快速测试IP是否被占用
=================================

::

    #!/bin/bash

    # 通过ping, 实现快速测试IP是否被占用
    for siteip in $(seq 1 254)
    do
        site="192.168.1.${siteip}"
        ping -c1 -W1 ${site} &> /dev/null
        if [ "$?" == "0" ]; then
            echo "$site is UP"
        else
            echo "$site is DOWN"
        fi
    done