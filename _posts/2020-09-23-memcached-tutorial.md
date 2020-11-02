# Memcached Home

Memcached is an open source, high-performance, distributed memory object caching system. This tutorial provides a basic understanding of all the relevant concepts of Memcached needed to create and deploy a highly scalable and performance-oriented system.

用来部署易扩展，面向性能的系统

# Memcached - Overview

Memcached is an open source, high-performance, distributed memory caching system intended to speed up dynamic web applications by reducing the database load.It is a key-value dictionary of strings, objects, etc., stored in the memory, resulting from database calls, API calls, or page rendering.

- 开源的，高性能，分布式内存缓存系统，目的是靠减少数据库负载来加速动态网站的加载速度。
- k-v 字典，存储string, object等

关键点
- It is open source.
- Memcached server is a big hash table.
- It significantly reduces the database load
- It is perfectly efficient for websites with high database load.
- It is distributed under Berkeley Software Distribution (BSD) license.
- It is a client-server application over TCP or UDP.


Memcached is not 

- a persistent data store，不是用来存储数据的
- a database
- application-specific
- a large object cache，不能存储大对象
- fault-tolerant or highly available，不是高可用
