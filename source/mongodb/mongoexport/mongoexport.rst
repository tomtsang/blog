

==========================
mongoexport
==========================

::

    [tom@check memi]$ cat field_KLine_1Min_b.txt 
    _id
    Type
    Sign
    ID
    TrdDt
    TrdTm
    PrevClsPx
    LastPx
    TM
    [tom@check memi]$ cat signals_1min_to_csv.sh 
    #!/bin/bash

    # mongoexport -h 192.168.31.240 -d openQuoteTmp -c Signals_1Min -q "{TrdDt:20171101}" --fieldFile=field_KLine_1Min_b.txt --type=csv -o Signals_1Min_csv/20171101.csv
    for dt in `cat dt_signals_1min.txt`
    do
        echo "$dt"
        mongoexport -h 192.168.31.240 -d openQuoteTmp -c Signals_1Min -q "{TrdDt:$dt}" --fieldFile=field_KLine_1Min_b.txt --type=csv -o Signals_1Min_csv/$dt.csv
    done

    echo "game over"
    [tom@check memi]$ 



draft
=============

::

    [tom@check memi]$ mongoexport -h 192.168.31.240 -d openQuote20171106  -c Signals_1Min -q "{TrdDt:20171107}" --fieldFile=field_KLine_1Min_b.txt --type=csv -o Signals_1Min_csv/20171107.csv
    2017-11-07T15:29:15.446+0800	connected to: 192.168.31.240
    2017-11-07T15:29:19.423+0800	exported 92421 records
    [tom@check memi]$ mongoexport -h 192.168.31.240 -d openQuote20171106  -c Signals_1Min -q "{TrdDt:20171106}" --fieldFile=field_KLine_1Min_b.txt --type=csv -o Signals_1Min_csv/20171106.csv
    2017-11-07T15:29:39.203+0800	connected to: 192.168.31.240
    2017-11-07T15:29:42.738+0800	exported 82241 records
    [tom@check memi]$ 

如果说要转移到共享盘，则

::

    sudo cp crontab/memi/Signals_1Min_csv/20171107.csv /misc/share/Y-运维组/data/tmp/Signals_1Min_csv/

