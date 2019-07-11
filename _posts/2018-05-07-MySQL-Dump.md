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


mysqldump -u <db_username> -h <db_host> -p db_name table_name > table_name.sql
root@mng-68:/data5/hoytwu/newScene/holdsDump# mysqldump -u dev -p coloru cv_scene_holds > cv_scene_holds_bak.sql
Enter password:
root@mng-68:/data5/hoytwu/newScene/holdsDump# ll
total 3553784
drwxr-xr-x 2 root root       4096 May 25 10:32 ./
drwxr-xr-x 4 root root       4096 May 25 10:31 ../
-rw-r--r-- 1 root root 3639060887 May 25 10:37 cv_scene_holds_bak.sql
root@mng-68:/data5/hoytwu/newScene/holdsDump#
```

### 还原

```
 mysql -u dev -psynergyinfo dmgr < dmgr.sql
mysql: [Warning] Using a password on the command line interface can be insecure.


mysql -uroot -p 数据库名< C:/Users/backup/数据库名.sql


mysql -h127.0.0.1 -P13310 -u dev -psynergyinfo workroom < cv_photo_filter_obs.sql
```

### 执行文件

```
root@tapi-221:/data1/hoytwu/add6000wtosceneid# mysql -u root -p coloru < insert_result.txt
Enter password:
root@tapi-221:/data1/hoytwu/add6000wtosceneid#


insert_result.txt 文件内容是

INSERT INTO `cv_scene_obs` (`id`, `code`, `name`, `tags`, `logo_etag`, `logo_path`, `mp4_etag_hd`, `mp4_path_hd`, `mp4_etag_bd`, `mp4_path_bd`, `created_at`, `updated_at`, `deleted_at`, `duration`)
VALUES
	(60000001,''。。。)
	。。。
```

