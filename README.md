# Setting up server

Let's update and install some packages:
```
pacman -Syu
pacman -S postgresql
pacman -S npm
```


## Setting up postgresql:
Log in as postgres:
```
sudo -u postgres -i
```

To initialize database:
```
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'
```

Create db test:
```
createdb test
```

Create user:
```
createuser <username>
```
  

Go into test:
```
psql -d test
```

Create schema, (not necessary)
```
create schema friends:
```

Create table:
```
create table companies( id SERIAL, name CHAR(15), idea CHAR(20));
```

Insert
```
insert into companies values (1, 'lol', 'lollataan');
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

Create password:
```
alter user <username> with encrypted password '<password>';
```

Grant privileges:
```
grant all privileges on database <dbname> to <username> ;
```

Test
curl --user "< username >" GET http://localhost:8888/



Uncomment from /var/lib/postgres/data/postgresql.conf:
listen_addresses = 'localhost,my_local_ip_address'

Grant privileges:
```
systemctl start postresql.service
systemctl enable postresql.service
```

Gor more info:
https://github.com/mevdschee/php-crud-api#configuration
https://www.leaseweb.com/labs/2015/10/creating-a-simple-rest-api-in-php/
