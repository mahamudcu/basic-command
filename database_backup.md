## MySql Database

```bash 
 => mysql -u root -p
 => SHOW DATABASES;
 => CREATE DATABASE db_name;
```
#### Download Mysql Database

```bash 
 => mysqldump -u root -p db_name > db_name_v2.sql
<---------Spacial case backup custom user ------>
 => mysqldump --single-transaction -u db_user -p db_1 > new_db.sql
```
#### Set Uploaded Mysql Database

```bash 
 => mysql -u root -p db_name < db_name_v2.sql
```
#### Server To Server Copier
```bash 
 => scp deployer@194.233.00.00:/home/deployer/lienexpos_production_v1_10_03_2023.sql   deployer@143.110.000.00:/home/deployer
 => scp deployer@178.128.102.000:/home/deployer/sql_file.sql  /home/syed/Desktop
```

## PSQL Database

```bash 
 => pg_dump -U db_user db_name   > /home/syftet/Development/db_name.dump 
 example:
 => pg_dump -U postgres campusbit_web_development >/home/syftet/Development/campusbit_db.dump
```





How to Restore database from project
 > go to project directory by terminal 
 > now write

psql -U db_user db_name < /path/new_name.dump

example:
psql -U postgres campusbit_web_development < /home/syed/Downloads/campusbit_db.dump
or
psql -U postgres healthrecord_development < /home/syed/Downloads/healthrecord_development_db.dump


 If FATAL: Peer authentication failed for user “dev”

Go to
https://stackoverflow.com/questions/18664074/getting-error-peer-authentication-failed-for-user-postgres-when-trying-to-ge