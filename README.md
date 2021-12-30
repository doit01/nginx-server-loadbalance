
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {

     client_max_body_size 10m;
    ssl_session_cache   shared:SSL:10m;
    ssl_session_timeout 10m;
    proxy_connect_timeout       5m;
    proxy_send_timeout          5m;
    proxy_read_timeout          5m;
    send_timeout                5m;
    keepalive_timeout    5m;
    fastcgi_read_timeout 5m;

    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
     upstream test_server_pools {
       ip_hash;
        server  localhost:811 ;
         server  localhost:812 ;
  
}


    upstream prod_server_pools {
    ip_hash;
        server  localhost:821 ;
         server  localhost:822 ;
          server  localhost:823 ;
}


    server {
        listen       81;
        server_name  test_server_pools;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
        location /downloadfromdesk/ {
            root   C:/Users/bi_zhansheng/Downloads/downloadfromdesk/;
            autoindex on;
        
        }
        location /zip/ {
            alias C:/Users/bi_zhansheng/Downloads/zip/;
             autoindex on;
        }
        location /ziptest/ {
            alias C:/Users/bi_zhansheng/Downloads/ziptest/;
             autoindex on;
        }

         location ^~/py/ {
               access_log logs/test_access.log;
               proxy_pass   http://test_server_pools/;
            # proxy_pass   http://127.0.0.1:5000/;
        }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    # prod
 server {
        listen       82;
        server_name  prod_server_pools;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }
           
        location /zip/ {
            alias C:/Users/bi_zhansheng/Downloads/zipprod/;
             autoindex on;
        }

         location ^~/py/ {
             access_log logs/prod_access.log;
            proxy_pass   http://prod_server_pools/;

            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $remote_addr;
            proxy_redirect default;
            proxy_buffer_size 512k;
            proxy_buffers 6 512k;
            proxy_busy_buffers_size 512k;
            proxy_temp_file_write_size 512k;
            client_max_body_size 100m;
        }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
