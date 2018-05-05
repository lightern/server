# Setting up server

Let's update and install some packages:
```
pacman -Syu
pacman -S postgresql
pacman -S npm
```


## Setting up postgresql:
Log in as postgres:
```sudo -u postgres -i```

# to initialize database:
initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data'

# create db test:
createdb test

# create user:
createuser <username>
  

# go into test:
psql -d test

# Create schema, (not necessary)
create schema friends:

# create table:
create table companies( id SERIAL, name CHAR(15), idea CHAR(20));

# insert
insert into companies values (1, 'lol', 'lollataan');

# select
select * from companies;

# check columns:
\d test

# check out: 
\q

# create password:
alter user <username> with encrypted password '<password>';
alter user antti with encrypted password 'antti1';

# grant privileges:
grant all privileges on database <dbname> to <username> ;
grant all privileges on database test to antti ;



# test
curl --user "antti" GET http://localhost:8888/



# uncomment from /var/lib/postgres/data/postgresql.conf:
listen_addresses = 'localhost,my_local_ip_address'

# for more info:
# https://github.com/mevdschee/php-crud-api#configuration
# https://www.leaseweb.com/labs/2015/10/creating-a-simple-rest-api-in-php/
