
# unix_timestamp在hive中的不同

```
hive> select unix_timestamp('20100110');
NULL

hive> select unix_timestamp('20100110 00:00:00');
NULL

hive> select unix_timestamp('2010-01-10');
NULL

#只有下面的这种写法可以拿到时间戳，其他的情况都不能拿到时间戳
hive> select unix_timestamp('2010-01-10 00:00:00');
1263052800
```