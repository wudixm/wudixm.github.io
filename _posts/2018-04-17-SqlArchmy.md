```
stmt.where(tbl.c.rank >= GroupMembers.RANK_MEMBER)
stmt = stmt.where(tbl.c.rank.in_([GroupMembers.RANK_MEMBER, GroupMembers.RANK_ADMIN]))

stmt = tbl.select().where(or_(and_(tbl.c.group_id.in_(group_ids), tbl.c.kind == GroupMemberAI.KIND_APPLY),and_(tbl.c.user_id == user_id, tbl.c.kind == GroupMemberAI.KIND_INVITE))).order_by(tbl.c.created_at.desc()).offset(ops['start']).limit(ops['length'])
            
            
stmt = tbl.count().where(tbl.c.user_id == user_id).where(tbl.c.recommended.in_([GroupZoneItem.RECOMMENDED_YES,GroupZoneItem.RECOMMENDED_DIGESTED])).where(tbl.c.updated_at > datetime.datetime.now().replace(hour=0, minute=0))

stmt = tbl.select().where(tbl.c.kind == 'diamond').order_by(tbl.c.priority.asc()).where(tbl.c.priority > 0)

stmt = stmt.where(**tbl.c.question.like('%{}%'.format(request.args.get('key_name')))**)

stmt = tbl.select()
        stmt = stmt.where(tbl.c.outdate == 0)
        stmt = stmt.**order_by(tbl.c.rank_date.desc(), tbl.c.point.desc())**
        stmt = stmt.limit(limit)
        stmt = stmt.offset(offset)

stmt = tbl.select().order_by(tbl.c.id.desc()).where(and_(tbl.c.channel_id == channel_id,tbl.c.is_show == 1))
```

