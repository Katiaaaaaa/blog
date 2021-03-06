---
title: MySQL题库(4)
date: 2020-10-09
tags: ["数据库","数据分析","MySQL-union","MySQL-all"]
categories: ["MySQL"]
author: "Katia"
---

> * union
> * 最值 >all()

<!--more-->


| Column Name   | Type    |
|  :-------: | :-----------:|
| movie_id      | int     |
| title         | varchar |

movie_id 是这个表的主键。
title 是电影的名字。
表：Users


| Column Name   | Type    |
|  :-------: | :-----------:|
| user_id       | int     |
| name          | varchar |

user_id 是表的主键。
表：Movie_Rating


| Column Name   | Type    |
|  :-------: | :-----------:|
| movie_id      | int     |
| user_id       | int     |
| rating        | int     |
| created_at    | date    |

(movie_id, user_id) 是这个表的主键。
这个表包含用户在其评论中对电影的评分 rating 。
created_at 是用户的点评日期。 
 

请你编写一组 SQL 查询：

查找评论电影数量最多的用户名。  
如果出现平局，返回字典序较小的用户名。    

查找在 2020 年 2 月 平均评分最高 的电影名称。    
如果出现平局，返回字典序较小的电影名称。    

查询分两行返回，查询结果格式如下例所示：  

Movies 表：

| movie_id    |  title       |
|  :-------: | :-----------:| 
| 1           | Avengers     |
| 2           | Frozen 2     |
| 3           | Joker        |


Users 表：

| user_id     |  name        |
|  :-------: | :-----------:| 
| 1           | Daniel       |
| 2           | Monica       |
| 3           | Maria        |
| 4           | James        |


Movie_Rating 表：

| movie_id    | user_id      | rating       | created_at  |
|  :-------: | :-----------:| :-----------:| :-----------:|
| 1           | 1            | 3            | 2020-01-12  |
| 1           | 2            | 4            | 2020-02-11  |
| 1           | 3            | 2            | 2020-02-12  |
| 1           | 4            | 1            | 2020-01-01  |
| 2           | 1            | 5            | 2020-02-17  | 
| 2           | 2            | 2            | 2020-02-01  | 
| 2           | 3            | 2            | 2020-03-01  |
| 3           | 1            | 3            | 2020-02-22  | 
| 3           | 2            | 4            | 2020-02-25  | 


Result 表：

| results      |
| :-----------:|
| Daniel       |
| Frozen 2     |


Daniel 和 Monica 都点评了 3 部电影（"Avengers", "Frozen 2" 和 "Joker"） 但是 Daniel 字典序比较小。  
Frozen 2 和 Joker 在 2 月的评分都是 3.5，但是 Frozen 2 的字典序比较小。  


1. union 两个表比较复杂的时候，作为虚拟表再连接



```sql
select name  as result from 
(select name from users u 
inner join movie_rating m on u.user_id = m.user_id
group by name 
having count(distinct movie_id)>=all(select count(*) from movie_rating group by user_id) order by name )a 

union 

select title from 
(select title as result from movies m 
inner join movie_rating mr on m.movie_id = mr.movie_id
where date_format(created_at, '%Y-%m') = '2020-02'
group by mr.movie_id
having avg(rating)>=all(select avg(rating) from movie_rating group by movie_id)
order by title) b 
;
```
