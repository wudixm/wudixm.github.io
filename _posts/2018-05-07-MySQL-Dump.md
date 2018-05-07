### SQLDump

```
root@tapi-221:~/dmgrsqldump# mysqldump -u dev -p audios_mgr -P 3311 > audios_mgr.sql
Enter password:
root@tapi-221:~/dmgrsqldump# ll
total 309860
drwxr-xr-x  2 root root      4096 May  7 09:30 ./
drwx------ 19 root root      4096 May  7 09:24 ../
-rw-r--r--  1 root root 298924386 May  7 09:30 audios_mgr.sql
-rw-r--r--  1 root root  18358973 May  7 09:29 dmgr.sql#
```

### 还原

```
 mysql -u dev -psynergyinfo dmgr < dmgr.sql
mysql: [Warning] Using a password on the command line interface can be insecure.

```

