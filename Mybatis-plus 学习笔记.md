## Mybatis-plus 学习笔记     (摘自bilibili狂神说)

<img src="/Users/chenqipeng/Desktop/4646B2CB40EF691B1778461842BB1F82.jpg" alt="4646B2CB40EF691B1778461842BB1F82" style="zoom:50%;" />

### 1. 简介

所有的crud都不需要写了，偷懒神器。

官网地址：https://baomidou.com/

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201112162103614.png" alt="image-20201112162103614" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201112165251370.png" alt="image-20201112165251370" style="zoom:50%;" />



[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

[![img](https://img.alicdn.com/tfs/TB1_rYHo7P2gK0jSZPxXXacQpXa-690-388.jpg)](https://www.aliyun.com/1212/2019/home?userCode=5wbjwd1y)

愿景

我们的愿景是成为 MyBatis 最好的搭档，就像 [魂斗罗](https://baomidou.com/img/contra.jpg) 中的 1P、2P，基友搭配，效率翻倍。

![img](https://baomidou.com/img/relationship-with-mybatis.png)

> 特性

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作  （BaseMapper）
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求，简单的crud不用自己编写了。
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题 
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

> 支持数据库

- mysql 、mariadb 、oracle 、db2 、h2 、hsql 、sqlite 、postgresql 、sqlserver 、presto 、Gauss 、Firebird
- Phoenix 、clickhouse 、Sybase ASE 、 OceanBase 、达梦数据库 、虚谷数据库 、人大金仓数据库 、南大通用数据库 



### 2. 快速入门

地址：https://baomidou.com/guide/quick-start.html

使用第三方组件：

1. 导入对应的依赖
2. 研究依赖如何编写
3. 代码如何编写
4. 提高扩展技术能力

> 步骤 

1.创建数据库**mybatis_plus**

2.创建user表

```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
	id BIGINT(20) NOT NULL COMMENT '主键ID',
	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
	PRIMARY KEY (id)
);
```

**data脚本**

```sql
DELETE FROM user;

INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');

# 真实开发中还需要  version（乐观锁）, deleted（逻辑删除）, gmt_create （创建时间）, gmt_modified （修改时间）
```

3. 编写项目

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201112214003190.png" alt="image-20201112214003190" style="zoom:50%;" />



4.导入依赖

```xml
<!--数据库驱动  -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<!--lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<!--mybatis-plus-->
<!--是自己开发的，并非官方的！-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.5</version>
</dependency>
```



ps ：我们使用mybatis-plus可以节省大量的代码。尽量不要同时导入 mybatis 和 mybatis-plus ! 会有版本的差异。

5. 连接数据库，这一步和mybatis一样。

​    application.properties

```properties
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#mysql 8 需要增加时区的配置 serverTimezone=GMT%2B8
```



6. 传统方法 pojo-dao(连接mybatis，配置mapper.xml文件) service controller

6. **现在用mybatis_plus后**

* pojo

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {

    private Long id;
    private String name;
    private Integer age;
    private String  email;
}
```

* mapper接口

```java
//在对应的mapper上面继承基本的类 BaseMapper
@Repository
public interface UserMapper extends BaseMapper<User> {
    //所有的crud都已经编写完成了

}
```

 

**别忘了加上扫描接口**



```java
@MapperScan("com.cqp.mapper")
@SpringBootApplication
public class MybatisPlusApplication {

    public static void main(String[] args) {
        SpringApplication.run(MybatisPlusApplication.class, args);
    }

}
```

* 测试

![image-20201114092736315](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114092736315.png)





### 3. 配置日志

 为了开发时可以看到sql的执行情况，项目上线时，再把日志去掉。

```properties
# 配置日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```

![image-20201114094748825](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114094748825.png)



### 4. CRUD扩展

#### 4.1. 插入

```java
//测试插入
@Test
public void testInsert(){
    User user=new User();
    user.setName("cqp");
    user.setEmail("2111053717@qq.com");
    user.setAge(24);
    int result = userMapper.insert(user);
    System.out.println(result);
    System.out.println(user);
}
```



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114102634776.png" alt="image-20201114102634776" style="zoom:50%;" />

> 数据库插入的id的默认值为：全局唯一的id      mybatis-plus默认的 @TableId(type = IdType.ID_WORKER)  

为什么有这么一串长长的id ?

**主键生成策略(uuid 自增id 雪花算法 redis zookeeper)**



SnowFlake 算法，是 Twitter 开源的分布式 id 生成算法。其核心思想就是：使用一个 64 bit 的 long 型的数字作为全局唯一 id。在分布式系统中的应用十分广泛，且ID 引入了时间戳，基本上保持自增。

教程链接 https://blog.csdn.net/lq18050010830/article/details/89845790



> 主键自增 

1. 实体类字段上 

```java
@TableId(type = IdType.AUTO)
private Long id;
```

2.  数据库字段一定要是自增的

​       <img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114105515869.png" alt="image-20201114105515869" style="zoom:50%;" />



> 其余的源码解释

```java
AUTO(0),       //数据库id自增
NONE(1),       //未设置主键
INPUT(2),      //手动输入
ID_WORKER(3),  //默认的全局唯一id
UUID(4),       //全局唯一id  uuid
ID_WORKER_STR(5);    // ID_WORK字符串表示法
```





#### 4.2. 更新

##### 自动填充

创建时间，修改时间！这些操作一般都是自动化完成的，我们不希望手动更新。

阿里巴巴开发手册： 所有的数据库表 gmt_create gmt_modified 几乎所有的表都要配上！而且需要自动化。



1 . 数据库加上时间字段

![image-20201114112014316](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114112014316.png)

2. pojo

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    @TableId(type = IdType.AUTO)
    private Long id;
    private String name;
    private Integer age;
    private String  email;
    
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
}
```



3. 编写处理器来处理这个注解即可

​       新建handler包  再新建class MyMetaObjectHandler 

```java
@Slf4j
@Component
public class MyMetaObjectHandler implements MetaObjectHandler {
    //插入时的填充策略
    @Override
    public void insertFill(MetaObject metaObject) {
        log.info("start fill insert");
        this.setFieldValByName("createTime",new Date(),metaObject);
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
    //更新时的填充策略
    @Override
    public void updateFill(MetaObject metaObject) {
        log.info("start fill update");
        this.setFieldValByName("updateTime",new Date(),metaObject);
    }
}
```



##### 乐观锁



> 乐观锁: 顾名思义十分乐观，总是认为不会出现什么问题，无论干什么都不去上锁，如果出现了问题，再次更新值测试。
>
> 悲观锁: 顾名思义十分悲观，总是认为会出问题，无论干什么都会上锁。再去操作。



version 、 new version

当要更新一条记录的时候，希望这条记录没有被别人更新
乐观锁实现方式：

- 取出记录时，获取当前version
- 更新时，带上这个version
- 执行更新时， set version = newVersion where version = oldVersion
- 如果version不对，就更新失败



1.改变表 增加version字段

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114143943532.png" alt="image-20201114143943532" style="zoom:50%;" />

2. 实体类加对应的字段  并且加上mybatis-plus的乐观锁注解

```java
@Version   
private Integer version;
```

3. 注册组件

​       新建 config文件夹 新建 MyBatisPlusConfig类

```java
@MapperScan("com.cqp.mapper")
@Configuration  //配置类
public class MyBatisPlusConfig {

    //注册乐观锁插件
    @Bean
    public OptimisticLockerInterceptor optimisticLockerInterceptor(){
        return new OptimisticLockerInterceptor();
    }
}
```

4.测试一下

```java
//测试乐观锁成功的情况
@Test
public void testOptimisticLocker(){
    //1.查询用户信息
    User user=userMapper.selectById(1L);
    //2.修改用户信息
    user.setName("小智");
    user.setAge(11);
    //3.执行更新操作
    userMapper.updateById(user);
}
```



![image-20201114150733257](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114150733257.png)



![image-20201114150814734](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114150814734.png)





ps : 报错数据库储存编码问题

解决方法：

> ```sql
> ALTER TABLE user CONVERT TO CHARACTER SET utf8;
> ```



**测试乐观锁失败的情况**

```java
//测试乐观锁失败的情况
@Test
public void testOptimisticLocker2(){
    //1.查询用户信息
    User user1=userMapper.selectById(1L);
    //2.修改用户信息
    user1.setName("小智1");


    User user2 = userMapper.selectById((1L));
    user2.setName("小智2");

    userMapper.updateById(user2);
    //3.执行更新操作
    userMapper.updateById(user1);
}
```

![image-20201114151322242](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114151322242.png)

  

由于小智2插队更新了数据 导致version+1=2  所以小智1的更新因为version导致更新失败。

![image-20201114151440391](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201114151440391.png)





#### 4.3 查询

```java
// 测试批量查询
@Test
public void testSelectById(){
    List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
    users.forEach(System.out::println);
}

//条件查询 map
@Test
public void testByMap(){
    HashMap<String,Object> map = new HashMap<>();
    map.put("name","cqp");  // 查询 name = cqp 的数据
    map.put("age",3);
    List<User> users = userMapper.selectByMap(map);
    users.forEach(System.out::println);
}
```



##### **分页查询**

1. 原始的 limit 进行分页

2. pagehelper第三方插件
3. MyBatis_plus 也内置了分页插件



**如何使用？**

1. 配置拦截器组件即可 在**MyBatisPlusConfig**配置类里面配置

   

```java
@Bean
public PaginationInterceptor paginationInterceptor() {
    PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
    return paginationInterceptor;
}
```

2.直接使用page对象

```java
// 测试分页查询
@Test
public void testPage(){
    // 参数一 当前页
    // 参数二 页面大小
    Page<User> page = new Page<>(1,5);
    userMapper.selectPage(page,null);
    page.getRecords().forEach(System.out::println);
}
```



#### 4.4 删除

 

> 逻辑删除

管理员可以查看被删除的记录！防止数据的丢失，类似于回收站。

1.在数据表中增加一个deleted字段

```sql
alter table user
   add deleted int(1)  DEFAULT 0  comment '逻辑删除';
```

2.实体类中增加属性

```java
//逻辑删除
@TableLogic
private Integer deleted;
```

3. 在**MyBatisPlusConfig**中配置逻辑删除组件

```java
//逻辑删除组件
@Bean
public ISqlInjector sqlInjector(){
    return new LogicSqlInjector();

}
```

4. 在properties里进行配置

```properties
#逻辑删除的配置
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

5.测试一下删除

```java
@Test
public void testDelete(){
    userMapper.deleteById(1L);
}
```

![image-20201116144942433](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201116144942433.png)



mybatis_plus很智能的查询时不会去查询被逻辑删除的数据。

### 5. 性能分析插件



mybatis_plus 也提供性能分析插件，如果超过这个时间就停止运行。

作用: 性能分析拦截器 , 用于输出每条SQL语句及其执行时间。

1. 导入插件
2. 测试使用

由于新版本移除了这个插件。而且druid也可以进行这项工作。



### 6.条件构造器 （Wrapper）

十分重要 

我们写一些复杂的sql，就用它来替代



> 测试一

```java
@Test
void contextLoads(){
    //查询name不为空的用户，并且邮箱不为空的用户，年龄大于等于12
    QueryWrapper<User> wrapper = new QueryWrapper<User>();
    wrapper
            .isNotNull("name")
            .isNotNull("email")
            .ge("age",12);
    userMapper.selectList(wrapper).forEach(System.out::println);
}
```

> 测试二

```java
@Test
void Test2(){
    //查询cqp
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper
            .eq("name","iu");
    User user = userMapper.selectOne(wrapper); // 查询一个数据，出现多个结果使用list或者map
    System.out.println(user);
}
```

> 测试三

```java
@Test
void Test3(){
    //查询年龄在20-30之间的用户
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.between("age",20,30);
    Integer count = userMapper.selectCount(wrapper);
    System.out.println(count);

}
```

> 测试四

```java
@Test
void Test4(){
    //模糊查询名字没有e 邮箱t开头的用户
    QueryWrapper<User> wrapper =new QueryWrapper<>();
    wrapper
            .notLike("name","e")
            .likeRight("email","t");
    List<Map<String, Object>> maps = userMapper.selectMaps(wrapper);
    maps.forEach(System.out::println);
}
```

> 测试五

```java
@Test
void Test5(){
    // 子查询
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper.inSql("id","select id from user where id < 3");
    List<Object> objects = userMapper.selectObjs(wrapper);
    objects.forEach(System.out::println);
    /*
    SELECT id,name,age,email,deleted,version,create_time,update_time 
    FROM user 
    WHERE deleted=0 
    AND id IN (select id from user where id < 3) 
     */
}
```

> 测试六

```java
@Test
void Test6(){
    // 通过id 进行排序  从大到小
    QueryWrapper<User> wrapper = new QueryWrapper<>();
    wrapper
            .orderByDesc("id");
    List<User> users = userMapper.selectList(wrapper);
    users.forEach(System.out::println);

}
```



> 项目代码

```java
public PageUtils fuzzyQueryByName(Map<String, Object> params, String name) {
    QueryWrapper<GatewayEntity> queryWrapper = new QueryWrapper<GatewayEntity>();
    queryWrapper.lambda().like(GatewayEntity::getName, name);
//        QueryWrapper<GatewayEntity> queryWrapper = new QueryWrapper<GatewayEntity>();
//        queryWrapper
//                .like("name",name);
    IPage<GatewayEntity> page = this.page(
            new Query<GatewayEntity>().getPage(params),
            queryWrapper
    );
    return new PageUtils(page);
}
```

```java
@Override
public PageUtils queryPage(Map<String, Object> params) {
    IPage<PrincipalEntity> page = this.page(
            new Query<PrincipalEntity>().getPage(params),
            new QueryWrapper<PrincipalEntity>()
    );

    return new PageUtils(page);
}
```







### 7.代码自动生成器



**AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。**



例子 随便新建一个java类即可

```java
package com.cqp;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableField;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.ArrayList;

//代码自动生成器
public class cqpCode {

    public static void main(String[] args) {
        //需要构建一个代码自动生成器 对象
        AutoGenerator mpg = new AutoGenerator();
        //配置策略

        //1、全局配置
        GlobalConfig gc = new GlobalConfig();
        //获得项目路径
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath+"/src/main/java");
        gc.setAuthor("cqp");
        // 不打开资源管理器 就是生成完代码之后自动帮你打开代码的文件夹
        gc.setOpen(false);
        // 不覆盖原来生成的
        gc.setFileOverride(false);
        gc.setServiceName("%sService"); //去service的I前缀
        gc.setIdType(IdType.ID_WORKER);
        gc.setDateType(DateType.ONLY_DATE);
        gc.setSwagger2(true);
        mpg.setGlobalConfig(gc);

        // 2.设置数据源
        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("root");
        dsc.setPassword("123456");
        dsc.setDbType(DbType.MYSQL);

        mpg.setDataSource(dsc);

        //3.包的配置
        PackageConfig pc = new PackageConfig();
        pc.setModuleName("blog");
        pc.setParent("com.cqp"); //com.cqp下新建一个blog的module
        pc.setEntity("pojo");   // pojo包
        pc.setMapper("mapper"); // mapper包
        pc.setService("service"); // service层
        pc.setController("controller");  //

        mpg.setPackageInfo(pc);

        //4.策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("user"); //设置要映射的表名
        strategy.setNaming(NamingStrategy.underline_to_camel); // 下划线转驼峰命名
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        strategy.setEntityLombokModel(true); // 自动lombok
        strategy.setLogicDeleteFieldName("deleted");  //让数据库表中的deleted属性变成逻辑删除的属性
        // 自动填充配置
        TableFill createime = new TableFill("create_time", FieldFill.INSERT);
        TableFill updateTime = new TableFill("update_time", FieldFill.INSERT_UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<>();
        tableFills.add(createime);
        tableFills.add(updateTime);
        strategy.setTableFillList(tableFills);

        // 乐观锁
        strategy.setVersionFieldName("version");
        strategy.setRestControllerStyle(true);//开启restful风格的驼峰命名
        strategy.setControllerMappingHyphenStyle(true); //localhost:8080/hello_id_2

        mpg.setStrategy(strategy);


        mpg.execute();  // 执行
    }
}
```

也可以一次生成多张表的对应代码

```java
 strategy.setInclude("user","equipment","gateway"); //设置要映射的表名
```









![image-20201118132646996](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118132646996.png)









如果报错

```
 java.lang.NoClassDefFoundError: org/apache/velocity/context/Context
```

原因是缺少了依赖，解决方案如下：

pom.xml文件当中加入velocity的依赖

    <!-- 模板引擎 -->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.0</version>
        </dependency>


现在依靠这个代码生成器进行测试。以后可以用这个写自己的项目！！！

1.导包

```xml
<!--数据库驱动  -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<!--lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<!--mybatis-plus-->
<!--是自己开发的，并非官方的！-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.0.5</version>
</dependency>

<!-- 模板引擎 -->
<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity-engine-core</artifactId>
    <version>2.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.swagger/swagger-annotations -->
<dependency>
    <groupId>io.swagger</groupId>
    <artifactId>swagger-annotations</artifactId>
    <version>1.5.22</version>
</dependency>
```



2. 编写application.properties

```java
# 数据库连接配置
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#mysql 8 需要增加时区的配置 serverTimezone=GMT%2B8

# 配置日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
```



3. 新建cqpCode(代码生成类)并执行。代码上面已经给出

![image-20201118201954004](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118201954004.png)





4. 编写测试代码并加上注解



> service层

![image-20201118202054464](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202054464.png)



![image-20201118202135516](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202135516.png)

> dao层

![image-20201118202207764](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202207764.png)



> controller 层

![image-20201118202300416](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202300416.png)

>启动类



![image-20201118202440581](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202440581.png)

> 测试成功！！！  以后可以用这个代码生成器写自己的博客项目了！

![image-20201118202532811](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202532811.png)









ps： 在Test里面测试dao层能否连上数据库并取得数据

![image-20201118202638065](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201118202638065.png)



### 8.资料



```sql
DROP TABLE IF EXISTS user;

CREATE TABLE user
(
   id BIGINT(20) NOT NULL COMMENT '主键ID',
   name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
   age INT(11) NULL DEFAULT NULL COMMENT '年龄',
   email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
   PRIMARY KEY (id)
);



INSERT INTO user (id, name, age, email) VALUES
(1, 'Jone', 18, 'test1@baomidou.com'),
(2, 'Jack', 20, 'test2@baomidou.com'),
(3, 'Tom', 28, 'test3@baomidou.com'),
(4, 'Sandy', 21, 'test4@baomidou.com'),
(5, 'Billie', 24, 'test5@baomidou.com');

alter table user modify id bigint auto_increment comment '主键ID';

alter table user
   add create_time datetime null comment '创建时间';

alter table user
   add update_time datetime null comment '更新时间';

alter table user
   add version int(10) null comment '乐观锁' default 1;


ALTER TABLE user CONVERT TO CHARACTER SET utf8;

alter table user
   add deleted int(1)  DEFAULT 0  comment '逻辑删除';
```



**application.properties**

```properties
# 数据库连接配置
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#mysql 8 需要增加时区的配置 serverTimezone=GMT%2B8

# 配置日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl

#逻辑删除的配置
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```

> maven

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.5.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.cqp</groupId>
    <artifactId>mybatis_plus</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>mybatis_plus</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
      
        <!--数据库驱动  -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <!--mybatis-plus-->
        <!--是自己开发的，并非官方的！-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.0.5</version>
        </dependency>

        <!-- 模板引擎 -->
        <dependency>
            <groupId>org.apache.velocity</groupId>
            <artifactId>velocity-engine-core</artifactId>
            <version>2.0</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/io.swagger/swagger-annotations -->
        <dependency>
            <groupId>io.swagger</groupId>
            <artifactId>swagger-annotations</artifactId>
            <version>1.5.22</version>
        </dependency>

    </dependencies>



    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```



**application.properties**

```properties
# 数据库连接配置
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis_plus?useSSL=false&useUnicode=true&characterEncoding=utf-8&serverTimezone=GMT%2B8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

#mysql 8 需要增加时区的配置 serverTimezone=GMT%2B8

# 配置日志
mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl

#逻辑删除的配置
mybatis-plus.global-config.db-config.logic-delete-value=1
mybatis-plus.global-config.db-config.logic-not-delete-value=0
```





**lab codeGnerator**

```java
package com.lab.lab;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.po.TableFill;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

import java.util.ArrayList;

public class codeGenerator {
    public static void main(String[] args) {
        AutoGenerator mpg = new AutoGenerator();
        GlobalConfig gc = new GlobalConfig();
        String projectPath = System.getProperty("user.dir");
        gc.setOutputDir(projectPath+"/src/main/java");
        gc.setAuthor("fxx");            //
        gc.setOpen(false);
        gc.setFileOverride(false);
        gc.setServiceName("%sService");
        gc.setIdType(IdType.ID_WORKER);
        gc.setDateType(DateType.ONLY_DATE);
        gc.setSwagger2(true);

        mpg.setGlobalConfig(gc);

        DataSourceConfig dsc = new DataSourceConfig();
        dsc.setUrl("jdbc:mysql://47.110.75.177:3306/ics2?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=GMT%2B8");
        dsc.setDriverName("com.mysql.cj.jdbc.Driver");
        dsc.setUsername("ics2");
        dsc.setPassword("DSNBM6Bx6ak5R86y");
        dsc.setDbType(DbType.MYSQL);

        mpg.setDataSource(dsc);


        PackageConfig pc = new PackageConfig();
        pc.setModuleName("ics2");
        pc.setParent("com.lab.lab");
        pc.setEntity("pojo");
        pc.setMapper("mapper");
        pc.setService("service");
        pc.setController("controller");

        mpg.setPackageInfo(pc);

        //4.策略配置
        StrategyConfig strategy = new StrategyConfig();
        strategy.setInclude("teacher"); //设置要映射的表名
        strategy.setNaming(NamingStrategy.underline_to_camel); // 下划线转驼峰命名
        strategy.setColumnNaming(NamingStrategy.underline_to_camel);
        strategy.setEntityLombokModel(true); // 自动lombok
        strategy.setLogicDeleteFieldName("is_delete");  //让数据库表中的deleted属性变成逻辑删除的属性
        // 自动填充配置
        TableFill createime = new TableFill("gmt_create", FieldFill.INSERT);
        TableFill updateTime = new TableFill("gmt_modified", FieldFill.INSERT_UPDATE);
        ArrayList<TableFill> tableFills = new ArrayList<>();
        tableFills.add(createime);
        tableFills.add(updateTime);
        strategy.setTableFillList(tableFills);
        mpg.setStrategy(strategy);
        mpg.execute();  // 执行
    }
}
```

