# slave

pastikan kita dah setup bahagian master terlebih dulu

### 1. edit my.cnf seperti di bawah

```
server-id       = 2
relay-log       = /var/log/mysql/mysql-relay-bin.log
log_bin         = /var/log/mysql/mysql-bin.log
binlog-do-db    = employees
```

pastikan restart mysql selepas edit my.cnf

### 2. copy master_employees yg dah di export dari master . kita boleh gunakan command scp

```
scp root@178.128.219.253:/root/docker-webstack/sql/master_employees.sql master_employees.sql
```
`178.128.219.253` adalah ip master server

`/root/docker-webstack/sql/master_employees.sql` adalah lokasi file di master server

### 3. masuk ke mysql shell dan create database employees
```
mysql -u root
```
```
CREATE DATABASE employees;
EXIT;
```

### 4. import master_employees.sql kedalam db employees
```
mysql -u root employees < master_employees.sql
```

### 5. masuk balik ke mysql shell dan run command berikut
```
mysql -u root
```
```
CHANGE MASTER TO MASTER_HOST='178.128.219.253',MASTER_USER='slave_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='mysql-bin.000001', MASTER_LOG_POS=645;
```
`178.128.219.253` adalah ip address master server
`slave_user` adalah user yg kita create di master 

`mysql-bin.000001` dan `645` adalah result yg kita dapat masa run `SHOW MASTER STATUS;` di master

contoh result dari master tadi

| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
|------------------|----------|--------------|------------------|
| mysql-bin.000001 |      645 | employees    |                  |

1 row in set (0.000 sec)


### 6. start slave
```
START SLAVE;
```

### 7. semak status
```
SHOW SLAVE STATUS\G
```

perhatikan bagian 
```
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
```             
pastikan dua2 ni running. jika ada error, boleh semak di bahagian error msg 

### 10. exit mysql shell. dan selesai bahagian slave

anda boleh insert rekod di master dan semak sama ada rekod tersebut dapat di sync ke slave. untuk slave yg melebihi 1, ulangi proses dari step 1 di atas
