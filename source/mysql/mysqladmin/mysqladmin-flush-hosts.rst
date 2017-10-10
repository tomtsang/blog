
mysqladmin flush-hosts
========================

 
Database ErrorHost 'adonis.havehost.com' is blocked because of many connection errors; unblock with 'mysqladmin flush-hosts'


https://stackoverflow.com/questions/21519997/how-to-do-mysqladmin-flush-hosts-on-server

::

    mysql -u root -p -e 'flush hosts'