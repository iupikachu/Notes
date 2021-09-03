## sql 做题总结



24.**对所有员工的薪水按照salary进行按照1-N的排名**

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20210609100020751.png" alt="image-20210609100020751" style="zoom:67%;" />



解法1：

自连接

```mysql
select 
    s1.emp_no , 
    s1.salary , 
    count(distinct s2.salary) as t_rank 
    from salaries as s1 inner join 
    salaries s2 
on s1.salary <= s2.salary 
where 
    s1.to_date = '9999-01-01'
AND 
    s2.to_date = '9999-01-01'
group by 
    s1.emp_no
order by 
    s1.salary desc,
    s1.emp_no asc;
```



解法2: 

窗口函数中的排序专用窗口函数

a a a b

1. RANK()  

   1 1 1 4

2. DENSE_RANK()

   1 1 1 2

3. ROW_NUMBER()

   1 2 3 4

用法: dense_rank() over (排序) as 结果

```sql
select 
    emp_no , 
    salary , 
    dense_rank() over (order by salary desc) as t_rank
from salaries
where
    to_date = '9999-01-01'
order by 
    t_rank asc,
    emp_no asc;
```





25. **获取员工其当前的薪水比其manager当前薪水还高的相关信息**

    分别得到经理工资表 和 员工工资表 用dept_no 连接两表 where加上筛选条件

```sql
select 
    emp_no,
    manager_no,
    emp_salary,
    manager_salary
from
(select 
    dm.emp_no as manager_no,
    dm.dept_no,
    s.salary as manager_salary
from
    dept_manager as dm,
    salaries as s
where
    dm.emp_no = s.emp_no
and dm.to_date = '9999-01-01'
and s.to_date = '9999-01-01'
) as msg_sal
inner join
(select 
    de.emp_no,
    de.dept_no,
    salary as emp_salary
from
    dept_emp as de,
    salaries as s
where
    de.emp_no = s.emp_no
and de.to_date = '9999-01-01'
and s.to_date = '9999-01-01'
) as emp_sal
on
    msg_sal.dept_no = emp_sal.dept_no
where
    emp_sal.emp_salary > msg_sal.manager_salary;
```





26.**汇总各个部门当前员工的title类型的分配数目**

 三表连接，题目要求是部门下的员工的title以及title的数目

所以要分组，对 部门，title 进行按顺序分组



按照原则: 除聚集计算语句外，select 语句中的每个列都必须在 group by 子句中给出 

select 有 dept_name，所以分组里也写上（其实不写也没事）

最后再order by 排序一下

```sql
select 
    de.dept_no,
    d.dept_name,
    t.title,
    count(de.dept_no) as count
from
    dept_emp as de,
    departments as d,
    titles as t
where
    de.emp_no = t.emp_no
and 
    de.dept_no = d.dept_no
group by 
    de.dept_no,
    d.dept_name,
    t.title
order by
    dept_no asc;
```



28.【较难】查找描述信息中包含robot的电影对应的分类名称以及电影数目，而且还需要该分类包含电影总数量>=5部



先对 film_category 进行筛选，选出分类包含电影 >= 5的 category_id 然后就简单了。 

```sql
select 
    c.name,
    count(fc.film_id)
from
    film as f,
    category as c,
    film_category as fc
where
    f.description like '%robot%'
and
    f.film_id = fc.film_id
and
    c.category_id = fc.category_id
and
    fc.category_id in (
    select
        category_id
    from
        film_category
    group by category_id having count(film_id) >= 5
    )
```





29.**使用join查询方式找出没有分类的电影id以及名称**

```sql
select 
    f.film_id,
    title
from
    film as f
left join 
    film_category as fc
on
    f.film_id = fc.film_id
where
    fc.category_id is null
```

