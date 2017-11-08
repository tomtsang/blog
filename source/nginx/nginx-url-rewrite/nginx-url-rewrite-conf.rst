
===========================
nginx-url-rewrite-conf
===========================

先放一个示例。

::

    server {
        listen       5112;
        server_name  factorapi;
        #charset koi8-r;
        access_log  /var/log/nginx/log/5111.access.log  main;
        rewrite_log on;
        charset utf-8;
        charset_types text/html application/json;
        location / {
            root   /usr/share/nginx/html/rewrite;
            index  index.html index.htm;
            # rewrite /a.json /b.json permanent;
            rewrite /a.json /b.json ;
        }

        location /factor-api {
            proxy_set_header        Host            $host;
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;

            # 如果参数中有 type=xx 则使用该值
            if ($args ~* type=(\d+?)(&|$)) {
                set $type $1;
            }
            # 如果参数中有 type=xx 则使用该值
            if ($args ~* cycle=(\d+?)(&|$)) {
                set $cycle $1;
            }

            # 取 request_uri 中的数据
            if ( $request_uri ~* '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' ) {
                set $stockcode $1;
            }

            if (-e /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json){
                rewrite ^/(.*)$ /data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json last;
            }
            proxy_pass http://192.168.31.181:3062/;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

上面这个示例，是参考 https://stackoverflow.com/questions/43284560/nginx-try-files-rewrite-if-exists-proxy-if-doesnt 而得来的。比较不错。

::

    server {
        listen       5112;
        server_name  factorapi;
        #charset koi8-r;
        access_log  /var/log/nginx/log/5111.access.log  main;
        rewrite_log on;
        charset utf-8;
        charset_types text/html application/json;
        location / {
            root   /usr/share/nginx/html/rewrite;
            index  index.html index.htm;
            # rewrite /a.json /b.json permanent;
            rewrite /a.json /b.json ;
        }
        location /factor-api {
            proxy_set_header        Host            $host;
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;

            # 如果参数中有 type=xx 则使用该值
            if ($args ~* type=(\d+?)(&|$)) {
                set $type $1;
            }
            # 如果参数中有 type=xx 则使用该值
            if ($args ~* cycle=(\d+?)(&|$)) {
                set $cycle $1;
            }
            # 取 request_uri 中的数据
            if ( $request_uri ~* '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' ) {
                set $stockcode $1;
            }
            rewrite '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' /data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json ; # 注意，这里不能用 _$cycle ，所以只能用 .$cycle 来连接了。

            if (!-e /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json ) {
                # return 400; 
                rewrite ^/(.+)$ /index.html last;
            }

            proxy_pass http://192.168.31.181:3062/; # 基本上这条规则，已失效了。
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

下面是 try_files, 

注意，try_files 还不能 与 proxy_pass 配合使用。

而且，在没有完全弄明白 try_files 之前，还是先不要使用了。

::


    server {
        listen       5112;
        server_name  factorapi;
        #charset koi8-r;
        access_log  /var/log/nginx/log/5111.access.log  main;
        rewrite_log on;
        charset utf-8;
        charset_types text/html application/json;
        location / {
            root   /usr/share/nginx/html/rewrite;
            index  index.html index.htm;
            # rewrite /a.json /b.json permanent;
            rewrite /a.json /b.json ;
        }

        location /factor-api {
            proxy_set_header        Host            $host;
            proxy_set_header        X-Real-IP       $remote_addr;
            proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;

            # 如果参数中有 type=xx 则使用该值
            if ($args ~* type=(\d+?)(&|$)) {
                set $type $1;
            }
            # 如果参数中有 type=xx 则使用该值
            if ($args ~* cycle=(\d+?)(&|$)) {
                set $cycle $1;
            }

            if ( $request_uri ~* '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' ) {
                set $stockcode $1;
            }
            
            try_files /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json @qwe;
            proxy_pass http://192.168.31.181:3062/; # 基本上这条规则，已失效了。
        }

        location @qwe { 
            rewrite ^/(.+)$ /index.html last; 
        }

            # try_files /data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json $request_uri;
            # try_files "http://192.168.31.181:5112/data/factor/20171107/600006.SH/c.json" $request_uri;
            # try_files /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json $request_uri;
        # }
        # location @qwe {
        #     proxy_set_header        Host            $host;
        #     proxy_set_header        X-Real-IP       $remote_addr;
        #     proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for; 
        #     proxy_pass http://192.168.31.181:3062/; # 基本上这条规则，已失效了。
        # }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

上面是 精简版本 测试的过程中，有下面这些

测试 正则表达式

        # ------- not ok -----------
        # rewrite ^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis /data/factor/20171107/$1/c.json permanent; # 这里有 大括号，要用 单引号 引起来。
        # ------ ok ---------
        # rewrite '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' /data/factor/20171107/$1/c.json permanent;
        # rewrite ^/factor-api\/stocks\/([1-9]\d*\.S[H|Z])\/factor_analysis /data/factor/20171107/$1/c.json permanent;
        # rewrite ^/factor-api\/stocks\/(.*)\/factor_analysis  /data/factor/20171107/$1/c.json permanent;
        # rewrite ^/factor-api\/stocks\/(\d*\.S[H|Z])\/factor_analysis /data/factor/20171107/$1/c.json permanent;

        # --------- if ok -----------------
        # rewrite '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' /data/factor/20171107/$1/c.json ;
        # set $stockcode $1;
        # if (!-e /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/c.json ) {
        #     return 400; 
        # }

        # rewrite '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' /data/factor/20171107/$1/factor_analysis_type_1_cycle_2.json ;
        # set $stockcode $1;
        # if (!-e /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis_type_1_cycle_2.json ) {
        #     return 400; 
        # }

        # rewrite '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' /data/factor/20171107/$1/$cycle.json ;
        # set $stockcode $1;
        # if (!-e /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis_type_1_cycle_2.json ) {
        #     return 400; 
        # }

    if ( $request_uri ~* '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' ) {
        set $stockcode $1; 
    }
    try_files /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json @qwe;
#        rewrite '^/factor-api\/stocks\/(\d{6}\.S[H|Z])\/factor_analysis' /data/factor/20171107/$1/factor_analysis.type.$type.cycle.$cycle.json ; # 注意，这里不能用 _$cycle ，所以只能用 .$cycle 来连接了>。
#        set $stockcode $1;
#        try_files /usr/share/nginx/html/rewrite/data/factor/20171107/$stockcode/factor_analysis.type.$type.cycle.$cycle.json @qwe;
    }

    location @qwe {
        rewrite ^/(.+)$ /index.html last;
        #proxy_pass http://192.168.31.181:3062/; # 基本上这条规则，已失效了。
    }
