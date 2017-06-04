# mysql行转列，列转行问题

## 1.列转行

有下面这样一张表

```
mysql> select * from tb_paidashi_pay_model_day where dt=20170516 ;
+------+----------+------+---------+------------+------------+----------+
| id   | platform | type | paytype | totalpayer | totalmoney | dt       |
+------+----------+------+---------+------------+------------+----------+
| 2667 | android  |    1 |      20 |         40 |      55503 | 20170516 |
| 2668 | android  |    1 |      70 |        106 |     150006 | 20170516 |
| 2669 | android  |    2 |      20 |         11 |      31502 | 20170516 |
| 2670 | android  |    2 |      70 |         22 |      77000 | 20170516 |
| 2671 | android  |    3 |      20 |          2 |       8001 | 20170516 |
| 2672 | android  |    3 |      70 |          7 |      40002 | 20170516 |
| 2673 | android  |    4 |      20 |          4 |          4 | 20170516 |
| 2674 | android  |    4 |      70 |          2 |      15001 | 20170516 |
| 2675 | ios      |    1 |      70 |         17 |      32500 | 20170516 |
| 2676 | ios      |    2 |      20 |          1 |       2800 | 20170516 |
| 2677 | ios      |    2 |      70 |          7 |      31600 | 20170516 |
| 2678 | pc       |    2 |      70 |          1 |       3900 | 20170516 |
+------+----------+------+---------+------------+------------+----------+
12 rows in set (0.00 sec)

```

现在需要将下面的查询结果**进行列转行**

```

mysql> select type, sum(1) sum from tb_paidashi_pay_model_day where dt=20170516 group by type;
+------+------+
| type | sum  |
+------+------+
|    1 |    3 |
|    2 |    5 |
|    3 |    2 |
|    4 |    2 |
+------+------+
4 rows in set (0.00 sec)

```

使用count统计

```
    SELECT
    count(case type  when 1 then 1 else NULL END) as  'type_1',
    count(case type  when 2 then 1  else NULL END) as  'type_2',
    count(case type  when 3 then 1 else NULL END) as  'type_3',
    count(case type  when 4 then 1 else NULL END) as  'type_4'
    from tb_paidashi_pay_model_day where dt=20170516 ;
+--------+--------+--------+--------+
| type_1 | type_2 | type_3 | type_4 |
+--------+--------+--------+--------+
|      3 |      5 |      2 |      2 |
+--------+--------+--------+--------+
1 row in set (0.00 sec)
```

这里需要注意的问题是count遇到null的时候，会不去统计，如果我们将null变成0，那么结果如下：
```
   SELECT
	  count(case type  when 1 then 1 else 0 END) as  'type_1',
	  count(case type  when 2 then 1  else 0 END) as  'type_2',
	  count(case type  when 3 then 1 else 0 END) as  'type_3',
	  count(case type  when 4 then 1 else 0 END) as  'type_4'
    from tb_paidashi_pay_model_day where dt=20170516 ;
+--------+--------+--------+--------+
| type_1 | type_2 | type_3 | type_4 |
+--------+--------+--------+--------+
|     12 |     12 |     12 |     12 |
+--------+--------+--------+--------+
1 row in set (0.00 sec)
```
出现上面的结果是因为count将非null做了统计，尽管是0，count也是会计数的

但是我们如果使用sum，那么结果如下：

```
  SELECT
	 sum(case type  when 1 then 1 else 0 END) as  'type_1',
	 sum(case type  when 2 then 1  else 0 END) as  'type_2',
	 sum(case type  when 3 then 1 else 0 END) as  'type_3',
	 sum(case type  when 4 then 1 else 0 END) as  'type_4'
  from tb_paidashi_pay_model_day where dt=20170516 ;
+--------+--------+--------+--------+
| type_1 | type_2 | type_3 | type_4 |
+--------+--------+--------+--------+
|      3 |      5 |      2 |      2 |
+--------+--------+--------+--------+
1 row in set (0.00 sec)



 SELECT
 sum(case type  when 1 then 1 else null END) as  'type_1',
 sum(case type  when 2 then 1  else null END) as  'type_2',
 sum(case type  when 3 then 1 else null END) as  'type_3',
 sum(case type  when 4 then 1 else null END) as  'type_4'
 from tb_paidashi_pay_model_day where dt=20170516 ;
+--------+--------+--------+--------+
| type_1 | type_2 | type_3 | type_4 |
+--------+--------+--------+--------+
|      3 |      5 |      2 |      2 |
+--------+--------+--------+--------+
1 row in set (0.00 sec)

```

## 2.行转列

参见：http://www.cnblogs.com/lhj588/p/3315876.html
