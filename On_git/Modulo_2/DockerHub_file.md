# Example of using MySQL in Docker

## Before compose-up

```sh
sudo setenforce 0
sudo systemctl enable --now docker.service
sudo mkdir /mnt/sr0
sudo mount /dev/sr0 /mnt/sr0
cd /mnt/sr0/docker/
sudo docker load -i mariadb_latest.tar
sudo docker load -i site_latest.tar
sudo docker images
```

On your Linux-system I recommend make folder for this compose file.

```sh
sudo mkdir -p /opt/nextcloud
sudo nano /opt/nextcloud/docker-compose.yml
```

```yml
volumes:
  nextcloud:
  db_nextcloud:

services:
  db_mysql:
    image: mariadb:10.11
    container_name: db_mysql
    restart: always
    volumes:
      - db_nextcloud:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=nextcloud
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud

  app:
    image: nextcloud:latest
    restart: always
    ports:
      - 8081:80
    depends_on:
      - db_mysql
    volumes:
      - nextcloud:/var/www/html
    environment:
      - MYSQL_PASSWORD=nextcloud
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_HOST=db_mysql
```
This is example for compose-file to up NextCloud in Docker with MySQL. Be careful with the image version `mariadb`. Otherwise, you will have to download the latest version from the Internet.

For start:
```sh
cd /opt/nextcloud
sudo docker compose -f docker-compose.yml up -d
```

ONLY IF YOU NEED STOP:
```sh
cd /opt/nextcloud
sudo docker compose down
```
## An example of using a simple MySQL application
```sh
sudo mkdir -p /opt/simple_web
sudo nano /opt/simple_web/docker-compose.yml
```

In this file I use simple Python FastAPI app. I pull image with app later  :). Example with magic-python-app:

```yml
services:
  testapp:
    container_name: testapp
    image: site:latest
    restart: always
    ports:
      - "8080:8000"
    environment:
      DB_HOST: "db"
      DB_PORT: "3306"
      DB_NAME: testdb
      DB_USER: testс
      DB_PASS: P@ssw0rd
      DB_TYPE: maria
    depends_on:
      - db
  db:
    container_name: db
    image: mariadb:10.11
    restart: always
    ports:
      - "3306:3306"
    environment:
      MARIADB_USER: testс
      MARIADB_PASSWORD: P@ssw0rd
      MARIADB_DATABASE: testdb
      MARIADB_ROOT_PASSWORD: P@ssw0rd
```

For start I used this command

```sh
cd /opt/simple_web
sudo docker compose -f docker-compose.yml up -d
```

ONLY IF YOU NEED STOP:
```sh
cd /opt/simple_web
sudo docker compose down
```
---------------------------------------------------------------------------
=========================================================================
# Example work with MySQL on High Availability Server Linux

For enable service for MySQL and Apache (yes, someone else is using this old thing)

```sh
sudo setenforce 0
sudo systemctl enable --now docker.service
sudo mkdir /mnt/sr0
sudo mount /dev/sr0 /mnt/sr0
sudo systemctl enable httpd --now
sudo systemctl enable mariadb --now
sudo mysql_secure_installation
```

but you use bare-metall... and you can only:
```sh
sudo mysql -u root -p
```
and write password. If you forgot: repeat `sudo mysql_secure_installation`. In this place you need сreate a user and give them access to the database:

```mysql
CREATE DATABASE webdb;
CREATE USER 'webc'@'%' IDENTIFIED BY 'P@ssw0rd';
GRANT ALL PRIVILEGES ON webdb.* TO 'webc'@'%';
FLUSH PRIVILEGES;
QUIT;
```
I think you have any dump, mb `aero.sql` or another. For my examle:
```sh
sudo mysql webdb < /mnt/sr0/web/dump.sql
```

For Apache you need `cp` config

```sh
sudo cp /mnt/sr0/web/index.php /var/www/html/
sudo cp /mnt/sr0/web/logo.png /var/www/html/
sudo nano /var/www/html/index.php
```

In `/var/www/html/index.php` change params:
```php
$username = "webc"
$password = "P@ssw0rd"
$dbname = "webdb"
```

Finally:
```sh
sudo systemctl restart httpd.service
```

----------------------------------------------------------------------------------------------------------------------------=========================================================================

# To learn how to work with nginx

## Config Nginx for MySQL with Nextcloud

```sh
sudo nano /etc/nginx/conf.d/nextcloud.conf
```

In `/etc/nginx/conf.d/nextcloud.conf` write the following:

```nginx
server {
       listen 80;
       server_name nextcloud.sirius-exam.org;
       location / {
           proxy_pass http://192.168.20.2:8081;
           proxy_set_header	Host	$host;
           proxy_set_header   X-Real-IP        $remote_addr;
           proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
       }
}
```

## Config for MySQL with testapp in docker

Then we will do the same configuration, but for the application (I promise that I will post the image later :3 )

```sh
sudo nano /etc/nginx/conf.d/docker.conf
```

```nginx
server {
       listen 80;
       server_name docker.sirius-exam.org;
       location / {
           proxy_pass http://192.168.20.2:8080;
           proxy_set_header	Host	$host;
       }
}
```
## Config for web Apache with MySQL + bonus for web-based auth)

```sh
sudo htpasswd -c /etc/nginx/.htpasswd WEBc
sudo nano /etc/nginx/conf.d/web.conf
```

```nginx
server {
       listen 80;
       server_name web.sirius-exam.org;
       location / {
           proxy_pass http://192.168.100.2:80;
           proxy_set_header	Host	$host;
           auth_basic           "AUTH";
           auth_basic_user_file /etc/nginx/.htpasswd;
       }
}
```


# AFTER NGINX CONFIG WRITE

```sh
sudo systemctl restart nginx.service
sudo setenforce 0
```

------------------------------------------------------------------------------------------
=========================================================================

# I don't use DNS...Yes, I am bad)

For use in your browser domain-name, you need change `/etc/hosts` on client-system with browser:

```sh
172.16.1.1   docker   docker.sirius-exam.org
172.16.1.1   nextcloud   nextcloud.sirius-exam.org
172.16.1.1   web      web.sirius-exam.org
```

After all command you 
http://docker.sirius-exam.org
http://nextcloud.sirius-exam.org
http://web.sirius-exam.org