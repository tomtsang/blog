
substitute
===========


替换

::

    grep "https://api-cm.zhangtl.com/" -rl package.json | xargs sed -i 's/https:\/\/api-cm.zhangtl.com\//http:\/\/10.10.15.181:8000\//g'

