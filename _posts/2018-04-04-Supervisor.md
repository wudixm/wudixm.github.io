```
ln -s source targe // linux 上创建一个软连接文件
ln -s  /data1/hoytwu/falsePlay/monitor/supervisorFile/configfile cv.bot.event.monitor.conf


root@mng-68:~# supervisorctl status
app:app_delay                            RUNNING   pid 5472, uptime 55 days, 2:58:33
app:app_moment                           RUNNING   pid 5473, uptime 55 days, 2:58:33
app:app_online                           RUNNING   pid 5474, uptime 55 days, 2:58:33
app:app_task_00                          STOPPED   Feb 26 02:21 PM
app:app_task_01                          STOPPED   Feb 26 02:21 PM
app:app_task_02                          STOPPED   Feb 26 02:22 PM
audio_hot_word                           RUNNING   pid 49129, uptime 1:08:45
bot_log_collector                        RUNNING   pid 30009, uptime 1:41:23
bot_log_video                            RUNNING   pid 47652, uptime 1 day,
。。。

root@mng-68:~# ls -l /etc/supervisor/conf.d/  // 在68 环境此目录下，有软件连接或实际的配置文件
total 4
lrwxrwxrwx 1 root root  48 Jan  4 10:53 cv.app.robot.conf -> /www/confcv/colorv/supervisors/cv.app.robot.conf
lrwxrwxrwx 1 root root  47 Jan  8 17:44 cv.app.task.conf -> /www/confcv/colorv/supervisors/cv.app.task.conf
-rw-r--r-- 1 root root 784 Mar 27 15:36 cv.bot.log.conf
lrwxrwxrwx 1 root root  39 Jan 23 12:43 cv.cvlog.visualize.conf -> /www/cvlog/monitor/cvlog.visualize.conf
lrwxrwxrwx 1 root root  49 Nov 29 18:48 cv.live_robot.conf -> /www/confcv/colorv/supervisors/cv.live_robot.conf
lrwxrwxrwx 1 root root  52 Dec 22 17:41 cv.monitor.error.conf -> /www/confcv/colorv/supervisors/cv.monitor.error.conf
lrwxrwxrwx 1 root root  44 Nov 29 18:45 cv.redis.conf -> /www/confcv/colorv/supervisors/cv.redis.conf
lrwxrwxrwx 1 root root  49 Jan 23 21:22 mysql.monitor.conf -> /www/confcv/colorv/supervisors/mysql.monitor.conf

supervisorctl stop/start/update

update 会去 /etc/supervisor/conf.d 目录下查找是否有最新的配置文件，如果发现有新的，则更新
```

