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


<h1>Ubuntu apache Mysql, Apache, php installation and configure steps</h1>
<pre>
sudo apt-get update
sudo apt-get install apache2
--------------------
sudo apt-get install mysql-server mysql-client
sudo mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
FLUSH PRIVILEGES;
sudo apt-get install phpmyadmin
sudo nano /etc/apache2/apache2.conf
include /etc/phpmyadmin/apache.conf >> add this line in the end.

--------------------

sudo apt-get install python-software-properties
sudo add-apt-repository ppa:ondrej/php
sudo add-apt-repository ppa:ondrej/apache2
sudo apt-get update

sudo apt-get install php7.2 php7.2-mysql php7.2-curl php7.2-json php7.2-cgi php-mbstring php7.2-mbstring php7.2-gettext libapache2-mod-php7.2 && sudo apt-get install php7.1 php7.1-mysql php7.1-curl php7.1-json php7.1-cgi php-mbstring php7.1-mbstring php7.1-gettext libapache2-mod-php7.1 && sudo apt-get install php7.0 php7.0-mysql php7.0-curl php7.0-json php7.0-cgi php-mbstring php7.0-mbstring php7.0-gettext libapache2-mod-php7.0 && sudo apt-get install php7.3 php7.3-mysql php7.3-curl php7.3-json php7.3-cgi php-mbstring php7.3-mbstring php7.3-gettext libapache2-mod-php7.3 && sudo apt-get install php7.4 php7.4-mysql php7.4-curl php7.4-json php7.4-cgi php-mbstring php7.4-mbstring php7.4-gettext libapache2-mod-php7.4

sudo apt install php7.1 libapache2-mod-php7.1 php7.1-common php7.1-gmp php7.1-curl php7.1-soap php7.1-bcmath php7.1-intl php7.1-mbstring php7.1-xmlrpc php7.1-mcrypt php7.1-mysql php7.1-gd php7.1-xml php7.1-cli php7.1-zip

=> Activate rewrite module.
sudo a2enmod rewrite
sudo service apache2 restart

---- Install ELASTICSEARCH ------
curl -XGET 'localhost:9200'
sudo service elasticsearch status
sudo service elasticsearch restart
sudo wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
sudo apt-get install apt-transport-https
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt-get update && sudo apt-get install elasticsearch

---- install m24 ----
sudo bin/magento setup:install --base-url=http://my.local/ --db-host=localhost --db-name=my_db --db-user=root --db-password=root --admin-firstname=admin --admin-lastname=admin --admin-email=rkhan@example.com --admin-user=admin --admin-password=Admin@123 --language=en_US --currency=USD --timezone=America/Chicago --use-rewrites=1 --backend-frontname=admin --search-engine=elasticsearch7 --elasticsearch-host=localhost:9200 --elasticsearch-port=9200

</pre>

<h1>Virtual Host apache ubuntu</h1>
<code>
    <VirtualHost *:80>
      ServerName custom.local
      ## Vhost docroot
      DocumentRoot "/var/www/html/custom_dir"
      ## Directories, there should at least be a declaration for /var/www/html/custom_dir

    <Directory "var/www/html/custom_dir">
        Options Indexes FollowSymlinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>
      ## Logging
      ErrorLog "/var/log/apache2/error.log"
      ServerSignature Off
      CustomLog "/var/log/apache2/access.log" combined
      ## Server aliases
      ServerAlias www.custom.local
      SetEnvIf Authorization "(.*)" HTTP_AUTHORIZATION=$1
        <IfModule mod_fastcgi.c>
                    AddHandler php7.4-fcgi .php
                    Action php7.4-fcgi /php7.4-fcgi virtual
                    Alias /php7.4-fcgi /usr/lib/cgi-bin/php7.4-fcgi-test.com
                    FastCgiExternalServer /usr/lib/cgi-bin/php7.4-fcgi-test.com -socket /var/run/php/php7.4-fpm-test.com.sock -pass-header Authorization
        </IfModule>
    </VirtualHost>
</code>
