=============================
mysql use
=============================

::

    mysql -h 192.168.31.181 -u root -p stock -e "select * from baidu_20_quar ;"  -N -s | sed -e 's/^/"/g;s/$/"\n/g' > ./baidu_20_quar.csv
    ## ok
    mysql -h 192.168.31.181 -u root -p stock -e "select * from baidu_20_quar ;" | sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g' > ./baidu_20_quar.csv

    mysql -h 192.168.31.181 -u root -p stock -ss -e "select * from baidu_20_quar ;" | sed 's/\t/","/g;s/^/"/;s/$/"/;s/\n//g' > ./2.csv


    mysql -h 192.168.31.181 -u root -p stock -ss -e "select * from baidu_20_quar ;" > ./3.csv


    ##

    mysql -h 192.168.31.181 -u root -p stock --execute='SELECT `FIELD`, `FIELD` FROM `TABLE` LIMIT 0, 10000 ' -X > file.csv

    ## 

    mysqldump -h 192.168.31.181 -u root -p  -t -T /home/tom/draft/mysql-20170619 stock baidu_20_quar --fields-terminated-by=',' 

    mysqldump -h 192.168.31.181 -u root -p -T/path/to/directory dbname table_name --fields-terminated-by=',' 

    ## 

    SELECT * FROM mytable  INTO OUTFILE '/tmp/mytable.csv'  FIELDS TERMINATED BY ','  OPTIONALLY ENCLOSED BY '"'  LINES TERMINATED BY '\n';


    mysqldump -h 192.168.31.181 -u root -p  stock czs_dxg --tab=/home/tom/draft2/ --fields-terminated-by=','


    select * from table into outfile '/tmp/table.csv' fields terminated by ',' optionally enclosed by '"' lines terminated by '\r\n';

    ##

    mysqldump -u root -p haitong factor_info | mysql -h 192.168.31.240 test

    mysqldump -u root -p haitong factor_info | mysql -h 192.168.31.240 -u root -p test

    mysqldump -u root -p -T/tmp/ test factor_info --fields-terminated-by=',' 

