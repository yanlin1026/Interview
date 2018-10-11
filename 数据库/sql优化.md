1. 考虑在 where 及 order by 涉及的列上建立索引
2. 建索引需要慎重考虑，视具体情况而定，一个表的索引数最好不要超过6个
3. 只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。
4. 尽可能的使用 varchar 代替 char ，因为首先变长字段存储空间小，可以节省存储空间，
# 几种放弃索引全文检索的情况
1. where字句字段null值判断
```sql
select id from t where num is null # 错误
select id from t where num = 0
```
2. where字句使用!=，<>操作符
3. where字句使用or连接
```sql
select id from t where num=10 or num=20	# 错误
select id from t where num=10	union all	select id from t where num=20
```
4. in 和 not in 也要慎用
```sql
select id from t where num=10 or num=20	# 错误
select id from t where num=10 union all	select id from t where num=20
```
5. like 中通配符的使用
```sql
select id from tabel where name like'%UncleToo%' # 错误
select id from tabel where name like'%UncleToo'	# 错误
select id from tabel where name like'UncleToo%'
```
6. 避免在 where 子句中对字段进行表达式操作
```sql
select name from table where id / 2 = 100 # 错误
select name from table where id = 100 * 2
```
7. 避免在 where 子句中对字段进行函数操作。
```sql
# 不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算
select id from table where datediff(day,datefield,'2014-07-17') >= 0
select id from table where datefield <= '2014-07-17'
```
8. 在子查询中，用 exists 代替 in 是一个好的选择。
```sql
select name from a where id in(select id from b)
select name from a where exists(select 1 from b where id = a.id)
```
