# M2-HANDBOOK - Notes for Magento2

Magento2 Profiler Impliemntation: <br/>

Source: https://blog.e-zest.com/magento-2-static-code-analysis#top <br/>

github coding standards: https://github.com/magento/magento-coding-standard <br/>


<h2>ENABLE HTML PROFILER IN MAGENTO2</h2>
Add <b>SetEnv MAGE_PROFILER "html"</b> in .htaccess in your project


<h2> Mysql Optmization : add below lines in /etc/mysql/my.cnf</h2>
<pre>
[mysqld]
query_cache_type=1
query_cache_size=512M
query_cache_limit=512M
innodb_buffer_pool_size=4096M
innodb_flush_log_at_trx_commit=0
max_heap_table_size=768M
tmp_table_size=768M
innodb_log_file_size=1024M [This should be 25% of innodb_buffer_pool_size]
</pre>


<h2>Password Hash for Admin@123</h2>
<pre>c1aea2f6c5dcb3759b7b43c0fa51a6d58377e5a1661d6a72ec85a193c2c5fceb:mMtBLa6jHHljChjtLB8yoQWZzzemH2ip:1</pre>

<h2>Restart Mysql Ubuntu</h2>
<pre>sudo /etc/init.d/mysql stop && sudo /etc/init.d/mysql stop</pre>

<h2>NGINX Conf file for magento</h2>
<pre>
upstream fastcgi_backend {
    server  unix:/run/php/php7.1-fpm.sock;
}

server {
    listen 80;
    listen [::]:80;
    server_name your.server.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your.server.com;

    ssl_certificate /etc/nginx/ssl/your.server.com.chained.crt;
    ssl_certificate_key /etc/nginx/ssl/your.server.com.key;

    set $MAGE_ROOT /var/www/html/you_project;
    set $MAGE_MODE production;
    include /var/www/html/your_project/nginx.conf.sample;

    include snippets/phpmyadmin.conf;
}
</pre>


<h2>Local nginx conf file for magento</h2>
<pre>upstream fastcgi_backend {
        server  unix:/run/php/php7.1-fpm.sock;
}

server {
        listen 80;
        server_name local.domain.com;

        set $MAGE_ROOT /var/www/html/directory;
        set $MAGE_MODE production;
        include /var/www/html/directory/nginx.conf.sample;

        location ~ /\.ht {
                deny all;
        }
}
</pre>
