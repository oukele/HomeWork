
### 查询出所有广东省的市、县的思路总结
 
> select sh.cityName 省,s.cityName 市,x.cityName 县 <br/>from s_provinces x <br/>
>	inner join s_provinces s on x.parentId = s.id <br/>
>	inner join s_provinces sh on s.parentId = sh.id <br/>
>	where sh.cityName="广东省"; <br/><br/>
> 当一个表存在递归关系时，我们首先从最基层开始进行查询 <br/>
>*我们先关联 县和市 的查询  select * from s_provinces x inner join s_provinces s on x.parentId=s.id;<br/>
>*这样引用 s_provinces 表里的 parendId 和 id 关联 定位到 子和父的关系。<br/>
>*然后就可以按照自已的需要进行查询了。
