# 1. Install nginx
```
apt install nginx apache2-utils -y
```
# 2. Create standart nginx config
```
cat << EOF > /etc/nginx/nginx.conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
worker_rlimit_nofile 100000;

events {
        worker_connections 4000;
        use epoll;
        multi_accept on;
}

http {
    # basic settings
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
        open_file_cache_valid 30s;
        open_file_cache_min_uses 2;
        open_file_cache_errors on;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

        # ssl settings
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;

    # websockets config
    map $http_upgrade $connection_upgrade {
            default upgrade;
            '' close;
        }

    # logging settings
    access_log off;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # gzip settings
    gzip on;
        gzip_min_length 10240;
        gzip_comp_level 1;
        gzip_vary on;
        gzip_disable msie6;
        gzip_proxied expired no-cache no-store private auth;
        gzip_types
        text/css
        text/javascript
        text/xml
        text/plain
        text/x-component
        application/javascript
        application/x-javascript
        application/json
    application/xml
        application/rss+xml
    application/atom+xml
        font/truetype
        font/opentype
        application/vnd.ms-fontobject
        image/svg+xml;
        reset_timedout_connection on;
        client_body_timeout 10;
        send_timeout 2;
        keepalive_requests 100000;

        # virtual host configs
    include /etc/nginx/conf.d/loki.conf;
}
EOF
```
# 3. Create loki config
```
cat << EOF > /etc/nginx/conf.d/loki.conf
upstream loki {
  server 127.0.0.1:3100;
  keepalive 15;
}

server {
  listen 80;
  #server_name loki.localdns.xyz;

  auth_basic "loki auth";
  auth_basic_user_file /etc/nginx/passwords;

  location /test {
    proxy_read_timeout 1800s;
    proxy_connect_timeout 1600s;
    proxy_pass http://loki;
    proxy_http_version 1.1;
    proxy_set_header X-Scope-OrgID test;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_set_header Connection "Keep-Alive";
    proxy_set_header Proxy-Connection "Keep-Alive";
    proxy_redirect off;
  }
  location / {
    proxy_read_timeout 1800s;
    proxy_connect_timeout 1600s;
    proxy_pass http://loki;
    proxy_http_version 1.1;
    #proxy_set_header X-Scope-OrgID test;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_set_header Connection "Keep-Alive";
    proxy_set_header Proxy-Connection "Keep-Alive";
    proxy_redirect off;
  }
}
EOF
```
# 4. Create auth_basic_user_file 
```
htpasswd -c /etc/nginx/passwords user
```
# 5. Start/Enable service nginx
```
systemctl daemon-reload
systemctl enable nginx
systemctl start nginx
systemctl status nginx
```