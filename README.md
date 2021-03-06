## Setup software:
#### Autostart API:
```
touch /etc/systemd/system/api.service
chmod 664 /etc/systemd/system/api.service
echo $'[Unit]\nDescription=API\n\n[Service]\nExecStart=/root/api\nType=forking\nRestart=always\n\n[Install]\nWantedBy=default.target' >> /etc/systemd/system/api.service
systemctl enable api.service
systemctl start api.service
```

## Setting up nginx server

#### Installation:
```
pacman -Syu
pacman -S nginx-mainline
systemctl start nginx.service
systemctl enable nginx.service
```
#### Optimization:
First let's look for number of cores and cores limitations:
```
grep processor /proc/cpuinfo | wc -l
ulimit -n
```

Then update the info to /etc/nginx/nginx.conf:
```
worker_processes <number of cores>;
worker_connections <ulimit>;
keepalive_timeout 15;
```
Optional:
```
client_body_buffer_size 10K;
client_header_buffer_size 1k;
client_max_body_size 8m;
large_client_header_buffers 2 1k;
client_body_timeout 12;
client_header_timeout 12;
send_timeout 10;
```

Remember to:
```
sudo service nginx restart
```

#### Setting up multiple domains:
Edit /etc/nginx/nginx.conf:
```
server {
        listen 80;
        listen [::]:80;
        server_name domainname1.dom;
        root /usr/share/nginx/domainname1.dom/html;
        location / {
           index index.php index.html index.htm;
        }
}

server {
        listen 80;
        listen [::]:80;
        server_name domainname2.dom;
        root /usr/share/nginx/domainname2.dom/html;
        location / {
           index index.php index.html index.htm;
        }
}
```
https://wiki.archlinux.org/index.php/Nginx#Server_blocks

#### To remember:
* Default path: /usr/share/nginx/html/index.html
* Config: /etc/nginx/nginx.conf

More info:
* https://wiki.archlinux.org/index.php/Nginx
* https://www.digitalocean.com/community/tutorials/how-to-optimize-nginx-configuration

## Setting up Apache server

```
pacman -Syu
pacman -S apache
systemctl start httpd.service
systemctl enable httpd.service
```
#### Create HTTPS in Apache:
In /etc/httpd/conf/httpd.conf, uncomment these:
```
LoadModule ssl_module modules/mod_ssl.so
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
Include conf/extra/httpd-ssl.conf
```
On server, generate CSR:
```
cd /etc/httpd/conf
openssl req -new -x509 -nodes -newkey rsa:4096 -keyout server.key -out server.crt -days 1095
chmod 400 server.key
```
After obtaining a key and certificate, make sure the SSLCertificateFile and SSLCertificateKeyFile lines in /etc/httpd/conf/extra/httpd-ssl.conf point to the key and certificate. 

Reboot httpd.service

## Setting up postgresql:

Let's update and install some packages:
```
pacman -Syu
pacman -S postgresql
```

Log in as postgres:
```
sudo -u postgres -i
```

Initialize database and exit as postgres:
```
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'
exit
```

Uncomment listen_addresses and port from /var/lib/postgres/data/postgresql.conf:
```
sed -i "/port = 5432/s/^#//g" '/var/lib/postgres/data/postgresql.conf'
sed -i "/listen_addresses = 'localhost'/s/^#//g" '/var/lib/postgres/data/postgresql.conf'
```

**Restart computer**

Start service
```
systemctl start postgresql.service
systemctl enable postgresql.service
```

Log in back as postgres:
```
sudo -u postgres -i
```

Create db <dbname>:
```
createdb test
```

Create <username>:
```
createuser <username>
```

Go into <dbname>:
```
psql -d test
```

Create password:
```
alter user <username> with encrypted password '<password>';
```

Grant privileges:
```
grant all privileges on database <dbname> to <username> ;
```

Create schema, (not necessary)
```
create schema friends:
```

Create tables:
```
CREATE TABLE companies (companyid SERIAL PRIMARY KEY, creatorid INT NOT NULL, name VARCHAR NOT NULL, idea VARCHAR, code VARCHAR, sector VARCHAR, subcode VARCHAR, subsector VARCHAR, city VARCHAR, area VARCHAR, lookingfor VARCHAR, contactdetails VARCHAR, extrainfo VARCHAR, createdate TIMESTAMP NOT NULL, startdate TIMESTAMP, status VARCHAR);
CREATE TABLE users (userid SERIAL PRIMARY KEY, useremail VARCHAR UNIQUE NOT NULL, userpassword VARCHAR NOT NULL, usersalt VARCHAR NOT NULL, usertoken VARCHAR NOT NULL, userverificationstring VARCHAR NOT NULL, userstatus VARCHAR NOT NULL);
CREATE TABLE cities (cityid SERIAL PRIMARY KEY, city VARCHAR NOT NULL, citysw VARCHAR NOT NULL, area VARCHAR NOT NULL);
CREATE TABLE sectors (sectorid SERIAL PRIMARY KEY, code VARCHAR NOT NULL, sector VARCHAR NOT NULL, subcode VARCHAR NOT NULL, subsector VARCHAR NOT NULL);
CREATE TABLE mainsectors (mainsectorid SERIAL PRIMARY KEY, code VARCHAR NOT NULL, sector VARCHAR NOT NULL);
CREATE TABLE professions (professionid SERIAL PRIMARY KEY, profession VARCHAR NOT NULL);

```

Import data, then ctrl+d and exit!

## Useful commands

Insert
```
insert into companies (creatorid, name, idea, code, sector, subcode, subsector, city, area, lookingfor, contactdetails, extrainfo) values (1, 'Meitsin firma', 'Ostoskeskus', 3, 'Sektori', 4, 'Subsektori', 'Helsinki', 'Uusimaa', 'Meedio', '0700123123', 'Ei lisätietoja.');

```

Select
```
select * from companies;
```

Check columns:
```
\d test
```

Check out: 
```
\q
```

Test
curl --user "< username >" GET http://localhost:8888/

In API remember to define also the used database!


Gor more info:
https://github.com/mevdschee/php-crud-api#configuration
https://www.leaseweb.com/labs/2015/10/creating-a-simple-rest-api-in-php/
