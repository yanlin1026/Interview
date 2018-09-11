1.查找最晚入职员工的所有信息  
```sql
select * from employees order by hire_date desc limit 1;
```  

2.查找入职员工时间排名倒数第三的员工所有信息  
```sql
select * from employees order by hire_date desc limit 2,1;
```

3.
