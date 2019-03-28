### json

mysql数据库里conf 列的值为json 形式的字符串，如

```json
{"active_name":"xiangzhi","active_start_time":"2017-10-30 16:46:50","share_address":"http://api.colorv.cn/pages/web_video_template?template=xiangzhi_share","type":"normal","app_address":"","all":{"top_bar":"\"相知是友爱\"短视频制作活动","bg_color":"#ffffff","img1":"img/test/h5_01_86d28ea3ea3329c19ea5d14a77201cdc.jpg","img2":"img/test/123_03_3ad842ee87db54157d84cff318694cbd.jpg","img3":"img/test/look_tag@2x_50563953a6a00f840233e97c3099a375.png","img4":"img/test/123_04_5881d55d0dfa5c64e11709f3323ce23f.jpg","group_id":10028......}
```

怎么匹配json 里的某个group_id 非0 的数据？

[官方文档](https://dev.mysql.com/doc/refman/8.0/en/json.html)

```sql
select * ,JSON_EXTRACT(conf, '$.all.group_id') as group_id from `cv_h5_template_conf` where conf like '%group_id%' and created_at > '2018' and created_at < '2019' and  JSON_EXTRACT(conf, '$.all.group_id') != 0
```

