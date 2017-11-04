

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