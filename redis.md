## try redis basic commands
https://try.redis.io/

## commands
Delete All Keys In Redis
```
redis-cli FLUSHALL
OR
redis-cli FLUSHDB
OR
redis-cli -n <database_number> FLUSHDB
```

list all keys
```
KEYS *
```

## high concurrency lock competation
https://zhuanlan.zhihu.com/p/52756935  
The solution is to put them in the queue to enforce them in a sequence. Would it be slow? //TODO




