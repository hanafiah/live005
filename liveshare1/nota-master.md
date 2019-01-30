1. run mysql shell dan create replika user

mysql shell
```
mysql -u root
```

create user
```
GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password';
```
