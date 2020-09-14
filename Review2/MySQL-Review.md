### MySQL

在使用msyql进行模糊查询的时候，很自然的会用到like语句，通常情况下，在数据量小的时候，不容易看出查询的效率，但在数据量达到百万级，千万级的时候，查询的效率就很容易显现出来。

一般情况下like模糊查询的写法为（field已建立索引）：

```sql
SELECT `column` FROM `table` WHERE `field` like '%keyword%';
```

> 上面的语句用explain解释来看，SQL语句并未用到索引，而且是全表搜索，如果在数据量超大的时候，可想而知最后的效率会是这样

对比下面的写法：

```sql
SELECT `column` FROM `table` WHERE `field` like 'keyword%';
```

> 这样的写法用explain解释看到，SQL语句**使用了索引**，搜索的效率大大的提高了！

但是有的时候，我们在做模糊查询的时候，并非要想查询的关键词都在开头，所以如果不是特别的要求，"keywork%"并不合适所有的模糊查询

​	怎么办?

--------------------------------------------------------------

- **LOCATE(substr,str)** 或者 **LOCATE(substr,str,pos)**

  第一个语法返回substr在字符串str 的第一个出现的位置。

  第二个语法返回子符串 substr 在字符串str，从pos处开始的第一次出现的位置。如果substr 不在str 中，则返回值为0 

  ```sql
  SELECT LOCATE('xbar',`foobar`); 
  ###返回0 
  
  SELECT LOCATE('bar',`foobarbar`); 
  ###返回4
  
  SELECT LOCATE('bar',`foobarbar`,5);
  ###返回7
  
  SELECT `column` FROM `table` WHERE LOCATE('keyword', `field`)>0
  ```

  > 备注：keyword是要搜索的内容，field为被匹配的字段，查询出所有存在keyword的数据



- **POSITION('substr' IN field)**

  position可以看做是locate的别名，功能跟locate一样

  ```sql
  SELECT `column` FROM `table` WHERE POSITION('keyword' IN `filed`)
  ```

- **INSTR(str,'substr')**

  ```sql
  SELECT `column` FROM `table` WHERE INSTR(`field`, 'keyword' )>0 
  ```



> 使用like，还是使用locate  position高效的前提条件是查询的字段上面已经建立起了索引





写的顺序：select ... from... where.... group by... having... order by..

执行顺序：from... where...group by... having.... select ... order by...                                    



sql 中order by 与group by的顺序 是：

select
 from
 where
 group by
 order by

> 注意：group by 比order by先执行，order by不会对group by 内部进行排序，如果group by后只有一条记录，那么order by 将无效。要查出group by中最大的或最小的某一字段使用 max或min函数。

例：

```sql
select 
sum(click_num) as totalnum,
max(update_time) as update_time,
count(*) as totalarticle 
from article_detail 
where userid =1 
group by userid 
order by update_time 
desc
```

