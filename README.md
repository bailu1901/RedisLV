[RedisLV-支持基于LevelDB的Redis(2.8.19)持久化方法](https://github.com/ivanabc/RedisLV)
---

### Redis 持久化的问题
1. RDB方式: 数据持久化的过程中存在不可控的内存消耗（linux fork本身的问题）。
2. AOF方式: 通过aof文件恢复数据库的过程慢。

总之，Redis数据库数据备份方式不满足`滚服游戏的服务器`（机器配置比较低）对游戏数据存储备份的需要。

---
### RedisLV 

#### 优点
1. 对于内存有限的服务，数据持久化过程额外内存消耗可控。
2. 相对RDB方式，数据库异常时候数据丢失的少。
2. 相对AOF方式，数据库的恢复更快（遍历leveldb写入内存）。

#### 缺点

1. 由于对redis写入操作需要同步到leveldb，导致性能损耗(读操作不受影响)。

#### 安装
* 阅读 [README](README)

#### 配置(redis.conf)
~~~
# leveldb 
leveldb yes
leveldb-path ./var 
~~~

#### 备份
```
redis-cli backup dir(备份文件目录)
```
* 当备份目录中包含BACKUP.log文件并且文件中有**SUCCESS**字段，表示备份成功

#### 注意事项
1. RedisLV中key的最大长度为 **127**；

---
### RedisLV(配置leveldb选项)与Redis对比

1. 命令支持状况(yes: 支持; no: 不支持)。`在RedisLV中使用不支持的命令将导致数据不能正确落地`


| Key         |  RedisLV       | Redis        |
|-------------|----------------| -------------|
| DEL         |       yes      |      yes     |
| DUMP        |       yes      |      yes     |
| EXISTS      |       yes      |      yes     |
| EXPIRE      |       `no`     |      yes     |
| EXPIREAT    |       `no`     |      yes     |
| KEYS        |       yes      |      yes     |
| MIGRATE     |       `no`     |      yes     |
| MOVE        |       `no`     |      yes     |
| OBJECT      |       yes      |      yes     |
| PERSIST     |       `no`     |      yes     |
| PEXPIRE     |       `no`     |      yes     |
| PEXPIREAT   |       `no`     |      yes     |
| PTTL        |       `no`     |      yes     |
| RANDOMKEY   |       yes      |      yes     |
| RENAME      |       `no`     |      yes     |
| RENAMENX    |       `no`     |      yes     |
| RESTORE     |       `no`     |      yes     |
| SORT        |`yes(not store)`|      yes     |
| TTL         |       `no`     |      yes     |
| TYPE        |       yes      |      yes     |
| SCAN        |       yes      |      yes     |

---

| String      | RedisLV        | Redis        |
|-------------|----------------|--------------|
| APPEND      |       yes      |      yes     |
| BITCOUNT    |       yes      |      yes     |
| BITOP       |       yes      |      yes     |
| DECR        |       yes      |      yes     |
| DECRBY      |       yes      |      yes     |
| GET	        |       yes      |      yes     |
| GETBIT      |       yes      |      yes     |
| GETRANGE    |       yes      |      yes     |
| GETSET      |       yes      |      yes     |
| INCR        |       yes      |      yes     |
| INCRBY      |       yes      |      yes     |
| INCRBYFLOAT |       yes      |      yes     |
| MGET        |       yes      |      yes     |
| MSET        |       yes      |      yes     |
| MSETNX      |       yes      |      yes     |
| PSETEX      |       `no`     |      yes     |
| SET         |       yes      |      yes     |
| SETBIT      |       yes      |      yes     |
| SETEX       |       `no`     |      yes     |
| SETNX       |       yes      |      yes     |
| SETRANGE    |       yes      |      yes     |
| STRLEN      |       yes      |      yes     |

---

| Hash        | RedisLV        | Redis        |
|-------------|----------------|--------------|
| HDEL        |       yes      |      yes     | 
| HEXISTS     |       yes      |      yes     |
| HGET        |       yes      |      yes     |
| HGETALL     |       yes      |      yes     |
| HINCRBY     |       yes      |      yes     |
| HINCRBYFLOAT|       yes      |      yes     |
| HKEYS       |       yes      |      yes     |
| HLEN        |       yes      |      yes     |
| HMGET       |       yes      |      yes     |
| HMSET       |       yes      |      yes     |
| HSET        |       yes      |      yes     |
| HSETNX      |       yes      |      yes     |
| HVALS       |       yes      |      yes     |
| HSCAN       |       yes      |      yes     |

---

| Set         |   RedisLV      | Redis        |
|-------------|----------------|--------------|
| SADD        |       yes      |      yes     |
| SCARD       |       yes      |      yes     |
| SDIFF       |       yes      |      yes     |
| SDIFFSTORE  |       `no`     |      yes     |
| SINTER      |       yes      |      yes     |
| SINTERSTORE |       `no`     |      yes     |
| SISMEMBERS  |       yes      |      yes     |
| SMEMBERS    |       yes      |      yes     |
| SMOVE       |       `no`     |      yes     |
| SPOP        |       `no`     |      yes     |
| SRANDMEMBER |       yes      |      yes     |
| SREM        |       yes      |      yes     |
| SUNION      |       yes      |      yes     |
| SUNIONSTORE |       `no`     |      yes     |
| SSCAN       |       yes      |      yes     |

---

| SortedSet       | RedisLV      | Redis        |
|-----------------|--------------|--------------|
| ZADD            |       yes    |      yes     |
| ZCARD           |       yes    |      yes     |
| ZCOUNT          |       yes    |      yes     |
| ZINCRBY         |       yes    |      yes     |
| ZRANGE          |       yes    |      yes     |
| ZRANGEBYSCORE   |       yes    |      yes     |
| ZRANK           |       yes    |      yes     |
| ZREM            |       yes    |      yes     |
| ZREMRANGEBYRANK |       yes    |      yes     |  
| ZREMRANGEBYSCORE|       yes    |      yes     |
| ZREVRANGE       |       yes    |      yes     |
| ZREVRANKBYSCORE |       yes    |      yes     |
| ZREVRANK        |       yes    |      yes     |
| ZSCORE          |       yes    |      yes     |
| ZUNIONSTORE     |       `no`   |      yes     |
| ZINTERSTORE     |       `no`   |      yes     |
| ZSCAN           |       yes    |      yes     |
| ZRANGEBYLEX     |       yes    |      yes     |
| ZLEXCOUNT       |       yes    |      yes     |
| ZREMRANGEBYLEX  |       yes    |      yes     |  
