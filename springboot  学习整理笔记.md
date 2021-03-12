

## springboot  学习整理笔记

### 1.默认的主启动类 （自动配置初理解）

```java
//@SpringBootApplication 来标注一个主程序类//说明这是一个Spring Boot应用@SpringBootApplicationpublic class SpringbootApplication {
   public static void main(String[] args) {     //以为是启动了一个方法，没想到启动了一个服务      SpringApplication.run(SpringbootApplication.class, args);   }
}
```



#### 1.1注解解析

@SpringBootApplication

* 说明是springboot主配置类

* 整个应用的入口

  进入这个注解：可以看到上面还有很多其他注解！

​       ![image-20200914213203642](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200914213203642.png)

* @ComponentScan 

  这个注解在Spring中很重要 ,它对应XML配置中的元素。

  作用：自动扫描并加载符合条件的组件或者bean ， 将这个bean定义加载到IOC容器中

*  @SpringBootConfiguration

​       作用：SpringBoot的配置类 ，标注在某个类上 ， 表示这是一个SpringBoot的配置类；

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917142243272.png" alt="image-20200917142243272" style="zoom:50%;" /> 

说明这是一个配置类 ，配置类就是对应Spring的xml 配置文件；

Configuration再进去看

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917142446090.png" alt="image-20200917142446090" style="zoom:50%;" /> 

@Component 这就说明，启动类本身也是Spring中的一个组件而已，负责启动应用！







* @EnableAutoConfiguration:  开启自动配置功能

  以前我们需要自己配置的东西，而现在SpringBoot可以自动帮我们配置 ；@EnableAutoConfiguration告诉SpringBoot开启自动配置功能，这样自动配置才能生效；

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917142847146.png" alt="image-20200917142847146" style="zoom:50%;" />

**@AutoConfigurationPackage ：自动配置包**

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917142952026.png" alt="image-20200917142952026" style="zoom:50%;" />

**@import** ：Spring底层注解@import ， 给容器中导入一个组件

Registrar.class 作用：将主启动类的所在包及包下面所有子包里面的所有组件扫描到Spring容器 ；



返回看

**@Import({AutoConfigurationImportSelector.class}) ：给容器导入组件 ；**

AutoConfigurationImportSelector ：自动配置导入选择器，那么它会导入哪些组件的选择器呢？我们点击去这个类看源码：

1.这个类继承的父类  **AutoConfigurationImportSelector**   有这样一个方法

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917143908322.png" alt="image-20200917143908322" style="zoom:50%;" />

2.这个方法又调用了  SpringFactoriesLoader 类的静态方法！我们进入SpringFactoriesLoader类loadFactoryNames() 方法



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917144429628.png" alt="image-20200917144429628" style="zoom:50%;" />

```java
public static List<String> loadFactoryNames(Class<?> factoryClass, ClassLoader classLoader) {
        String factoryClassName = factoryClass.getName();

        try {
          
          //去获取一个资源 "META-INF/spring.factories"
          
            Enumeration<URL> urls = classLoader != null ? classLoader.getResources("META-INF/spring.factories") : ClassLoader.getSystemResources("META-INF/spring.factories");
            ArrayList result = new ArrayList();
            
          //将读取到的资源遍历，封装成为一个Properties

            while(urls.hasMoreElements()) {
                URL url = (URL)urls.nextElement();
                Properties properties = PropertiesLoaderUtils.loadProperties(new UrlResource(url));
                String propertyValue = properties.getProperty(factoryClassName);
                String[] var8 = StringUtils.commaDelimitedListToStringArray(propertyValue);
                int var9 = var8.length;

                for(int var10 = 0; var10 < var9; ++var10) {
                    String factoryName = var8[var10];
                    result.add(factoryName.trim());
                }
            }

            return result;
        } catch (IOException var12) {
            throw new IllegalArgumentException("Unable to load factories from location [META-INF/spring.factories]", var12);
        }
    }
```

3.发现一个多次出现的文件：spring.factories，全局搜索它

#### 1.2 spring.factories

![image-20200917144948797](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917144948797.png)

**WebMvcAutoConfiguration**

我们在上面的自动配置类随便找一个打开看看，比如 ：WebMvcAutoConfiguration

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917145052087.png" alt="image-20200917145052087" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917145200183.png" alt="image-20200917145200183" style="zoom:50%;" />

可以看到这些一个个的都是JavaConfig配置类，而且都注入了一些Bean.

所以，自动配置真正实现是从classpath中搜寻所有的META-INF/spring.factories配置文件 ，并将其中对应的 org.springframework.boot.autoconfigure. 包下的配置项，通过反射实例化为对应标注了 @Configuration的JavaConfig形式的IOC容器配置类 ， 然后将这些都汇总成为一个实例并加载到IOC容器中。

**结论：**

1. SpringBoot在启动的时候从类路径下的META-INF/spring.factories中获取EnableAutoConfiguration指定的值
2. 将这些值作为自动配置类导入容器 ， 自动配置类就生效 ， 帮我们进行自动配置工作；
3. 整个J2EE的整体解决方案和自动配置都在springboot-autoconfigure的jar包中；
4. 它会给容器中导入非常多的自动配置类 （xxxAutoConfiguration）, 就是给容器中导入这个场景需要的所有组件 ， 并配置好这些组件 ；
5. 有了自动配置类 ， 免去了我们手动编写配置注入功能组件等的工作；





### 2.自动配置再理解

![image-20200917153016251](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917153016251.png)

分析一波

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917153119225.png" alt="image-20200917153119225" style="zoom:50%;" />

自动配置属性

```java
@EnableConfigurationProperties(HttpEncodingProperties.class)
```

点进去看这个properties文件

![image-20200917153508474](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917153508474.png)

一一对应的。   固有规律 xxxProperties    xxxAutoConfiguration

回去看下面的三个注解

spring底层注解 根据不同的条件判断当前配置或者类是否生效

```java
@ConditionalOnWebApplication
@ConditionalOnClass(CharacterEncodingFilter.class)
@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917153844816.png" alt="image-20200917153844816" style="zoom:50%;" />



自动装配原理

1.spring boot启动会加载大量自动配置类

2.给容器中自动配置类添加组件时，会从properties类中获取某些属性，我们只需要在配置文件中指定这些属性的值即可；





### 3.springboot web开发

jar：webapp

自动装配

* 导入静态资源  WebMvcAutoApplication下的方法

```java
    @Override
   public void addResourceHandlers(ResourceHandlerRegistry registry) {
      if (!this.resourceProperties.isAddMappings()) {
         logger.debug("Default resource handling disabled");
         return;
      }
      Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
      CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
      if (!registry.hasMappingForPattern("/webjars/**")) {
         customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
               .addResourceLocations("classpath:/META-INF/resources/webjars/")
               .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
      }
      String staticPathPattern = this.mvcProperties.getStaticPathPattern();
      if (!registry.hasMappingForPattern(staticPathPattern)) {
         customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
               .addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
               .setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
      }
   }
```



什么是webjars？

可以以jar包的形式引入前端框架

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917165932178.png" alt="image-20200917165932178" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917170046056.png" alt="image-20200917170046056" style="zoom:50%;" />

可以按照这个路径访问到下面的文件

![image-20200917170116571](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200917170116571.png)



下面的代码表示

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200918182023958.png" alt="image-20200918182023958" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200918182222954.png" alt="image-20200918182222954" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200918182244483.png" alt="image-20200918182244483" style="zoom:50%;" />



* 首页

​       1.前面的public resource static 中如果有index.html会将其解析为主页。

​       2.需要thymleaf模板引擎的支持

​       springboot-starter 的官网地址https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200918190141876.png" alt="image-20200918190141876" style="zoom:50%;" />

```xml
<!--thymeleaf-->
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring5</artifactId>
</dependency>
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-java8time</artifactId>
</dependency>
```



```java
//在templates下面的所有页面只能通过controller来跳转
//这个需要模版引擎的支持    thymeleaf
@Controller
public class IndexController {
    @RequestMapping("/test")
    public String test(){
        return "test";
    }
}
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200919190850568.png" alt="image-20200919190850568" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200919190916277.png" alt="image-20200919190916277" style="zoom:50%;" />



这样就可以访问到templates模板包下的页面了

结论：只要使用thymeleaf ，只需要导入对应的依赖就可以了 我们将html放在我们的templates目录下

​            

* jsp 模版引擎Thymeleaf

  

```java
//在templates下面的所有页面只能通过controller来跳转
//这个需要模版引擎的支持    thymeleaf
@Controller
public class IndexController {
    @RequestMapping("/test")
    public String test(Model model){
        model.addAttribute("msg","hello springboot");
        return "test";
    }
}
```



```html
<html xmlns:th="http://www.thymeleaf.org">

ps：别忘记加这个命名空间
```



```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
<!--所有的html元素迪欧可以被thymeleaf接管： th：元素名-->
<div th:text="${msg}"></div>

</body>
</html>
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200919192317531.png" alt="image-20200919192317531" style="zoom:50%;" />





* 装配扩展SpringMvc



<img src="/Users/chenqipeng/Library/Containers/com.tencent.qq/Data/Library/Caches/Images/A75DF9FBCBF9DF734C2B8102AB543223.png" alt="A75DF9FBCBF9DF734C2B8102AB543223" style="zoom:50%;" />

​    这个类必须是 WebMvcConfigurer类

扩展方法：

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200920125039816.png" alt="image-20200920125039816" style="zoom:50%;" />



例子：扩展视图跳转功能

```java
//如果要扩展springmvc 官方建议我们这样去做！
//全面接管 springmvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    //视图跳转
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/cqp").setViewName("test");
    }
}
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200920125131584.png" alt="image-20200920125131584" style="zoom:50%;" />

**@EnableWebMvc ** 这玩意就是导入了一个类 DelegationWebMvcConfiguration（作用：从容器中获取所有的webmvcconfig）这个类又继承了 WebMvcConfigurationSupport。

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200920125944128.png" alt="image-20200920125944128" style="zoom:50%;" />



总结：在springboot中，有非常多的xxxxx Configuration帮助我们进行扩展配置，只要看见了这个东西，我们就要注意了。



* 增删改查

  准备工作：模拟数据库中的数据

```java
//部门表
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Department {
        private Integer id;
        private String departmentName;
}
```



```java
//员工表
@Data
@NoArgsConstructor
public class Employee {
    private Integer id;
    private String lastName;
    private String email;
    private Integer gender;  //0:女  1:男
    private Department department;
    private Date birth;

    public Employee(Integer id, String lastName, String email, Integer gender, Department department) {
        this.id = id;
        this.lastName = lastName;
        this.email = email;
        this.gender = gender;
        this.department = department;
        //默认创建日期
        this.birth = new Date();
    }
}
```



```java
//部门dao
@Repository
public class DepartmentDao {
    //模拟数据库中的数据

    private static Map<Integer, Department> departments=null;

    static{
        departments=new HashMap<Integer, Department>(); //创建一个部门表
        departments.put(101,new Department(101,"教学部"));
        departments.put(102,new Department(102,"市场部"));
        departments.put(103,new Department(103,"教研部"));
        departments.put(104,new Department(104,"运营部"));
        departments.put(105,new Department(105,"后勤部"));

    }

    //获得所有部门信息
    public Collection<Department> getDepartments(){
        return departments.values();
    }
    //通过id得到部门
    public Department getDepartmentById(Integer id){
        return departments.get(id);
    }
}
```



```java
//员工Dao
@Repository
public class EmployeeDao {

    //模拟数据库中的数据
    private static Map<Integer, Employee> employees=null;
    //员工有所属的部门
    @Autowired
    private DepartmentDao departmentDao;
    static{
        employees=new HashMap<Integer, Employee>(); //创建一个员工表
        employees.put(1001,new Employee(1001,"AA","12341241@qq.com",1,new Department(101,"教学部")));
        employees.put(1002,new Employee(1002,"BB","1234RE41@qq.com",1,new Department(102,"市场部")));
        employees.put(1003,new Employee(1003,"CC","12343541@qq.com",0,new Department(103,"教研部")));
        employees.put(1004,new Employee(1004,"DD","123T3241@qq.com",0,new Department(104,"运营部")));
        employees.put(1005,new Employee(1005,"EE","1234RT41@qq.com",1,new Department(105,"后勤部")));
    }
    //主键自增
    private  static Integer initId=1006;
    //增加一个员工
    public  void save(Employee employee){
        if(employee.getId()==null){
            employee.setId(initId++);    }
        employee.setDepartment(departmentDao.getDepartmentById(employee.getDepartment().getId()));
        employees.put(employee.getId(),employee);
    }
    //查询全部员工信息
    public Collection<Employee> getAll(){
        return employees.values();
    }
    //通过id查询员工
    public  Employee getEmployeeById(Integer id){
        return employees.get(id);
    }
    //删除员工
    public  void  delete(Integer id){   employees.remove(id);
    }
}
```



首页实现

。。。。。。。。

登陆功能

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200920153028948.png" alt="image-20200920153028948" style="zoom:50%;" />

```java
@Controller
public class LoginController {
    @RequestMapping("/user/login")
    @ResponseBody
    public String login(){
        return "OK";
    }
}
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200920153105207.png" alt="image-20200920153105207" style="zoom:50%;" />

跑通之后再往下完善功能

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200920153322891.png" alt="image-20200920153322891" style="zoom:50%;" />



  #### 3.1 员工列表展示

​      1.提取公共页面



​        ![image-20200922133014510](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922133014510.png)



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922133121286.png" alt="image-20200922133121286" style="zoom:50%;" />

传递参数（active='xxx'）接收参数

```html
 <a th:class="${active=='main.html'?'nav-link active':'nav-link'}" th:href="@{/main.html}">
```



**展示数据**

```html
<table class="table table-striped table-sm">
							<thead>
								<tr>
									<th>id</th>
									<th>lastName</th>
									<th>email</th>
									<th>gender</th>
									<th>department</th>
									<th>birth</th>
									<th>操作</th>
								</tr>
							</thead>
							<tbody>

							<tr th:each="emp:${emps}">
							      <td th:text="${emp.getId()}"></td>
							      <td>[[${emp.getLastName()}]]</td>
							      <td th:text="${emp.getEmail()}"></td>
							      <td th:text="${emp.getGender()==0?'女':'男'}"></td>
							      <td th:text="${emp.department.getDepartmentName()}"></td>
								  <td th:text="${#dates.format(emp.getBirth(),'yyyy-MM-dd hh:mm:ss')}"></td>
								  <td>
									  <button class="btn btn-sm btn-primary">编辑</button>
									  <button class="btn btn-sm btn-danger">删除</button>
								  </td>
							</tr>
							</tbody>
						</table>
```

```java
@Controller
public class EmployeeController {
    @Autowired
    EmployeeDao employeeDao;
    @RequestMapping("/emps")
    public String list(Model model){
        Collection<Employee> employees= employeeDao.getAll();
        model.addAttribute("emps",employees);
        return "emp/list";
    }
}
```

结果

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922153811237.png" alt="image-20200922153811237" style="zoom:50%;" />

#### 3.2添加员工

1.按钮跳转

2.跳转到添加页面

3.添加员工成功

4.返回首页



跳转按钮。默认为get方法

```html
<h2><a class="btn btn-sm btn-success " th:href="@{/emp}">添加员工</a></h2>
```

add.html代码

```html
<main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
   <form th:action="@{/emp}" method="post">
      <div class="form-group">
         <label>LastName</label>
         <input type="text" name="lastName" class="form-control" placeholder="chen">
      </div>
      <div class="form-group">
         <label>Email</label>
         <input type="email" name="email" class="form-control" placeholder="2111053717@qq.com">
      </div>
      <div class="form-group">
         <label>Gender</label>
         <div class="form-check form-check-inline">
            <input class="form-check-input"  type="radio" name="gender" value="1">
            <label class="form-check-label">男</label>
         </div>
         <div class="form-check form-check-inline">
            <input class="form-check-input" type="radio" name="gender" value="0">
            <label class="form-check-label">女</label>
         </div>
      </div>
      <div class="form-group">
         <label>department</label>
         <select class="form-control" name="department.id">
            <option th:each="dept:${departments}" th:text="${dept.getDepartmentName()}"th:value="${dept.getId()}">
               </option>

         </select>
      </div>
      <div class="form-group">
         <label>Birth</label>
         <input type="text" name="birth" class="form-control" placeholder="8.23">
      </div>
      <button type="submit" class="btn btn-primary">添加</button>
   </form>
</main> 
```

**表单提交会根据controller里的参数类型进行自动对表单数据的封装。**

表单中的input name的属性值必须和controller中的参数类型的属性名一致。

一个form对应一个对象

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922213116360.png" alt="image-20200922213116360" style="zoom:50%;" />





EmployeeController代码。 

```java
@GetMapping("/emp")
public String toAddpage(Model model){
    //查出所有部门的信息
    Collection<Department> departments = departmentDao.getDepartments();
    model.addAttribute("departments",departments);
    return "emp/add";
}

@PostMapping("/emp")
public String addEmp(Employee employee){
    //添加的操作
    System.out.println("save>>"+employee);
    employeeDao.save(employee);//调用底层业务方法保存员工信息
    return "redirect:/emps";
}
```





<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922212351724.png" alt="image-20200922212351724" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922212415624.png" alt="image-20200922212415624" style="zoom:50%;" />

问题：如果日期不是  2019/1/1的格式会报错？

如何解决：

这个和springmvc自带的格式转换器有关。

对时间日期格式化

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922213819949.png" alt="image-20200922213819949" style="zoom:50%;" />

跟随这个format‘源码找到它的默认格式是 dd/MM/yyyy

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200922214026676.png" alt="image-20200922214026676" style="zoom:50%;" />



#### 3.3修改员工

​    传递参数

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200923105200480.png" alt="image-20200923105200480" style="zoom:50%;" />

${}取变量

/()传递参数

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200923111831469.png" alt="image-20200923111831469" style="zoom:50%;" />



第一种括号传参的话参数可以传递但是不支持restFul风格

第二种写法：





<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200923112054792.png" alt="image-20200923112054792" style="zoom:50%;" />

```html
<!--传递参数${emp.id}-->
<a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.getId()}" >编辑</a>
```



update.html

```html
<main role="main" class="col-md-9 ml-sm-auto col-lg-10 pt-3 px-4">
   <form th:action="@{/updateEmp}" method="post">
      <input type="hidden" name="id" th:value="${emp.getId()}">
      <div class="form-group">
         <label>LastName</label>
         <input type="text" th:value="${emp.getLastName()}" name="lastName" class="form-control" placeholder="chen">
      </div>
      <div class="form-group">
         <label>Email</label>
         <input type="email" th:value="${emp.getEmail()}" name="email" class="form-control" placeholder="2111053717@qq.com">
      </div>
      <div class="form-group">
         <label>Gender</label>
         <div class="form-check form-check-inline">
            <input th:checked="${emp.getGender()==1}"  class="form-check-input"  type="radio" name="gender" value="1">
            <label class="form-check-label">男</label>
         </div>
         <div class="form-check form-check-inline">
            <input th:checked="${emp.getGender()==0}" class="form-check-input" type="radio" name="gender" value="0">
            <label class="form-check-label">女</label>
         </div>
      </div>
      <div class="form-group">
         <label>department</label>
         <select class="form-control" name="department.id">
            <option th:selected="${dept.getId()==emp.getDepartment().getId()}"    th:each="dept:${departments}" th:text="${dept.getDepartmentName()}"th:value="${dept.getId()}">
            </option>

         </select>
      </div>
      <div class="form-group">
         <label>Birth</label>
         <input th:value="${#dates.format(emp.getBirth(),'yyyy-MM-dd HH:mm')  }" type="text" name="birth" class="form-control" placeholder="8.23">
      </div>
      <button type="submit" class="btn btn-primary">添加</button>
   </form>
</main>
```



```java
    //去员工的修改页面
@GetMapping("/emp/{id}")
    public String toUpdateEmp(@PathVariable("id")Integer id,Model model ){
        //查出原来的数据
    Employee employee= employeeDao.getEmployeeById(id);
    model.addAttribute("emp",employee);
    Collection<Department> departments = departmentDao.getDepartments();
    model.addAttribute("departments",departments);

    return "emp/update";
    }

    //修改员工信息
    @PostMapping("/updateEmp")
    public String updateEmp(Employee employee){
        employeeDao.save(employee);
        return "redirect:/emps";
    }
```



注销

```html
<a class="nav-link" th:href="@{/user/logout}">注销</a>
```

```java
//注销
@RequestMapping("/user/logout")
public String logout(HttpSession session){
    session.invalidate();
    return "redirect:/index.html";
}
```

* 404

​     在templates包下建一个error包 放入404.html  500.html即可

* 拦截器

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200924121703295.png" alt="image-20200924121703295" style="zoom:50%;" />







```java
public class LoginHandleInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //登陆成功之后 应该有用户的session
       Object loginuser=request.getSession().getAttribute("loginUser");
       if(loginuser==null){//没有登陆
           request.setAttribute("msg","没有权限，请先登陆");
           request.getRequestDispatcher("/index.html").forward(request,response);
           return false;
       }else{
           return true;
       }

    }


}
```



```java
//如果要扩展springmvc 官方建议我们这样去做！
//全面接管 springmvc
@Configuration
public class MyMvcConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("index");
        registry.addViewController("/index.html").setViewName("index");
        registry.addViewController("/main.html").setViewName("dashboard");
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new LoginHandleInterceptor())
                .addPathPatterns("/**").excludePathPatterns("/index.html","/","/user/login","/asserts/**");
    }
}
```





* 国际化



### 4.Data

对于数据访问层，无论是sql（关系型数据库）还是nosql（非关系型数据库）springBoot底层都是采用Spring Data的方式统一处理。

Spring Date也是Spring中与 Spring Cloud Spring Boot等齐名的知名项目。

https://spring.io/projects/spring-data



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200924122813267.png" alt="image-20200924122813267" style="zoom:50%;" />



测试数据源

```java
@Test
void contextLoads() throws SQLException {
    //查看默认数据源  class com.zaxxer.hikari.HikariDataSource
    System.out.println(dataSource.getClass());
    //获得数据库连接
    Connection connection = dataSource.getConnection();
    System.out.println(connection);

    // xxxxTemplate  :springboot已经配置好的模板，拿来即用
    // CRUD增删改查的方法在这个template下封装好了
    // jdbc
    // redis

    //关闭
    connection.close();
```



查询数据库用户表

```java
@RestController
public class JDBCController {
    @Autowired
    JdbcTemplate jdbcTemplate;

    //查询数据库的所有信息
    //没有实体类 数据库中的东西怎么获取？ 存到map里
    @GetMapping("/userList")
    public List<Map<String,Object>> userList(){
        String sql="select * from tb_user_info";
        List<Map<String, Object>> list_maps = jdbcTemplate.queryForList(sql);
        return list_maps;
    }

}
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200925210356455.png" alt="image-20200925210356455" style="zoom:50%;" />

```java
@GetMapping("/addUser")
public String addUser(){
   String sql="insert into tb_user_info(id,user_name,password,create_user) values(99,'小值','123456','admin')";
   jdbcTemplate.update(sql);
   return "add-ok";
}

@GetMapping("/updateUser/{id}")
public String updateUser(@PathVariable("id") int id){
    String sql="update tb_user_info set user_name=?,password=? where id="+id;
    //封装
    Object[] objects=new Object[2];
    objects[0]="小明";
    objects[1]="zzzzzz";
    jdbcTemplate.update(sql,objects);
    return "update-ok";
}

@GetMapping("/deleteUser/{id}")
public String deleteUser(@PathVariable("id") int id){
    String sql="delete from tb_user_info where id=?";
    jdbcTemplate.update(sql,id);
    return "delete-ok";
}
```

#### 4.1 druid



```xml
<!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.24</version>
</dependency>

```



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200925233746033.png" alt="image-20200925233746033" style="zoom:50%;" />

引入jar包之后    type属性指定就ok了

```yaml
spring:
  datasource:
    username: root
    password: 123456
    url: jdbc:mysql://localhost:3306/iiot?useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    #Spring Boot默认是不注入这些属性值的，需要自己绑定
    #druid数据源专有配置
    initialsize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 600e0
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 3e0e0e
    validationQuery: SELECT 1 FROM DUAL
    testwhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true
    #配置监控统计拦截的filters，stat:监控统计、Log4j:日志记录、walL:防御sqL注入
    #如果允许时报错java.Lang.CLassNotFoundException: org.apache.Log4j.Priority
    #则导入log4j依赖即可，Maven 地址: https: //mvnrepository.com/artifact/Log4j/Log4j
    filters: stat,wall,log4j
    maxPoolpreparedstatementPerConnectionsize: 20
    useGlobalDataSourcestat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```



druid可以自定义配置

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200925235239099.png" alt="image-20200925235239099" style="zoom:50%;" />



application.yml文件要与config类绑定起来

不配置的话yml中的私有化属性不会生效

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200925235923035.png" alt="image-20200925235923035" style="zoom:50%;" />

将自定义的容器druidDataSource加入到spring容器中，就不需要springboot自己创建了。现在就可以配置它了。可以实现一些高级功能，比如说监控

```java
@Configuration
public class DruidConfig {
    @ConfigurationProperties(prefix = "spring.datasource")
    @Bean
    public DataSource druidDataSource(){
        return new DruidDataSource();
    }

    //后台监控  :这个类写出来相当于 web.xml  因为springboot 内置了servlet容器 所以没有web.xml
    //替代方法: ServletRegistrationBean 注册到bean容器中
    // servlet注册bean   需要放入druid的注册bean
    @Bean
    public ServletRegistrationBean StatViewServlet(){
        ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");

        //后台需要有人登陆，账号密码配置
        HashMap<String, String> initParameters = new HashMap<>();
        //增加配置
        initParameters.put("loginUsername","admin");  //登陆key是固定的  loginUsername  loginPassword
        initParameters.put("loginPassword","123456");
        //允许谁可以访问
        initParameters.put("allow","");
        //禁止谁能访问
       // initParameters.put("lll","192.168.11.123");

        bean.setInitParameters(initParameters); //设置初始化参数
        return bean;
    }
}
```



运行结果：

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926120738673.png" alt="image-20200926120738673" style="zoom:50%;" />

补充：filter

```java
//filter
@Bean
public FilterRegistrationBean webStartFilter(){
      FilterRegistrationBean bean=new FilterRegistrationBean();
      bean.setFilter(new WebStatFilter());
      //可以过滤哪些请求?
    HashMap<String, String> initParameters = new HashMap<>();
    //这些东西不进行统计
    initParameters.put("exclusions","*.js,*.css,/druid/*");
    return bean;
}
```



#### 4.2  mybatis

整合包

mybatis-spring-boot-starter       官方文档：http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>

```

新建项目。勾选下面三个依赖。

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926122728409.png" alt="image-20200926122728409" style="zoom:50%;" />

放入上面的maven依赖

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926123520321.png" alt="image-20200926123520321" style="zoom:50%;" />

写数据库配置

```properties
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.url=jdbc:mysql://localhost:3306/iiot?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926124429964.png" alt="image-20200926124429964" style="zoom:50%;" />



连接测试成功



现在开始使用mybatis

第一步：实体类

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928193641925.png" alt="image-20200928193641925" style="zoom:50%;" />



第二步 mapper接口

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928194944235.png" alt="image-20200928194944235" style="zoom:50%;" />





<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928195111918.png" alt="image-20200928195111918" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928195805674.png" alt="image-20200928195805674" style="zoom:50%;" />



第三步 sql语句

原来是要在mapper包下写mapper.xml文件

但是这里写在resource包下 方便管理。（但是这样就不能用class访问了 这会导致注解和resource下不能同时使用，只能全部用配置文件）

resource包下建mybatis包 mybatis包下建mapper包 再建立UserMapper.xml文件

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928201926287.png" alt="image-20200928201926287" style="zoom:50%;" />

第四步：全局配置mybatis属性

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928201150338.png" alt="image-20200928201150338" style="zoom:50%;" />



第五步：写个controller测试一下

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928202745571.png" alt="image-20200928202745571" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200928202816588.png" alt="image-20200928202816588" style="zoom:50%;" />





ok！















### 5. SpringSecurity(安全)



Spring Security is a powerful and highly customizable authentication and access-control framework.

Spring Security是一个功能强大且高度可定制的身份验证和访问控制框架。

https://docs.spring.io/spring-security/site/docs/5.4.0/reference/html5/#new

**简介**

SpringSecurity是针对spring项目的安全框架，也是springboot底层安全模块默认的技术选型，可以实现强大的web安全控制，对于安全控制，我们仅需要引入spring-boot-starter-security模块，进行少量的配置，即可实现强大的安全管理。

记住几个类：

* WebSecurityConfigurerAdapter   自定义Security策略

* AuthenticationManagerBuilder  自定义认证策略

* @EnableWebSecurity  开启WebSecurity模式  @Enablexxxx开启某个功能

  

核心是把权限赋给角色 而不是具体的用户

用户通过活得角色来获得相应的权限



shiro  和 spring security很像。除了类和名字不一样

认证 授权（vip1 vip2 vip3）

* 功能权限
* 访问权限
* 菜单权限
* 拦截器 过滤器



准备工作 搭建学习环境

导入静态页面

导入thymleaf web 依赖

编写路由控制

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200929135500041.png" alt="image-20200929135500041" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200929135541547.png" alt="image-20200929135541547" style="zoom:50%;" />

第一步导入 springsecurity依赖



```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930123020132.png" alt="image-20200930123020132" style="zoom:50%;" />



官网例子



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930124051782.png" alt="image-20200930124051782" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930124019864.png" alt="image-20200930124019864" style="zoom:50%;" />

```java
//AOP横切编程
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    //授权
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //首页所有人可以访问  功能页只有对应有权限的人才能访问
        http.authorizeRequests()
                .antMatchers("/").permitAll()
                .antMatchers("/level1/**").hasRole("vip1")
                .antMatchers("/level2/**").hasRole("vip2")
                .antMatchers("/level3/**").hasRole("vip3");

        //没有权限默认会到登陆页面
        http.formLogin();
      
        //注销 开启注销跳到首页
        http.logout().logoutSuccessUrl("/");
      
        //开启记住我功能  cookie
        http.rememberMe();
    }


    //认证   springboot 2.1.x 可以直接使用
    //报错 There is no PasswordEncoder mapped for the id "null"
    //在spring 5中新增了很多加密方式
    //真实场景数据应该从数据库中取出
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                .withUser("cqp").password(new BCryptPasswordEncoder().encode("123456") ).roles("vip2","vip3")
                .and()
                .withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1","vip2","vip3")
                .and()
                .withUser("guest").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1");

    }
}
```



不同的角色能够进入不同的页面 没有权限会被拒绝



现在实现的话所有用户登陆后会看到所有的信息

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930190345481.png" alt="image-20200930190345481" style="zoom:50%;" />



但是我们想实现如果cqp登陆的话 只能看到level2和level3被授予权限的内容。



先导整合包

```xml
<!--整合包-->
 <!-- https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity4 -->
 <dependency>
     <groupId>org.thymeleaf.extras</groupId>
     <artifactId>thymeleaf-extras-springsecurity5</artifactId>
     <version>3.0.4.RELEASE</version>
 </dependency>
```



有了这个整合包可以在thymleaf里写 spring security的操作

先解决如果登陆显示logout和登录名 如果没登陆显示登陆按钮

注意命名空间  xmlns:sec="http://www.thymeleaf.org/extras/spring-security"

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930194804563.png" alt="image-20200930194804563" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930194827219.png" alt="image-20200930194827219" style="zoom:50%;" />

成功！



现在实现只显示有权限的内容

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930200501827.png" alt="image-20200930200501827" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200930200435985.png" alt="image-20200930200435985" style="zoom:50%;" />

成功！！！





踩坑：

spring security和thymleaf整合包最新版本thymeleaf-extras-springsecurity4不支持springboot的高版本。

导入*DefaultWebSecurityManager*时很容易误导入*import org.apache.shiro.mgt.DefaultSecurityManager;*

包导致项目报错。

应导为import org.apache.shiro.web.mgt.DefaultWebSecurityManager; 包

### 6.shiro

shiro笔记

![image-20201208142447928](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201208142447928.png)







![image-20201208142541901](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201208142541901.png)







### 7.分布式Dubbo+Zookeeper + SpringBoot

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926135750430.png" alt="image-20200926135750430" style="zoom:50%;" />



#### 7.1 dubbo

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926140351324.png" alt="image-20200926140351324" style="zoom:50%;" />

官网地址：http://dubbo.apache.org/zh-cn/docs/user/quick-start.html

#### 7.2 RPC（Remote Procedure Call）远程过程调用

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926141226090.png" alt="image-20200926141226090" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926141513447.png" alt="image-20200926141513447" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926142638163.png" alt="image-20200926142638163" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926142731350.png" alt="image-20200926142731350" style="zoom:50%;" />



Http Springcloud（生态）

netty 

RPC两个核心模块：通信 序列化（方便数据传输，数据传输需要转换）



dubbo 18年重启项目 Dubbo 3.x   （rpc框架）



#### 7.3 dubbo

什么是dubbo？

dubbo是一款高性能,轻量级的开源java rpc 框架，提供了三大核心能力：**面向接口的远程方法调用**，**智能容错和负载均衡**，**以及服务自动注册和发现**。







![image-20200926144301096](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926144301096.png)





服务提供者(Provider) :暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务。
服务消费者(Consumer) : 调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
注册中心(Registry) :注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者
监控中心(Monitor) :服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心



##### 调用关系说明

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。



zookeeper：hadoop hive



配置安装zookeeper    （mac版本）

1.下载zookeeper，[下载地址](https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/)
2.解压在需要安装的目录，随意就行，然后打开在zookeeper的conf目录下找到zoo_sample.cfg文件，复制一份，并重命名为zoo.cfg，就放在同目录下就行。

然后在zookeeper文件夹下新建 data 和logs文件夹

```xml
tickTime=2000 
dataDir= /Users/chenqipeng/programming/environments/zookeeper/zookeeper-3.4.14/data
dataLogDir=/Users/chenqipeng/programming/environments/zookeeper/zookeeper-3.4.14/logs  
clientPort=2181
```



启动zookeeper，进入到zookeeper的bin目录下，执行zkServer.sh start或者（zkServer.sh start-foreground），运行zkCli.sh查看是否能连接上本机的2181端口

```
Connecting to localhost:21811
```

或者输入jps

```
1157 QuorumPeerMain1
```

有QuorumPeerMain就说明启动成功。（后续的步骤中都不要关闭它，保持运行状态）

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926153712158.png" alt="image-20200926153712158" style="zoom:50%;" />

参考：https://blog.csdn.net/d007letian/article/details/78018176

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926160237738.png" alt="image-20200926160237738" style="zoom:50%;" />



打包这个目录

```xml
mvn package -Dmaven.test.skip=true
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926160444203.png" alt="image-20200926160444203" style="zoom:50%;" />

   

打包成功

![image-20200926160726369](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926160726369.png)



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926161021169.png" alt="image-20200926161021169" style="zoom:50%;" />





执行jar包。 

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926161604516.png" alt="image-20200926161604516" style="zoom:50%;" />



 ps:zookeeper开启才会成功访问 

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926161655386.png" alt="image-20200926161655386" style="zoom:50%;" />

默认用户名是root

默认密码是root

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926161806973.png" alt="image-20200926161806973" style="zoom:50%;" />

上述方式也可以通过idea导入maven项目再在idea里打包。

dubbo-admin：是一个监管后台 可以查看我们注册了哪些服务，哪些服务被消费了。



#### 7.4 demo 实战

1.新创建空项目

2.添加module 勾选一个web模块

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926162911735.png" alt="image-20200926162911735" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926163530769.png" alt="image-20200926163530769" style="zoom:50%;" />

配置

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926214215490.png" alt="image-20200926214215490" style="zoom:50%;" />

添加注解

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926214502979.png" alt="image-20200926214502979" style="zoom:50%;" />









3.maven jar包导入。版本错误会启动失败. springboot 版本 2.2.1

```xml

		<!--导入dubbo和zookeeper依赖-->
		<!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
		<dependency>
			<groupId>org.apache.dubbo</groupId>
			<artifactId>dubbo-spring-boot-starter</artifactId>
			<version>2.7.8</version>
		</dependency>
		<!--zkclient-->
		<!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
		<dependency>
			<groupId>com.github.sgroschupf</groupId>
			<artifactId>zkclient</artifactId>
			<version>0.1</version>
		</dependency>
        <!--引入zookeeper日志会冲突  引入下面的依赖可以解决-->
		<!-- https://mvnrepository.com/artifact/org.apache.curator/curator-framework -->
		<dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-framework</artifactId>
			<version>2.12.0</version>
		</dependency>
		<dependency>
			<groupId>org.apache.curator</groupId>
			<artifactId>curator-recipes</artifactId>
			<version>2.8.0</version>
		</dependency>
		<dependency>
			<!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
				<groupId>org.apache.zookeeper</groupId>
				<artifactId>zookeeper</artifactId>
				<version>3.6.2</version>
			<!--排除这个slf4j-log412-->
			<exclusions>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>slf4j-log4j12</artifactId>
				</exclusion>
			</exclusions>
		</dependency>

```



启动ProviderServerApplication后访问dubbo-admin

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926213512775.png" alt="image-20200926213512775" style="zoom:50%;" />



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926213701220.png" alt="image-20200926213701220" style="zoom:50%;" />



成功监听到生产者

4.新建模块 consumer-server

引入jar包  编写配置文件 配置注册中心

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926223603230.png" alt="image-20200926223603230" style="zoom:50%;" />

消费的业务代码

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926223753512.png" alt="image-20200926223753512" style="zoom:50%;" />

实现两个模块调用

编写测试。并运行

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20200926223857605.png" alt="image-20200926223857605" style="zoom:50%;" />



成功。



总结：

步骤：(前提：zookeeper服务开启)

1.提供者提供服务

​     1.导入依赖

​     2.配置注册中心的地址，以及服务发现名 和要扫描的包

​     3.在想要被注册的服务上面 增加注解@Service （dubbo包下的）

2.消费者如何消费

   1.导入依赖

   2.配置注册中心地址 配置自己的服务名

   3.从远程注入服务。@Reference





ps: maven环境配置

1.先在命令行下敲：vi  .profile  (也可以是  vi ~/.profile  这个是配置你自己当前用户的环境变量，别的用户用不了)

2.进入文件后，敲  i  ,进入编辑模式

然后就写下下面这几行就好

MAVEN_HOME=/Users/user/workspace/software/apache-maven-3.5.3 （找到自己下载后且解压的maven文件路径）

export MAVEN_HOME

export PATH=$MAVEN_HOME/bin:$PATH

3.上面几行敲完后就要保存文件了。

3.1 按下  esc 键退出编辑模式。

3.2 敲下命令   :wq!  这四个字符，注意是四个字符！！！

4.保存完文件就是让这个环境变量可以马上好使啊。

敲下命令行  source .profile  (当前用户的话是  source  ~/.profile)

5.验证maven环境变量是否好使，

敲下命令  mvn  -v

敲下命令后出现你maven的版本号后，那就恭喜你成功了。





springboot测试类报错的话，请先导入        

```xml
<dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <scope>test</scope>
</dependency>
```







### 8.swagger简介

官网：https://swagger.io/



**springboot集成swagger项目**



1.新建一个springboot web项目

2.导入相关依赖

```xml
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

```

3.编写hello过程

4.配置swagger。  config

```java

@Configuration
@EnableSwagger2   //开启swagger
public class SwaggerConfig {
}
```

这样swagger才能运行 有默认配置

运行测试 

访问http://localhost:8080/swagger-ui.html

![image-20201015201319878](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201015201319878.png)



**配置swagger**

swagger的bean实例 Docket

```java
@Configuration
@EnableSwagger2   //开启swagger
public class SwaggerConfig {

    //配置类sawgger的docket bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                ;
    }
    //配置swagger信息=apiInfo
    private ApiInfo apiInfo(){
        //作者信息
        Contact contact=new Contact("cqp","asdasd.com","2111053717@qq.com");
        return new ApiInfo("cqp的swagger API文档",
                "三天皆勤勉，昨天今天明天！",
                "v1.0",
                "urn:tos",
                contact,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());

    }

}
```

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201015204710283.png" alt="image-20201015204710283" style="zoom:50%;" />



**Swagger配置扫描接口**

Docket.select

```java
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .select()
            //RequestHandlerSelectors : 配置扫描接口的方式
            //basePackage : 指定要扫描的包
            //any（）: 扫描全部
            // none（）：都不扫描
            // withClassAnnotation 扫描类上的注解 参数是一个注解的反射对象
            // withMethodAnnotation 扫描方法上的注解
            .apis(RequestHandlerSelectors.basePackage("com.cqp.swagger.controller"))
            //过滤 什么路径 只扫描/cqp/** 下的东西
            .paths(PathSelectors.ant("/cqp/**"))
            .build()
            ;
}
```

配置是否启动swagger



如何做到swagger在生产环境中使用，在发布的时候不使用？

* 判断是不是生产环境  
* 注入enable（）



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016112817010.png" alt="image-20201016112817010" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016112939153.png" alt="image-20201016112939153" style="zoom:50%;" />

![image-20201016113009508](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016113009508.png)<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016113010519.png" alt="image-20201016113010519" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016113042647.png" alt="image-20201016113042647" style="zoom:50%;" />



**配置api文档的分组**

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016113447556.png" alt="image-20201016113447556" style="zoom:50%;" />

​	



如何配置多个分组？

多个docket实例即可



<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016113857518.png" alt="image-20201016113857518" style="zoom:50%;" />





<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016113834758.png" alt="image-20201016113834758" style="zoom:50%;" />





**实体类扫描**

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016114520564.png" alt="image-20201016114520564" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016114539925.png" alt="image-20201016114539925" style="zoom:50%;" />

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016114645518.png" alt="image-20201016114645518" style="zoom:50%;" />







测试接口

<img src="/Users/chenqipeng/Library/Application Support/typora-user-images/image-20201016122057505.png" alt="image-20201016122057505" style="zoom:50%;" />



总结：

* 可以通过swagger给难理解的属性或者接口增加注释信息
* 接口文档实时更新
* 可以在线测试



【注意点】 正式发布时关闭swagger





### 9.任务

异步任务～

定时任务～

邮件发送～

### 10.注意事项

**@RequestBody只支持POST请求，GET请求不能使用@RequestBody，修改GET请求为POST即可，如果需要使用GET请求，可以使用@RequestParam和@PathVariable**

