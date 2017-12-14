=================================================
optimize-a-mysqldump-of-a-large-database
=================================================


https://dba.stackexchange.com/questions/20/how-can-i-optimize-a-mysqldump-of-a-large-database



mysqldump -h... -u... -p... --hex-blob --routines --triggers --all-databases | gzip > MySQLData.sql.gz

