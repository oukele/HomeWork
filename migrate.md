## 总结分割方法
###
###城市表分割
>首先在表s_provinces中,查询所有的市，和县(区)<br/>
>select count(*) from s_provinces where depth=2; # 市 ( 373 )<br/>
>select count(*) from  s_provinces where depth=3;# 县 ( 3341 )<br/>
>这样合并起来 我们拿到的数据应该是 3714条数据<br/>
><br/>
>新建一个 city 表 <br/>
>提取出县(区)的id，省份，城市，县<br/>
>create table city as <br/>
> select x.id,sh.cityName province,s.cityName city,x.cityName district from <br/>
> 	(select * from s_provinces where depth=3)x <br/>
>	inner join s_provinces s on x.parentId = s.id and s.depth=2 <br/>
> 	inner join s_provinces sh on s.parentId = sh.id and sh.depth=1; <br/> 
>select count(*) from city; #结果为 3341 条 说明 这里只是拿到了所有的县<br/>
>提取市结果的语法(因为县的结果我们已经全部提取出来了，所有我们下面的操作就不用查县的了)<br/>
>select s.id, sh.cityName as province, s.cityName as city, null as district from <br/>
>(select * from s_provinces where depth=2)s<br/>
>   inner join  s_provinces sh on s.parentId = sh.id and sh.depth=1;<br/>
>将得到市的结果插入city表中.<br/>
>（经过 as 创建的表 字段设置默认是不能为null 需要重新设置）<br/>
>inser into city <br/>
> select s.id, sh.cityName as province, s.cityName as city, null as district from <br/>
>(select * from s_provinces where depth=2)s<br/>
>   inner join  s_provinces sh on s.parentId = sh.id and sh.depth=1;<br/>
>这样lagou_city表中的数据才是完整的数据.<br/>
>将city表的数据和lagou表中的数据合并，整理一张lagou_city表。<br/>
>提取出我们所需要的数据(使用lagou1(主表) 关联 外表(city) 使用 外表 city值 去匹配 主表的 city 并且县是为null的 )
>create table lagou_city as<br/>
>select pid, id as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at <br/>
>from <br/>
>(select * from lagou1 where district is null) la <br/>
>inner join city c on c.city like concat (la.city,"%") and c.district is null ;<br/>
>再向lagou_city表插入县(district)不为空的数据<br/>
>select pid, id as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at 
>from <br/>
>(select * from lagou1 where district is not null)la<br/>
>inner join city c on c.city like concat(la.city,"%") abd c.district like concat(la.district,"%");<br/>
>这样我们就能用cid和city连接，找到相对应的地区数据。<br/>

>####总结：
>先从基层开始做起，根据需要把大概思路构建出来，不着急，冷静的做。（子查询效率比较高。。）


