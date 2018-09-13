>总结分割的具体步骤

>首先源数据表为
>
	select * from china_city limit 4; --china_city具有全国的省市县 
	select * from lagou_position_bk limit 2; 
	-- lagou_position_bk (工作信息，地区信息，分类信息，公司信息)

>我们先将公司表分割出来,因为公司本身自己是有id的，分离出来会比较简单

	rop table if exists lagou_company;
	create table lagou_company  as select distinct 
	company_id，
	company_short_name,
	company_full_name,
	company_size,
	financestage 
	from lagou_position_bk ;

>在将公司、城市的信息分割出来
>>分割前需要先创建lagou_city表来存放（id,省，市，县）（因为lagou_position_bk 没有自己的地址id所以需要先创建地址表，将id反插到lagou_position_bk中）
	
	-- 373 市 3341 县 总和 3714
	select count(*) from china_city where depth=2;
	select count(*) from china_city where depth=3;

	create table lagou_city as
	select d.id, 
	p.cityName as province,
	c.cityName as city,
	d.cityName as district 
	from (select * from china_city where depth=3) d
    join china_city c on d.parentId = c.id and c.depth=2
    join china_city p on c.parentId = p.id and p.depth=1
	union
	select c.id,
	p.cityName as province,
	c.cityName as city,
	null as district 
	from (select * from china_city where depth=2) c
	join china_city p on c.parentId = p.id and p.depth = 1;

	--我们通过并接的方法将所有的省市县都连接在一起
>通过上面的方法我就可以获得表lagou_city
>>在lagou_position_bk中district列有可能为空所以我们在将公司、城市的信息分割出来时要分两种情况（如果不分就有可能一部分的地址id没有数据）

	-- position 表中 district 为空的数据
	
	select p.*, c.cid from 
	(select * from lagou_position_bk where district is null) p
	join lagou_city c on c.city like concat(p.city, '%') 
	and c.district is null

	-- position 表中 district 不为空的数据
	select p.*, c.cid from 
	(select * from lagou_position_bk where district is not null) p
	join lagou_city c on c.city like concat(p.city, '%')
	and c.district like concat(p.district, '%')

>再将创建表lagou_position将刚刚查询到的东西都放进去，做到分离的效果
	
	create table lagou_position
	as
	select pid, cid as city, company_id as company,
	 position, field, salary_min, salary_max,
	 workyear, education, ptype, pnature, advantage, published_at, updated_at from
	(
	-- position 表中 district 为空的数据
	select p.*, c.cid from (select * from lagou_position_bk where district is null) p
	 join lagou_city c on c.city like concat(p.city, '%') and c.district is null
	
	union all

	-- position 表中 district 不为空的数据
	select p.*, c.cid from 
	(select * from lagou_position_bk where district is not null) p
	join lagou_city c on c.city like concat(p.city, '%')
	 and c.district like concat(p.district, '%')
	) as ppp;
>通过lagou_city表与lagou_position表中的地址相匹配，组合成一张临时表，通过创建一个新的表将需要的字段放进去组合成一张新的表最主要的目的就是要把city列与district列分离出来将lagou_city的city_id列作为外键插入到其中，做到地址表的分离;这样我们就可以做到工作信息，地区信息，分类信息，公司信息分离出来了;



>总结
>>整体的步骤都是比价简单的，我们常常都是边敲代码边思考，常常会把自己带到误区，所以我们应该先想好步骤才开始敲，不然反复的敲反复的删是一件没有意义的事情:





