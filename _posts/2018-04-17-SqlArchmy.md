---
title: Python sqlalchemy
excerpt: |
  Python sqlalchemy
category: 数据库
feature_image: "https://picsum.photos/2560/600?image=872"
---

```
stmt.where(tbl.c.rank >= GroupMembers.RANK_MEMBER)
stmt = stmt.where(tbl.c.rank.in_([GroupMembers.RANK_MEMBER, GroupMembers.RANK_ADMIN]))

stmt = tbl.select().where(or_(and_(tbl.c.group_id.in_(group_ids), tbl.c.kind == GroupMemberAI.KIND_APPLY),and_(tbl.c.user_id == user_id, tbl.c.kind == GroupMemberAI.KIND_INVITE))).order_by(tbl.c.created_at.desc()).offset(ops['start']).limit(ops['length'])
            
            
stmt = tbl.count().where(tbl.c.user_id == user_id).where(tbl.c.recommended.in_([GroupZoneItem.RECOMMENDED_YES,GroupZoneItem.RECOMMENDED_DIGESTED])).where(tbl.c.updated_at > datetime.datetime.now().replace(hour=0, minute=0))

stmt = tbl.select().where(tbl.c.kind == 'diamond').order_by(tbl.c.priority.asc()).where(tbl.c.priority > 0)

stmt = stmt.where(**tbl.c.question.like('%{}%'.format(request.args.get('key_name')))**)

stmt = tbl.count().where(tbl.c.user_id == user_id). \
            where(tbl.c.recommended.in_([GroupZoneItem.RECOMMENDED_YES, GroupZoneItem.RECOMMENDED_DIGESTED])) \
            .where(tbl.c.updated_at > datetime.datetime.now().replace(hour=0, minute=0))
        


stmt = tbl.select()
        stmt = stmt.where(tbl.c.outdate == 0)
        stmt = stmt.**order_by(tbl.c.rank_date.desc(), tbl.c.point.desc())**
        stmt = stmt.limit(limit)
        stmt = stmt.offset(offset)

stmt = tbl.select().order_by(tbl.c.id.desc()).where(and_(tbl.c.channel_id == channel_id,tbl.c.is_show == 1))



stmt = tbl.select(**tbl.c.user_id**).where(tbl.c.created_at > (datetime.datetime.now() - datetime.timedelta(int(d))))


stmt = stmt.where(sqlalchemy.or_(
                tbl.c.category_id.in_(cat_ids),
                tbl.c.kind != 'video'
            ))
            
            
联合查询
tbl_a = ActivityVideoRanking.get_tbl()
tbl_kw = VideoKeyword.get_tbl()
stmt = tbl_a.select() \
    .select_from(tbl_a.join(tbl_kw, tbl_a.c.video_id == tbl_kw.c.video_id))\
    .where(tbl_kw.c.verify == VideoKeyword.VERIFY_PASS).where(tbl_a.c.activity_name == rank_topic)
stmt = stmt.order_by(tbl_a.c.ranking.asc())
obs = list(stmt.execute())



city_obj = City.get_clz_obj(nation=nation, state=state, city=city)
            if city_obj.is_null:
                city_obj.set('nation', nation)
                city_obj.set('state', state)
                city_obj.set('city', city)
                city_obj.save()
                
                
#group by                
stmt = tbl.select().where(tbl.c.created_at >= begin).where(tbl.c.created_at < end).group_by(tbl.c.user_id)


数据库中is 和== 行为不一致
In [74]: ret = list(tbl.select().where(tbl.c.deleted_at == None).execute())

In [75]: ret
Out[75]:
[(1L, u'\u65d7\u888d', u'[{"video_id":"1","desc":"4"},{"video_id":"3","desc":"6"}]', datetime.datetime(2018, 7, 20, 11, 45, 38), datetime.datetime(2018, 7, 20, 11, 46, 11), None),
 (2L, u'\u76f4\u64ad', u'[{"video_id":"2","desc":"6"},{"video_id":"4","desc":"8"},{"video_id":"5","desc":"9"}]', datetime.datetime(2018, 7, 19, 19, 14, 52), datetime.datetime(2018, 7, 19, 19, 14, 52), None),
 (4L, u'\u6d3b\u52a8', u'[{"video_id":"121","desc":"121"},{"video_id":"131","desc":"131"},{"video_id":"18","desc":"18"}]', datetime.datetime(2018, 7, 20, 11, 45, 14), datetime.datetime(2018, 7, 20, 11, 45, 46), None),
 (5L, u'\u56de\u653e', u'[{"video_id":"1417","desc":"1417"},{"video_id":"17","desc":"17"}]', datetime.datetime(2018, 7, 20, 11, 45, 17), datetime.datetime(2018, 7, 20, 11, 45, 49), None)]

In [76]: ret = list(tbl.select().where(tbl.c.deleted_at is None).execute())

In [77]: ret
Out[77]: []

```



### in isnot

```
tbl = App.get_tbl(tbl_name, schema='coloru', db_name='db_search')
        stmt = select([tbl.c.id, tbl.c.name])
        stmt = stmt.where(tbl.c.id.in_(video_ids))
        stmt = stmt.where(tbl.c.name.isnot(None))
        stmt = stmt.where(tbl.c.name != '')
        rows = list(stmt.execute())
```

### update

```
tbl = GroupAnnouncement.get_tbl()
stmt = tbl.update().where(tbl.c.id > 0).values(sticky=GroupAnnouncement.STICK_NO).execute()
stmt = tbl.update().where(tbl.c.id == group_anc_id).values(sticky=GroupAnnouncement.STICK_YES).execute()

tbl = FilmTemplateHeader.get_tbl()
stmt = tbl.update().where(tbl.c.id==34).values(vip_template=0).execute()
```


### distinct

```
        tbl = GroupActivity.get_tbl()
        stmt = tbl.select().distinct(tbl.c.obj_id).where(tbl.c.status==GroupActivity.ON_DUTY).order_by(tbl.c.rank.desc())
        ret = list(stmt.execute())

```

### delete

```
        tbl = GroupZoneItem.get_tbl()
        tbl.delete().where(tbl.c.obj_kind == obj_kind).where(tbl.c.obj_id == obj_id).execute()
```

