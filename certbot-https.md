维基百科介绍

> Let's Encrypt 是一个将于2015年末推出的数字证书认证机构，将通过旨在消除当前手动创建和安装证书的复杂过程的自动化流程，为安全网站提供免费的SSL/TLS证书。
> 
> Let's Encrypt 是由互联网安全研究小组（ISRG，一个公益组织）提供的服务。主要赞助商包括电子前哨基金会，Mozilla基金会，Akamai以及思科。2015年4月9日，ISRG与Linux基金会宣布合作。
> 
> 用以实现这一新的数字证书认证机构的协议被称为自动证书管理环境（ACME）。[4] GitHub上有这一规范的草案，且提案的一个版本已作为一个Internet草案发布。
> 
> Let's Encrypt 宣称这一过程将十分简单、自动化并且免费。

* * *

### 安装Let’s Encrypt(certbot)

GitHub地址：[https://github.com/certbot/certbot](https://github.com/certbot/certbot)

```
wget https://dl.eff.org/certbot-auto
chmod a+x ./certbot-auto
./certbot-auto --help
```

./certbot-auto --help 这步安装依赖环境并初始化，可能需要等待较长时间，我实际等待了1分钟。

### 获取证书

```
./certbot-auto certonly --webroot --agree-tos -v -t --email chenjiablog@gmail.com -w /home/wwwroot/chenjia.info -d chenjia.info
```

使用docker获取

```
docker run -it --rm --name certbot \
  -v /etc/letsencrypt:/etc/letsencrypt          \
  -v /var/log/letsencrypt:/var/log/letsencrypt  \
  -v /home:/home  \
 certbot/certbot certonly --webroot --agree-tos -v -t --email chenjiablog@gmail.com  \
 -w /home/wwwroot/chenjia.info -d chenjia.info
```

请根据自己的实际情况修改。

`-w /home/wwwroot/chenjia.info`

这个是你网站目录的根目录，同时你这个目录需要浏览器可以访问。


### 配置nginx

```
server
    {
        listen       80;
        server_name blog.chenjia.info;

        location / {
            return 301 https://$host$request_uri;
        }
    }

server
    {
        listen       443 ssl;
        server_name blog.chenjia.info;
        index index.html index.htm index.php;
        root /home/wwwroot/blog.chenjia.info;
        autoindex on;
        autoindex_exact_size off;
        try_files $uri $uri/ /index.php?$uri&$args;

        ssl_certificate      /etc/letsencrypt/live/blog.chenjia.info/fullchain.pem;
        ssl_certificate_key  /etc/letsencrypt/live/blog.chenjia.info/privkey.pem;

        ssl_ciphers "TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-1:28-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;
        ssl_session_cache shared:SSL:10m;
        ssl_session_timeout  10m;
        ssl_session_tickets off;
        ssl_stapling on;
        ssl_stapling_verify on;
        ssl_trusted_certificate /etc/letsencrypt/live/blog.chenjia.info/chain.pem;
        resolver 8.8.4.4 8.8.8.8 valid=300s;
        resolver_timeout 10s;
        add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";

        location ~ .*\.(php|php5)?$
            {
                fastcgi_read_timeout 600;
                fastcgi_send_timeout 600;
                fastcgi_pass  unix:/tmp/php-cgi.sock;
                fastcgi_index index.php;
                include fastcgi.conf;
            }

        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
            {
                expires      365d;
            }

        location ~ .*\.(js|css)?$
            {
                expires      365d;
            }
        location /status {
            stub_status on;
            access_log   off;
        }

        access_log  /home/wwwlogs/blog.chenjia.info.log  access;
    }
```

最后重载一下nginx的配置。

```shell
/etc/init.d/nginx reload
```



### Let’s Encrypt续签

```
./certbot-auto renew
```

使用docker

```
docker run -it --rm --name certbot   -v /etc/letsencrypt:/etc/letsencrypt            -v /var/log/letsencrypt:/var/log/letsencrypt    -v /home:/home   certbot/certbot renew
```

或者自动重载nginx,适合加到计划任务里面。

```
./certbot-auto renew  --post-hook "service nginx reload"
```