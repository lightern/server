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

Start service
```
systemctl start postresql.service
systemctl enable postresql.service
```

**Restart computer**

Log in back as postgres:
```
sudo -u postgres -i
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

Create tables:
```
CREATE TABLE companies (companyid SERIAL PRIMARY KEY, creatoremail VARCHAR NOT NULL, name VARCHAR UNIQUE NOT NULL, idea VARCHAR NOT NULL, code VARCHAR NOT NULL, sector VARCHAR NOT NULL, subcode VARCHAR, subsector VARCHAR, city VARCHAR NOT NULL, area VARCHAR NOT NULL, lookingfor VARCHAR, contactdetails VARCHAR NOT NULL, extrainfo VARCHAR);
CREATE TABLE users (userid SERIAL PRIMARY KEY, useremail VARCHAR UNIQUE NOT NULL, userpassword VARCHAR NOT NULL, usersalt VARCHAR NOT NULL, usertoken VARCHAR NOT NULL, userstatus VARCHAR NOT NULL);
CREATE TABLE cities (cityid SERIAL PRIMARY KEY, city VARCHAR NOT NULL, citysw VARCHAR NOT NULL, area VARCHAR NOT NULL);
CREATE TABLE sectors (sectorid SERIAL PRIMARY KEY, code VARCHAR NOT NULL, sector VARCHAR NOT NULL, subcode VARCHAR NOT NULL, subsector VARCHAR NOT NULL);
CREATE TABLE mainsectors (mainsectorid SERIAL PRIMARY KEY, code VARCHAR NOT NULL, sector VARCHAR NOT NULL);
CREATE TABLE professions (professionid SERIAL PRIMARY KEY, profession VARCHAR NOT NULL);

```

Import data, then ctrl+d and exit!

## Useful commands

Insert
```
INSERT INTO companies (name, idea) VALUES ('company1', 'idea1');
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

In API remember to define also the used database!


Gor more info:
https://github.com/mevdschee/php-crud-api#configuration
https://www.leaseweb.com/labs/2015/10/creating-a-simple-rest-api-in-php/
