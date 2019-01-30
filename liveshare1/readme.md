# master

sebelum teruskan langkah di bawah. pastikan anda dah ada db. dalam contoh ni db yang digunakan adalah employees . setup db di master server sahaja

### 1. edit my.cnf seperti di bawah

```
bind-address    = 0.0.0.0
# boleh gunakan ip address server. jika direct install mariadb/mysql
# bind-address    = 178.128.219.253

server-id       = 1
log_bin         = /var/log/mysql/mysql-bin.log
binlog-do-db    = employees
```

pastikan restart mysql selepas edit my.cnf

### 2. run mysql shell dan create replika user

mysql shell
```
mysql -u root
```

create user
```
GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password';
```

### 3. flush priviledge
```
FLUSH PRIVILEGES;
```

### 4. lock table
```
USE employees;
FLUSH TABLES WITH READ LOCK;
```

### 5. run command master status dan catat result 
```
SHOW MASTER STATUS;
```

contoh result

| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
|------------------|----------|--------------|------------------|
| mysql-bin.000001 |      645 | employees    |                  |

1 row in set (0.000 sec)

kita akan gunakan maklumat di kolum File dan Position masa setup slave nanti

### 6. exit mysql shell
```
exit
```

### 7. export db employees
```
mysqldump -u root --opt employees > /sql/master_employees.sql
```

### 8. masuk ke mysql shell semula 
```
mysql -u root
```

### 9. unlock table semula
```
 use employees;
 UNLOCK TABLES;
```

### 10. exit mysql shell. dan selesai bahagian master
