##查询出所有广东省的市、县的思路总结
使用递归的方式对自己进行自查询，首先让自己的表取一个别名作为省表，
在使用自己的表的别名作为市表，让省表的id去连接相当于外键市列的parentId，在取别名获得县表，使用市表的id去连接县表的外键列parentId,就可以通过查询省表的名字，就可以查询出省的所有市，所有县;

	select x.id,x.cityName,s.cityName,q.cityName 
	from s_provinces x	join s_provinces s on x.id =s.parentId
	join s_provinces q on q.parentId=s.id where x.cityName='广东省';

若要把市自己自己也带上(因为市自己本身本不带任何县的信息，所以它不会被查询出来)，就要用多表连接union,用同样的方法就可以把省下面的市全部查询出来;
>union会把重复的数据去除，而union all会把所有数据显示出来

	select x.id,x.cityName,s.cityName,null cityName 
	from s_provinces x left join s_provinces s
	on x.id=s.parentId  where x.cityName='广东省'
	union all
	select x.id,x.cityName,s.cityName,q.cityName from s_provinces x
	left join s_provinces s on s.parentId=x.id
	left join s_provinces q on q.parentId=s.id where x.cityName='广东省';