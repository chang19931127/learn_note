# MYSQL语句 

## 链接

- 内链接

相同的

```sql
SELECT a.user_name,a.over,b.over FROM user1 a INNER JOIN user2 b ON a.user_name=b.user_name;
```

- 左外连接

左全部

```sql
SELECT a.user_name,a.over,b.over FROM user1 a LEFT JOIN user2 b ON a.user_name = b.user_name;
```

- 右外连接

右全部

```sql
SELECT b.user_name,b.over,a.over FROM user1 a RIGHT JOIN user2 b ON a.user_name = b.user_name WHERE a.user_name IS NOT NULL;
```

- 全连接

全部连接

```sql
SELECT a.user_name,a.over,b.over FROM user1 a LEFT JOIN user2 b ON a.user_name = b.user_name UNION ALL SELECT b.user_name,b.over,a.over FROM user1 a RIGHT JOIN user2 b ON a.user_name = b.user_name;
```

- 交叉连接

笛卡尔基，CROSS JOIN 不需要连接条件，实际中用处不大

```sql
SELECT a.user_name,a.over,b.over FROM user1 a CROSS JOIN user2 b ;
```

- 使用join更新表

场景：更新同时存在于两张表中的某个字段,mysql不能更新在from从句中出现的表

```sql
UPDATE user1 a JOIN (SELECT b.user_name FROM user1 a JOIN user2 b ON a.user_name = b.user_name) b ON a.user_name = b. user_name set a.over = '齐天大圣' ;
```

- 使用join来优化子查询

```sql
SELECT a.user_name,a.over,(SELECT over FROM user2 b WHERE a.user_name = b.user_name) AS over2 FROM user1 a ; 
#效率太低
#修改后
SELECT a.user_name,a.over,b.over AS over2 FROM user1 a LEFT JOIN user2 b ON a.user_name = b.user_name;
```

- 使用join来优化聚合子查询

```sql
#查询四人组打怪最多的时间
SELECT a.user_name,b.timestr,b.kills FROM user1 a JOIN user_kills b ON a.id = b.user_id WHERE b.kills = (SELECT MAX(c.kills) FROM user_kill c WHERE c.user_id = b.user_id);
#优化下
SELECT a.user_name,b.timestr,b.kills FROM user1 a JOIN user_kills b ON a.id = b.user_id JOIN user_kills c ON c.user_id = b.user_id GROUP BY a.user_name,b.timestr,b.kills HAVING b.kills = MAX(c.kills);
```

- 如何实现分组选择

什么是分组选择，有一些记录有多个分类，我们需要在某个分类中的某些个数据，就是分组选择。
例如最受欢迎的分类课程，一般情况，先确定分类，再去查询最受欢迎，但是太麻烦

```sql
SELECT a.user_name,b.timestr,b.kills FROM user1 a join user_kills FROM user1 a JOIN user_kills b ON a.id = b.user_id WHERE user_name = '孙悟空' ORDER BY b.kills DESC LIMIT 2；
#取经四人组杀人排行前两条
#孙悟空，猪八戒，沙僧，要执行三次，
#优化后
SELECT d.user_name,c.timestr,kills FROM (SELECT user_id,timestr,kills,(SELECT count(*) FROM user_kills b WHERE b.user_id = a.user_id and a.kills < b.kills) AS cnt FROM user_kills a GROUP BY user_id,timestr,kills) c JOIN user1 d ON c.user_id = d.id WHERE cnt <= 2;
```

## SQL 开发技巧

- 如何进行行列转换


```sql
--进行行转列场景，报表统计,汇总显示,其实可以使用union来连接，但是太麻烦
SELECT a.user_name,sum(kills) FROM user1 a join user_kills b ON a.id = b.user_id GROUP BY a.user_name 
--行转换列
SELECT sum(CASE WHEN user_name = '孙悟空' THEN kills END) as '孙悟空',
       sum(CASE WHEN user_name = '猪八戒' THEN kills END) as '猪八戒',
       sum(CASE WHEN user_name = '沙僧' THEN kills END) as '沙僧'
       FROM user1 a JOIN user_kills b ON a.id = b.user_id;
--进行列转行场景，属性拆分,ETL数据处理
--利用序列表我们需要有一张序列表，就一行整数序列tb_sequence
SELECT user_name,REPLACE(SUBSTRING(SUBSTRING_INDEX(mobile,',',a.id),CHAR_LENGTH(SUBSTRING_INDEX(mobile,',',a.id-1))+1),',','') 
       AS mobile FROM tb_sequence a CROSS JOIN(SELECT user_name,CONCAT(mobile,',') AS mobile,LENGTH(mobile) - LWNGTH(REPLACE(
       mobile,',','')) +1 size FROM user1 b) b ON a.id <= b.size
--另一种结构，提供一个装备表，我们需要查询出来，谁有什么装备 
--装备表结果集
SELECT user_name,arms,clothing,shoe FROM user1 a JOIN user1_equipment b ON a.id = b.user_id;
-- 列转行
select user_name,
case when c.id = 1 then 'arms'
     when c.id = 2 then 'clothing'
     when c.id = 3 then 'shoe'
end as equipment,
coalesce(case when c.id = 1 then arms end,
case shen c.id = 2 then clothing end,
case when c.id = 3 then shoe end) as eq_name
from user1 a
join user1_equipment b on a.id = b.user_id
cross join th_sequence c where c.id <= 3 
order by user_name

```

- 如何生成唯一序列号

```sql
--使用场景，数据库主键，业务序列号，订单号，车票号
--数据库自己也可以生成，mysql 是 AUTO_INCREMENT，优先选择系统提供的序列号生成方式
--创建表的时候，auto_increment mysql 自生成序列，容易产生空洞，就是1,2,3,5   4没了
--使用sql方式来生成 格式YYYYMMDDNNNNNNN
--这里使用的是存储过程
DECLARE v_cnt INT;
DECLARE v_timestr INT;
DECLARE rowcount BIGINT;
SET v_timestr = DATE_FORMAT(NOW(),'%Y%m%d');
SELECT ROUND(RAND()*100,0) + 1 INTO v_cnt;
START TRANSACTION;
     UPDATE order_seq SET order_sn = order_sn + v_cnt WHERE timestr = v_timestr;
     IF ROW_COUNT() = 0 THEN
         INSERT INTO order_seq(timestr,order_sn) VALUES(v_timestr,v_cnt);
     END IF;
     SELECT CONCAT(v_timestr,LPAD(order_sn,7,0)) AS order_sn
         FROM order_seq WHERE timestr = v_timestr;
COMMIT;
```

- 如何删除重复数据

```sql
--如何查询数据是否重复? 利用GROUP BY 和 having从句
select user_name,count(*) from user1_test group by user_name having count(*)>1;
--如何删除重复数据，对于相同数据保留ID最大的
DELETE a FROM user1_test a JOIN(
SELECT user_name,COUNT(*),MAX(id) AS id
FROM user1_test GROUP BY user_name HAVING COUNT(*) > 1) b
ON a.user_name = b.user_name WHERE a.id < b.id;
-- 更加复杂的重复数据删除，例如table 中的mobile 重复了，mobile 中有多个mobile 但是有几个mobile 重复
```

- 如何在子查询中匹配多个值
  
什么是子查询：当一个查询是另外一个查询的条件时，称之为子查询,

常见的子查询场景，由于子查询性能较低

使用子查询可以避免由子查询中的数据产生的重复

```sql
--最终的结果没有重复
select user_name from user1 where id in (select user_id from user_kills);
--连接查询就有重复,所以就需要去重复 distinct
select a.user_name from user1 a join user_kills b on a.id = b.user_id; 
```

使用子查询更加符合语义，更好理解，在子查询中匹配多个值

```sql
 select a.user_name,b.timestr,kills from user1 a join user_kills b on a.id = b.user_id join
 (select user_id,max(kills) as cnt from user_kills group by user_id) c on b.user_id = c.user_id 
 and b.kills = c.cnt;
 --等价这个,mysql中特有的多列过滤子查询
 select a.user_name,b.timestr,kills from user1 a join user_kills b on a.id = b.user_id where
 (b.user_id,b.kills) in (select user_id,max(kills) from user_kills group by user_id);
```

- 解决同一属性多值过滤问题

```sql
-- table中某一个属性有多个值，但是多次出现，并且因为另外一个属性的不同，而情景不同
-- 例如唐僧有几个技能，悟空有几个技能，存到一张表中
-- 查询出，同时具有两个技能的人,同时具有念经和变化
-- 使用join的实现方式 ，几个同时，就要join几次。。。
select a.user_name,b.skill,c.skill from user1 a join user1_skills b on a.id = b.user_id
and b.skill = '念经' join user1_skills c on c.user_id = b.user_id and b.skill = '变化'
where b.skill_level > 0 and c.skill_level > 0;
-- 无敌复杂，求出具有两个技能的人
select a.user_name,b.skill,c.skill,d.skill,e.skill from user1 a
left join user1_skills b on a.id = b.user_id and b.skill = '念经' and b.skill_level > 0
left join user1_skills c on a.id = c.user_id and c.skill = '变化' and c.skill_level > 0
left join user1_skills d on a.id = d.user_id and d.skill = '腾云' and d.skill_level > 0
left join user1_skills e on a.id = e.user_id and e.skill = '浮水' and e.skill_level > 0
where (case when b.skill is not null then 1 else 0 end) +
(case when c.skill is not null then 1 else 0 end) +
(case when d.skill is not null then 1 else 0 end) +
(case when e.skill is not null then 1 else 0 end) >= 2;
-- group by 从句来解决,回想下 找重复
select a.user_name from user1 a join user1_skills b on a.id = b.user_id
where b.skill in('念经','变化','腾云','浮水') and b.skill_level > 0 
group by a.user_name having count(*) >=2;;

```

- 如何计算累进税问题

累进税，不同的区间税率不同，导致结算方式不同

```sql
--使用join实现工资对不同纳税区间的匹配
select a.user_name,money,low,hight,rate from user1 a 
join taxRate b on a.money > b.low 
order by user_name;
--利用least()函数来确定每个区间的纳税额
select user_name,money,low,hight,least(money-low,hight-low) as curmoney,rate
from user1 a join taxRate b on a.money > b.low 
order by user_name,low;
--汇总
select user_name,sum(curmoney * rate) as taxMoney 
from(select user_name,money,low,high,least(money-low,high-money) as curmoney,
rate from user1 a join taxRate b on a.money > b.low) a
group by user_name;
```
## MySQL数据库优化

### SQL及索引优化

- 如何分析SQL查询

    我们这里使用的是MySQL提供的sakila数据库[官方url](http://dev.mysql.com/doc/index-other.html)看看这个网站把
    
- 如何发现有问题的SQL？

    使用MySQL慢查询日志对效率问题进行监控,那么如何开启这个日志尼？
```sql
    show variables like 'show_query_log';
    set global show_query_log_file = 'url';
    set global log_queries_not_using_indexes = on;
    set global long_query_time = 1;
```
