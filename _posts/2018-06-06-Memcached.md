---
title: Memcache 实际使用
excerpt: |
  Memcache 实际使用
category: 数据库
feature_image: "https://picsum.photos/2560/600?image=872"
---
### 群组删除

```
echo 'delete GroupMembers#3290514#10325' | nc 127.0.0.1 11213

echo 'get Video#id#104457224' | nc 127.0.0.1 11213

root@mng-68:~# echo 'delete Video#id#105656565' | nc 127.0.0.1 11214
DELETED
```

