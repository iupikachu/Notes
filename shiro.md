## shiro 





### 前言

![image-20210106202205816](shiro.assets/image-20210106202205816.png)

> ### cookies

存储在客户端(浏览器)

由于HTTP是一种无状态的协议，服务器单从网络连接上无从知道客户身份。怎么办呢？就**给客户端们颁发一个通行证吧，每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了。这就是Cookie的工作原理**。

　　Cookie实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。

``` java

    /*
    测试 cookie
     */
    @RequestMapping(value = "/login2",produces="application/json;charset=UTF-8")
    public void Test(HttpServletRequest request, HttpServletResponse response) throws IOException {
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");
        response.setContentType("text/html; charset=utf-8");   // 有效使 PrintWriter 不乱码
        PrintWriter out = response.getWriter();

        Cookie[] cookies = request.getCookies();

        if(cookies != null){
            out.write("你上一次访问的时间是:");
            for (Cookie cookie : cookies) {
                if(cookie.getName().equals("lastLoginTime")){

                    long lastLoginTime = Long.parseLong(cookie.getValue());
                    Date date = new Date(lastLoginTime);
                    out.write(date.toLocaleString());
                }
            }
        }else{
            out.write("这是您第一次访问本站");
        }

        Cookie cookie = new Cookie("lastLoginTime",System.currentTimeMillis()+"");
//        Cookie cookie1 = new Cookie("name", URLEncoder.encode("张三","utf-8"));   存中文
        response.addCookie(cookie);
    }
```



> ### session

* 服务器会给每个用户（浏览器）创建一个session
* 一个session 独占一个浏览器，只要浏览器没有关闭，这个session就存在
* 用户登陆之后，整个网站都可以访问。 保存了用户的信息
* Session 注销后会产生一个新的 SessionID 意味着一次新的会话 里面存的数据都没有了



不同的浏览器得到不一样的Sessionid

​	session创建成功,ID：88D5C14F54C34E198E81AB54E2D2EC21

​	session创建成功,ID：680909E914D45D3F0AB855BE3A355846



使用 Session的场景：

* 保存一个登陆用户的信息
* 购物车信息
* 整个网站中经常使用的数据



Session 和 Cookie 的区别:

* Cookie 是把用户的数据写给用户的浏览器，浏览器保存 （可以保存多个）
* Session 把用户的数据写给用户独占的 Session 中，服务器端保存 （保存重要的信息，减少服务器资源的浪费）
* Session 对象由服务端创建 



> 有了Session 和 Cookie 就能区分出客户端了，但是服务器需要保存所有人的Sessionid，这对服务器来说是一个巨大的开销。
>
> 而且用户认证之后，下一次用户的请求还必须在这台服务器上，这样才能拿到授权的资源，这样在分布式的应用上，限制了负载均衡的能力，限制了应用扩展的能力。
>
> **如果只让客户端去保存就好了？**
>
> 用户登录时，我把他的用户id，进行加密，生成一个签名（token），返回给用户，他保存这个token。我不保存这个token.
>
> 他下一次要访问时，带上这个token，我拿到token和他的用户id，我用同样的算法和秘钥签名，看这两个token是否一致。一致就认为是本人。



解除了session id这个负担， 可以说是无事一身轻， 机器集群现在可以轻松地做水平扩展， 用户访问量增大， 直接加机器就行。 这种无状态的感觉实在是太好了！



### 1.1 JWT（json web token）

 

json web token 是一个开放标准，用于在各方之间以json对象安全地传输信息。此信息可以被验证和信任，因为它是数字签名的。jwt可以使用HMAC算法、RSA、或ECDSA的公钥/私钥对进行签名。



通过 json 的形式作为 web 应用中的令牌，用于在各方之间安全地将信息作为 json 对象传输。



### 1.2 JWT 能做什么？



1. 授权   

​       一旦用户登录，每个后续请求将包括JWT,从而允许该用户访问该令牌允许的路由，服务和资源。 **单点登录**是当今广泛使用JWT的一项功能。

2. 信息交换

   jwt 可以很好的在各方之间安全的传输信息。因为可以对 jwt进行签名，所以可以确保发件人的身份。签名是用标头和有效负载计算的，还可以验证内容是否被篡改。



### 



![image-20210106210925894](shiro.assets/image-20210106210925894.png)

![image-20210106211532187](shiro.assets/image-20210106211532187.png)





### 1.3 Token的优点

1. 无状态

   token存储在客户端，完全无状态。

2. 可扩展

   负载均衡器可以将用户传递到任意服务器，因为在任意地方都没有状态或会话信息。

3. 安全

   Token不是cookie，有助于防止CSRF (跨站请求伪造) 攻击



JWT 与 Oauth2的区别

* OAuth2是一种授权框架
* JWT是一种认证协议

OAuth2用在使用第三方账号登录的情况(比如使用weibo, qq, github登录某个app)，而JWT是用在前后端分离, 需要简单的对后台API进行保护时使用。



### 1.4 JWT 的组成



![image-20210107093055896](shiro.assets/image-20210107093055896.png)



前端拿到之后，每次访问都带上这个token，后端根据Header和Payload 重新计算 Signature进行比对。并且能够拿到 Payload

里面的数据而不用频繁查询数据库。 在jwt中不应该加入敏感信息。

![image-20210107093626781](shiro.assets/image-20210107093626781.png)

 

### 1.5 使用JWT



**引入依赖**

```xml
<!-- 引入jwt-->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.11.0</version>
</dependency>
```



```java
/*
测试 jwt
 */
@Test
void createToken() {
    Calendar instance = Calendar.getInstance();
    instance.add(Calendar.SECOND,100);
     String token = JWT.create()
                 .withClaim("userid",21)
                 .withClaim("username","cqp") // payload
                 .withExpiresAt(instance.getTime())      // 指定令牌过期时间
                 .sign(Algorithm.HMAC256("21312wdsdf")); // 签名
    System.out.println(token);

}

@Test
void testToken(){
    JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("21312wdsdf")).build();

    DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MDk5OTQ0OTgsInVzZXJpZCI6MjEsInVzZXJuYW1lIjoiY3FwIn0.KVaSwV9S8SSwiWfhu9CMwCqanPrbz8jnt_6EULgbftI");
    System.out.println(verify.getClaims().get("userid").asInt());
    System.out.println(verify.getClaims().get("username").asString());
}
```



JWTUtils 工具类

```java
package com.cqp.swagger.utils;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTCreator;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.interfaces.DecodedJWT;
import org.springframework.stereotype.Component;

import java.util.Calendar;
import java.util.HashMap;
import java.util.Map;

@Component
public class JWTUtils {


    // 秘钥
    private static final String SECRET_KEY = "!@#$%^&*BJDFTY!@#^!&^#";

    /*
    生成 token header.payload.signature
     */
     public static String getToken(Map<String,String> map ){

         Calendar instance = Calendar.getInstance();
         instance.add(Calendar.DATE,7); // 默认七天过期

         // 创建 jwt builder
         JWTCreator.Builder builder = JWT.create();

         // PayLoad
         map.forEach((key,value) ->{
             builder.withClaim(key,value);
         });

         // 指定令牌过期时间
         String token = builder.withExpiresAt(instance.getTime())
                 .sign(Algorithm.HMAC256(SECRET_KEY));// sign 签名

         return token;
     }
    /**
     * 验证 token 合法性
     * 验签过程中有错误的话会抛出异常
     */

    public static void verify(String token){
        JWT.require(Algorithm.HMAC256(SECRET_KEY)).build().verify(token);
    }

    /**
     * 获取token信息
     *  decodeJWT.getClaims().get("userid").asInt()
     *  decodeJWT.getClaims().get("username").asString() 去拿用户信息
     */
public static DecodedJWT getTokenInfo(String token){
    DecodedJWT decodedJWT = JWT.require(Algorithm.HMAC256(SECRET_KEY)).build().verify(token);
    return decodedJWT;
}
}
```



### 1.6 springboot 整合 JWT

![image-20210107163249438](shiro.assets/image-20210107163249438.png)

#### 1.6.1 测试

```xml
<!-- 引入jwt-->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.4.0</version>
</dependency>
<!--引入mybatis -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
<!--引入lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<!--引入druid-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.24</version>
</dependency>
<!--引入mysql-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```



```java
@RestController
@Slf4j
public class UserController {
    @Autowired
    private UserService userService;

    @GetMapping("/user/login")
    public Map<String,Object> login(User user){

    log.info("用户名:[{}]",user.getName());
    log.info("密码:[{}]",user.getPassword());
    Map<String,Object> map = new HashMap<>();

    try{
        User userDB = userService.login(user);
        // 查到用户了 生成 jwt令牌
        HashMap<String, String> payLoad = new HashMap<>();
        payLoad.put("id",userDB.getId());
        payLoad.put("name",userDB.getName());

        String token = JWTUtils.getToken(payLoad);

        map.put("state",true);
        map.put("msg","登录成功");
        map.put("token",token);
    }catch (Exception e){
        map.put("state",false);
        map.put("msg",e.getMessage());
    }
     return  map;
    }
    /*
    验证令牌
     */
    @PostMapping("/user/test")
    public Map<String,Object> test (String token){
        HashMap<String, Object> map = new HashMap<>();
        log.info("当前token:[{}]",token);
        try{
            DecodedJWT verify = JWTUtils.verify(token);   // 验证令牌

            map.put("state",true);
            map.put("msg","请求成功");
            return map;
        }catch (SignatureVerificationException e){
            e.printStackTrace();
            map.put("msg","无效签名");
        }catch (TokenExpiredException e){
            e.printStackTrace();
            map.put("msg","token过期");
        }catch (AlgorithmMismatchException e){
            e.printStackTrace();
            map.put("msg","token算法不匹配");
        }catch(Exception e){
            e.printStackTrace();
            map.put("msg","验证失败");
        }
        map.put("state",false);

        return map;
    }
```



![image-20210107192611900](shiro.assets/image-20210107192611900.png)

![image-20210107192627920](shiro.assets/image-20210107192627920.png)



问题: 使用上述方式每次都要传递token数据，每个方法都要验证 token 代码冗余，不够灵活，如何优化?

- 拦截器  （单体应用）
- springcloud 分布式可以放到网关中去做





下面就试试拦截器

把 jwt 放在请求头中

#### 1.6.2 拦截器

```java
package com.cqp.swagger.interceptors;

import com.auth0.jwt.exceptions.AlgorithmMismatchException;
import com.auth0.jwt.exceptions.SignatureVerificationException;
import com.auth0.jwt.exceptions.TokenExpiredException;
import com.auth0.jwt.interfaces.DecodedJWT;
import com.cqp.swagger.utils.JWTUtils;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.HashMap;

public class JWTInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HashMap<String, Object> map = new HashMap<>();
        // 获取请求头中的令牌
        String token = request.getHeader("token");

        try{
            JWTUtils.verify(token);   // 验证令牌
            // 无异常 放行请求
            return true;
        }catch (SignatureVerificationException e){
            e.printStackTrace();
            map.put("msg","无效签名");
        }catch (TokenExpiredException e){
            e.printStackTrace();
            map.put("msg","token过期");
        }catch (AlgorithmMismatchException e){
            e.printStackTrace();
            map.put("msg","token算法不匹配");
        }catch(Exception e){
            e.printStackTrace();
            map.put("msg","JWTInterceptor报告验证失败");
        }

        map.put("state",false);  // 不成功 告诉前端
        // 将map 转为 json 给前端  ResponseBody 底层用的是 jackson  也可以用 fastjson但是要重新引入依赖
        // 项目中默认有 jackson
        String json = new ObjectMapper().writeValueAsString(map);
        response.setContentType("application/json;charset=UTF-8");
        response.getWriter().println(json);
        return false;
    }
}
```



```java
package com.cqp.swagger.config;

import com.cqp.swagger.interceptors.JWTInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new JWTInterceptor())
                          .addPathPatterns("/user/test")      // 其他接口拦截保护 需要token
                          .excludePathPatterns("/user/login");  // 所有用户都放行
    }
}
```



   不带token请求 失败

![image-20210107202841474](shiro.assets/image-20210107202841474.png)



  请求头中带 token  成功

![image-20210107202916656](shiro.assets/image-20210107202916656.png)



builder.withClaim（）必须要声明类型。 因为 生成token时传入的是Map map。id 就必须设为String了。网上别的工具类是传入Map不过用的是JJWT的包。



### 2.开始 Shiro

![image-20210108134934937](shiro.assets/image-20210108134934937.png)



#### 2.1 权限管理

​		权限管理属于系统安全的范畴，权限管理实现对用户访问系统的控制，按照安全规则或者安全策略控制用户可以访问而且只能访问自己被授权的资源。
  权限管理包括**用户身份认证**和**授权**两部分，简称认证授权。对于需要访问控制的资源用户首先经过身份认证，认证通过后用户具有该资源的访问权限方可访问。



#### 2.2 常见权限框架

##### 2.2.1  Shiro简介

  Apache Shiro是Java的一个安全框架。目前，使用Apache Shiro的人越来越多，因为它相当简单，对比Spring Security，可能没有Spring Security做的功能强大，但是在实际工作时可能并不需要那么复杂的东西，所以使用小而简单的Shiro就足够了。对于它俩到底哪个好，这个不必纠结，能更简单的解决项目问题就好了。



##### 2.2.2 Spring Security

​		Spring Security是一个能够为基于Spring的企业应用系统提供声明式的安全访问控制解决方案的安全框架。它提供了一组可以在Spring应用上下文中配置的Bean，充分利用了Spring IoC，DI（控制反转Inversion of Control ,DI:Dependency Injection 依赖注入）和AOP（面向切面编程）功能，为应用系统提供声明式的安全访问控制功能，减少了为企业系统安全控制编写大量重复代码的工作。它是一个轻量级的安全框架，它确保基于Spring的应用程序提供身份验证和授权支持。它与Spring MVC有很好地集成，并配备了流行的安全算法实现捆绑在一起。安全主要包括两个操作“认证”与“验证”（有时候也会叫做权限控制）。“认证”是为用户建立一个其声明的角色的过程，这个角色可以一个用户、一个设备或者一个系统。“验证”指的是一个用户在你的应用中能够执行某个操作。在到达授权判断之前，角色已经在身份认证过程中建立了。



##### 2.2.3 Shiro 和 Spring Security 比较

* Shiro 比 Spring更容易使用，实现和最重要的理解
* Spring Security更加知名的唯一原因是因为品牌名称
* “Spring”以简单而闻名，但讽刺的是很多人发现安装Spring Security很难
* Spring Security却有更好的社区支持
* Apache Shiro在Spring Security处理密码学方面有一个额外的模块
* Spring-security 对spring 结合较好，如果项目用的springmvc ，使用起来很方便。但是如果项目中没有用到spring，那就不要考虑它了。
* Shiro 功能强大、且 简单、灵活。是Apache 下的项目比较可靠，且**不跟任何的框架或者容器绑定**，可以独立运行



2.3 shiro 的核心架构

![image-20210108135814978](shiro.assets/image-20210108135814978.png)



核心类:



**Subject : 主体**

**authenticator : 身份验证者**              ModularRealmAuthenticator

**authorizer: 授权者**          

**Realm : 领域**                                  相当于datasource数据源   （也包括 认证授权的功能） 

**SessionManager : 会话管理**            不依赖 web 容器的 session，所以shiro可以用在非web应用上。也可以将分布式应用的会话集中在一点，此特性可以实现单点登录功能

**CacheManager :** 将用户权限数据存储在缓存，可以提高性能

**Cryptography： 安全数据加密** 

**Web Integration web系统集成**

**Interations 集成其它应用，spring、缓存框架**





### 3. Shiro 认证



判断用户是否合法



关键类

* Subject  主体

​        访问系统的用户，可以是用户，程序。

* Principal   身份信息

​       主体进行身份认证的标识，具有唯一性。一个主体可以有多个身份，但是必须有一个主身份。

* credential  凭证信息

​        只有主体才知道的安全信息，如密码，证书。



**认证流程**

![image-20210109134619730](shiro.assets/image-20210109134619730.png)



查看你token里携带的原始信息是否与我数据库中的一致。



练习使用 shiro，就先新建一个 module maven项目即可。test 和 java 下 新建resources目录。



```xml
<!--引入shiro依赖-->
<dependencies>
  <dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.6.0</version>
  </dependency>
```



![image-20210109140425539](shiro.assets/image-20210109140425539.png)

新建 shiro配置文件 .ini 结尾

ini配置文件 用来学习 shiro  书写我们系统中相关权限数据（方便学习） 后续切换到数据库中的数据 （整合springboot不需要）

```ini
[users]
cqp=970823
iu=930516
```



测试

```java
package com.cqp;

import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.IncorrectCredentialsException;
import org.apache.shiro.authc.UnknownAccountException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.mgt.DefaultSecurityManager;
import org.apache.shiro.realm.text.IniRealm;
import org.apache.shiro.subject.Subject;

public class TestAuthenticatior {
    public static void main(String[] args) {
       // 1.创建安全管理器对象
        DefaultSecurityManager securityManager = new DefaultSecurityManager();

        // 2.给安全管理器设置realm
        securityManager.setRealm(new IniRealm("classpath:shiro.ini"));

        // 3.给安全管理工具类设置安全管理器
        SecurityUtils.setSecurityManager(securityManager);

        // 4. 关键对象 subject
        Subject subject = SecurityUtils.getSubject();

        // 5. 创建令牌token
        UsernamePasswordToken token = new UsernamePasswordToken("iu", "9305162");

        try{
            System.out.println("认证状态:"+subject.isAuthenticated());
            subject.login(token);   // 用户认证 成功就不报异常
            System.out.println("认证状态:"+subject.isAuthenticated());
        }catch (UnknownAccountException e){
            e.printStackTrace();
            System.out.println("认证失败: 用户名不存在");
        }catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("认证失败: 密码不正确");
        }catch (Exception e){
            e.printStackTrace();
        }

    }
}
```



#### 3.1源码分析

![image-20210110153602348](shiro.assets/image-20210110153602348.png)

打上断点，一路深入进去。





![image-20210110153540275](shiro.assets/image-20210110153540275.png)

最后Authenticator  调用 realm 的getAuthenticationInfo方法，在Realm中认证用户的token。





![image-20210110154109370](shiro.assets/image-20210110154109370.png)

在SimpleAcountRealm 根据用户的用户名拿到 account



![image-20210110154824177](shiro.assets/image-20210110154824177.png)



![image-20210110160528019](shiro.assets/image-20210110160528019.png)

AuthenticatingRealm    assertCredentialsMatch(token, info); 中完成密码校验



![image-20210110162003251](shiro.assets/image-20210110162003251.png)





![image-20210110161141227](shiro.assets/image-20210110161141227.png)







![image-20210110151904766](shiro.assets/image-20210110151904766.png)

![image-20210110152122215](shiro.assets/image-20210110152122215.png)





>### 认证：
>
>   1. 最终执行用户名比较  SimpleAccountRealm
>
>      doGetAuthenticationInfo 在这个方法中完成用户名校验
>
>       2. 最终密码校验 AuthenticatingRealm 
>
>​        assertCredentialsMatch   密码校验
>
>​     总结
>
>​        AuthenticatingRealm   认证 realm      doGetAuthenticationInfo
>
>​        AuthorizingRealm      授权 realm       doGetAuthorizationInfo         
>
>​    
>
>​    因为我们现在是读取 .ini 文件的数据
>
>​    认证的数据需要连上数据库就需要覆盖 doGetAuthenticationInfo 这个方法
>
>​    授权的数据也需要连上数据库就需要覆盖  doGetAuthorizationInfo     这个方法
>
>  SimpleAccountRealm 继承了 AuthenticatingRealm AuthorizingRealm 既有doGetAuthenticationInfo 和 doGetAuthorizationInfo 
>
>如果我们日后要自定义 realm的话 我们可以自定义realm去继承 AuthorizingRealm 这样就可以覆盖这个类中的**授权**和**认证**这两个方法
>
>具体的实现我们可以通过 Mybatis 去查询数据库，返回对应的信息，由上一层自动完成密码的比较。



![image-20210110162818272](shiro.assets/image-20210110162818272.png)

![image-20210110162847140](shiro.assets/image-20210110162847140.png)





#### 3.2**自定义Realm**

由上面的源码分析可知，要写一个自定义realm可以继承AuthorizingRealm去实现doGetAuthenticationInfo 和 doGetAuthorizationInfo 方法。

![image-20210111105817323](shiro.assets/image-20210111105817323.png)





```java
package com.cqp.realm;

import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;

/**
 * 自定义realm实现   将认证/授权数据的来源转为数据库的实现
 */
public class  CustomerRealm extends AuthorizingRealm {

     // 授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        return null;
    }

    // 认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        // 在token中获取用户名
        String principal = (String) token.getPrincipal();
        System.out.println(principal);
        // 根据身份信息 使用jdbc mybatis 查询数据库  这里先写死
        if("iu".equals(principal)){
            // 参数1: 返回数据库中的正确用户名  参数2: 返回数据库中的正确密码  参数3：提供当前realm的名字 this.getName
            SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(principal,"930516",this.getName());  // 传入数据库中的用户名和密码
            return simpleAuthenticationInfo;

        }
        return null;
    }
}
```



测试

```java
/**
 * 使用自定义Realm
 * 测试认证
 */
public class TestCustomerRealmAuthenticator {
    public static void main(String[] args) {

        // 创建securityManager
        DefaultSecurityManager defaultSecurityManager = new DefaultSecurityManager();
        // 创建自定义realm
        defaultSecurityManager.setRealm(new CustomerRealm());    // 会调用 realm 中的 doGetAuthenticationInfo 去进行token信息的获取
        // 设置安全工具类
        SecurityUtils.setSecurityManager(defaultSecurityManager);
        // 通过安全工具类获取 subject
        Subject subject = SecurityUtils.getSubject();

        // 创建token 相当于从前端获取
        UsernamePasswordToken token = new UsernamePasswordToken("iu", "930516");

        try{
            subject.login(token);
            System.out.println(subject.isAuthenticated());
        }catch (IncorrectCredentialsException e){
            e.printStackTrace();
            System.out.println("密码错误！");
        }catch (UnknownAccountException e){
            e.printStackTrace();
            System.out.println("用户名错误！");
        }
    }
}
```



#### 3.3 使用 md5 和 salt 加密



> MD5 算法
>
> 作用：一般用来加密 签名（校验和）
>
> 特点：MD5 算法不可逆 如果内容系统无论执行多少次md5生成的结果始终是一致的
>
> 生成结果: 始终是一个16进制 32 位长度字符串



![image-20210111134135508](shiro.assets/image-20210111134135508.png)



MD5 使用

```java
public class TestShiroMD5 {
    public static void main(String[] args) {
        // 使用 MD5
        Md5Hash md5Hash1 = new Md5Hash("123");
        System.out.println(md5Hash1.toHex());

        // 使用MD5 + salt
        Md5Hash md5Hash2 = new Md5Hash("123","@97#$");
        System.out.println(md5Hash2.toHex());

        // 使用MD5 + salt + hash 散列 次数
        Md5Hash md5Hash3 = new Md5Hash("123","@97#$",1024);
        System.out.println(md5Hash3.toHex());
    }
}
```



当数据库中的数据经过 md5 和 salt 加密之后。我们进行验证时也应该做出处理。



### 4. Shiro 授权

访问控制，谁能访问哪些资源。主体进行身份认证后需要分配权限方可访问系统的资源，对于某些资源没有权限是无法访问。

资源：系统菜单、页面、按钮、类方法、商品信息等。可以对一类商品授权，也可以对一个商品授权。

还能规定主体对资源的操作许可。如 用户查询，用户添加权限，某个方法的调用权限。



#### 4.1授权方式

* 基于角色的访问控制 (Role-Based Access Control)  以角色为中心的访问控制

```java
if(subject.hasRole(){
       // 操作什么资源
}
```

​        

* 基于资源的访问控制 (Resource-Based Access Control) 以资源为中心的访问控制

```java
if(subject.isPermission("user:update:01")){   // 资源实例
  // 对01用户进行修改
}
if(subject.isPermission("user:update:*")){   // 资源类型
  //对所有user可以进行修改操作
}
```



#### 4.2 权限字符串

​	权限字符串的规则是："资源标识符 ：操作 : 资源实例表示符 "     

对哪个资源的哪个实例具有什么操作，" : "是分割符。







### 5.Springboot 整合 shiro

![image-20210112152015600](shiro.assets/image-20210112152015600.png)





#### 5.1 认证功能

##### 5.1.1 认证注册

配置jsp

![image-20210112153743727](shiro.assets/image-20210112153743727.png)



```xml
<!--jsp解析依赖-->
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<dependency>
    <groupId>jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

![image-20210112153704973](shiro.assets/image-20210112153704973.png)



jsp结合 springboot配置完毕，可以运行测试。（新建model子项目需要以上步骤）

独立项目放于 static下就可以访问index.jsp



1. 新建 config包 新建ShiroConfig 类

```java
/**
 * 整合shiro框架配置类
 */
@Configuration
public class ShiroConfig {
    // 1.创建shiroFilter   负责拦截所有请求
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        // 给 filter 设置安全管理器
        shiroFilterFactoryBean.setSecurityManager(defaultWebSecurityManager);
      
      // 配置系统受限制资源
        // 配置系统公共资源
        HashMap<String, String> map = new HashMap<>();
        map.put("/index.jsp","authc"); // authc 请求这个资源需要认证和授权
        shiroFilterFactoryBean.setFilterChainDefinitionMap(map);

        // 默认认证界面路径
        shiroFilterFactoryBean.setLoginUrl("login.jsp");
        return shiroFilterFactoryBean;
    }
    // 2.创建安全管理器
    @Bean
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("getRealm") Realm realm){
        DefaultWebSecurityManager defaultWebSecurityManager = new DefaultWebSecurityManager();
        // 安全管理器设置 realm
        defaultWebSecurityManager.setRealm(realm);
        return defaultWebSecurityManager;
    }
    // 3.创建自定义realm
    @Bean
    public Realm getRealm(){
        CustomerRealm customerRealm = new CustomerRealm();
        return customerRealm;
    }
}
```



authc 是shiro过滤器的简写

| 配置缩写          | 对应的过滤器                         | 功能                                                         |
| ----------------- | ------------------------------------ | ------------------------------------------------------------ |
| 身份验证相关的    |                                      |                                                              |
| anon              | AnonymousFilter                      | 指定url可以匿名访问                                          |
| authc             | FormAuthenticationFilter             | 基于表单的拦截器；如“/**=authc”，如果没有登录会跳到相应的登录页面登录；主要属性：usernameParam：表单提交的用户名参数名（ username）； passwordParam：表单提交的密码参数名（password）； rememberMeParam：表单提交的密码参数名（rememberMe）； loginUrl：登录页面地址（/login.jsp）；successUrl：登录成功后的默认重定向地址； failureKeyAttribute：登录失败后错误信息存储key（shiroLoginFailure） |
| authcBasic        | BasicHttpAuthenticationFilter        | Basic HTTP身份验证拦截器，主要属性： applicationName：弹出登录框显示的信息（application） |
| logout            | authc.LogoutFilter                   | 退出拦截器，主要属性：redirectUrl：退出成功后重定向的地址（/） |
| user              | UserFilter                           | 用户拦截器，用户已经身份验证/记住我登录的都可                |
| 授权相关的        |                                      |                                                              |
| roles             | RolesAuthorizationFilter             | 角色授权拦截器，验证用户是否拥有所有角色；主要属性： loginUrl：登录页面地址（/login.jsp）；unauthorizedUrl：未授权后重定向的地址；示例“/admin/**=roles[admin]” |
| perms             | PermissionsAuthorizationFilter       | 权限授权拦截器，验证用户是否拥有所有权限；属性和roles一样；示例“/user/**=perms[“user:create”]” |
| port              | PortFilter                           | 端口拦截器，主要属性：port（80）：可以通过的端口；示例“/test= port[80]”，如果用户访问该页面是非80，将自动将请求端口改为80并重定向到该80端口，其他路径/参数等都一样 |
| rest              | HttpMethodPermissionFilter           | rest风格拦截器，自动根据请求方法构建权限字符串（GET=read, POST=create,PUT=update,DELETE=delete,HEAD=read,TRACE=read,OPTIONS=read, MKCOL=create）构建权限字符串；示例“/users=rest[user]”，会自动拼出“user:read,user:create,user:update,user:delete”权限字符串进行权限匹配（所有都得匹配，isPermittedAll） |
| ssl               | SslFilter                            | SSL拦截器，只有请求协议是https才能通过；否则自动跳转会https端口（443）；其他和port拦截器一样 |
| noSessionCreation | NoSessionCreationAuthorizationFilter | 需要指定权限才能访问                                         |

2. 新建 shiro 包下 新建realms包下 新建 CustomerRealm 

```java
public class CustomerRealm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        return null;
    }
}
```



由于authc 所以要访问index.jsp 需要验证 所以在登录界面做验证

login.jsp 提交表单 新建controller包 新建 userController 在这里做登录的后台处理验证

**ps: 在web环境中只要我们创建了 安全管理器（shiroConfig中）就会自动给shiro的安全工具类注入web安全管理器。**

 这样我们就可以直接用 SecurityUtils.getSubject(); 来获取主体。



```java
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
    System.out.println("====doGetAuthenticationInfo====");

    String principal = (String) token.getPrincipal();

    if("cqp".equals(principal)){
        return new SimpleAuthenticationInfo(principal,"123",this.getName());
    }
    return null;
}
```

这里暂时先写死 用户名:cqp 密码:123 进行认证

 

3.  注册 md5 加盐 加密

![image-20210115134353076](shiro.assets/image-20210115134353076.png)



添加 数据库的依赖

```xml
<!--mybatis-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
<!--mysql-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<!--druid-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.24</version>
</dependency>
```



springboot配置文件

```yml
server:
  port: 8888
  servlet:
    context-path: /shiro

spring:
  application:
    name: shiro
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/shiro?useUnicode=true&useSSL=false&characterEncoding=utf8&serverTimezone=Asia/Shanghai
    username: root
    password: 123456
  mvc:
    view:
      prefix: /
      suffix: .jsp

mybatis:
  type-aliases-package: com.cqp.springboot_jsp_shiro.entity
  mapper-locations: classpath:com/cqp/mapper/*.xml


mybatis-plus:
  mapper-locations: classpath*:/mapper/**Mapper.xml
```



注册页面

```jsp
<%@page contentType="text/html; UTF-8" pageEncoding="UTF-8" isELIgnored="false" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
      <h1>用户注册</h1>
      <form action="${pageContext.request.contextPath}/user/register" method="post">
          用户名:<input type="text" name="username"> <br/>
          密码:  <input type="text" name="password"><br/>
          <input type="submit" value="立即注册">
      </form>

</body>
</html>
```



开发entity

```java
@Data
@Accessors(chain = true)
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private Integer id;
    private String username;
    private String password;
    private String salt;
}
```

dao

```java
package com.cqp.springboot_jsp_shiro.dao;

import com.cqp.springboot_jsp_shiro.entity.User;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface UserDao {

    void save (User user);
}
```

mapper

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.cqp.springboot_jsp_shiro.dao.UserDao">

    <insert id="save" parameterType="User" useGeneratedKeys="true" keyProperty="id">
        insert into t_user values (#{id},#{username},#{password},#{salt})
    </insert>

</mapper>
```

service

```java
public interface UserService{
    // 注册用户方法
    void register(User user);
}
```



serviceImpl

```java
package com.cqp.springboot_jsp_shiro.service;

import com.cqp.springboot_jsp_shiro.dao.UserDao;
import com.cqp.springboot_jsp_shiro.entity.User;
import com.cqp.springboot_jsp_shiro.utils.SaltUtils;
import org.apache.shiro.crypto.hash.Md5Hash;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
@Transactional
public class UserServiceImpl implements UserService{
    @Autowired
    private UserDao userDao;

    @Override
    public void register(User user) {
       // 处理业务调用dao
       // 明文密码进行 md5 + salt + hash 散列

        // 1. 生成盐
        String salt = SaltUtils.getSalt(8);
        // 2. 将随机盐保存到数据库
        user.setSalt(salt);
        // 3. 明文密码进行 md5 salt hash
        Md5Hash md5Hash = new Md5Hash(user.getPassword(),salt,1024);
        System.out.println("md5Hash:"+md5Hash.toString());
        System.out.println("md5Hash。toHex:"+md5Hash.toHex());
        user.setPassword(md5Hash.toHex());
        userDao.save(user);
    }
}
```



随机盐生成工具

```java
package com.cqp.springboot_jsp_shiro.utils;

import java.util.Random;

public class SaltUtils {

    public static String getSalt (int n){
        char[] chars = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz!@#$^&*()_+".toCharArray();
        StringBuilder sb = new StringBuilder();
        for(int i = 0; i < n ; i++){
            char aChar = chars[new Random().nextInt(chars.length)];
            sb.append(aChar);
        }
        return sb.toString();
    }

    public static void main(String[] args) {
        String salt = getSalt(10);
        System.out.println(salt);
    }
}
```



controller

```java
/**
 * 用户注册
 */
@RequestMapping("register")
  public String register(User user){
     try{
        userService.register(user);
         return "redirect:/login.jsp";
     }catch (Exception e){
         e.printStackTrace();
         return "redirect:/register.jsp";
     }

  }
```





##### 5.1.2 认证登录



自定义的realm需要调用业务对象，但是我们的业务对象是交给 spring容器管理（工厂）

**如何给自定义realm注入业务对象？**

​	CustomerRealm 本身是不交给工厂管理的，所以没有办法注入 userService对象

​    我们可以自己去写一个工厂的工具类去解决这个问题。

```java
@Component
public class ApplicationContextUtils implements ApplicationContextAware {

    private static ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
      context = applicationContext;  // 拿到工厂
    }

    // 根据bean名字获取工厂中指定的 bean对象
    public static Object getBean(String beanName){
        return context.getBean(beanName);
    }

}
```





```java
public class CustomerRealm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("====doGetAuthenticationInfo====");
        // 根据身份信息
        String principal = (String) token.getPrincipal();
        // 工厂工具类获取 userService业务对象
        UserService userService = (UserService) ApplicationContextUtils.getBean("userService");

        User user = userService.findByUserName(principal);

        if(!ObjectUtils.isEmpty(user)){
            return new SimpleAuthenticationInfo(user.getUsername(),user.getPassword(), ByteSource.Util.bytes(user.getSalt()),this.getName());
        }
        return null;
    }
}
```



我们现在用的是md5 + 随机盐的加密方式

所以就不能使用默认的凭证匹配器

如果要用 md5 和 随机盐 那么我们就需要在shiro配置中去修改默认使用的简单匹配器  Shiroconfig 中注入的 realm

```java
  // 3.创建自定义realm
    @Bean
    public Realm getRealm(){
        CustomerRealm customerRealm = new CustomerRealm();
        // 修改凭证校验匹配器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        // 设置加密算法为 md5
        credentialsMatcher.setHashAlgorithmName("MD5");
        // 设置散列次数
        credentialsMatcher.setHashIterations(1024);
        customerRealm.setCredentialsMatcher(credentialsMatcher);

        // 全局开启缓存管理
        customerRealm.setCacheManager(new RedisCacheManager());
        customerRealm.setCachingEnabled(true);
        // 开启认证缓存
        customerRealm.setAuthenticationCachingEnabled(true);
        // 给认证的缓存数据设置名字
        customerRealm.setAuthenticationCacheName("authenticationCache");

        // 开启授权缓存
        customerRealm.setAuthorizationCachingEnabled(true);
        // 给授权的缓存数据设置名字
        customerRealm.setAuthorizationCacheName("authorizationCache");
        return customerRealm;
    }
```

#### 5.2 授权功能



三种实现方式

![image-20210121095406992](shiro.assets/image-20210121095406992.png)



进入主页之后要去拿到用户的 权限信息。

用用户的身份信息去拿用户的权限信息时会调用 doGetAuthorizationInfo 方法

返回 AuthorizationInfo 根据这个去控制用户的资源





##### 5.2.1 标签实现



```jsp
<%@page contentType="text/html; UTF-8" pageEncoding="UTF-8" isELIgnored="false" %>
<%@taglib prefix="shiro" uri="http://shiro.apache.org/tags" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
       <h1>系统主页v1.0</h1>
       <a href="${pageContext.request.contextPath}/user/logout">退出登录</a>
       <ul>
           <shiro:hasAnyRoles name="user,admin">
               <li><a href="">用户管理</a>
                   <ul>
                       <shiro:hasPermission name="user:add:*">
                           <li><a href="">添加</a></li>
                       </shiro:hasPermission>
                       <shiro:hasPermission name="user:delete:*">
                           <li><a href="">删除</a></li>
                       </shiro:hasPermission>
                       <shiro:hasPermission name="user:update:*">
                           <li><a href="">修改</a></li>
                       </shiro:hasPermission>
                       <shiro:hasPermission name="user:find:*">
                           <li><a href="">查询</a></li>
                       </shiro:hasPermission>

                   </ul>
               </li>
           </shiro:hasAnyRoles>
           <shiro:hasAnyRoles name="admin">
               <li><a href="">商品管理</a></li>
               <li><a href="">订单管理</a></li>
               <li><a href="">物流管理</a></li>
           </shiro:hasAnyRoles>

       </ul>
</body>
</html>
```



```java
public class CustomerRealm extends AuthorizingRealm {
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        /* 获取身份信息 */
        String primaryPrincipal = (String) principalCollection.getPrimaryPrincipal();
        System.out.println("调用授权认证:"+primaryPrincipal);
        // 根据主身份信息获取角色和权限信息
        if("cqp".equals(primaryPrincipal)){
            SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
            simpleAuthorizationInfo.addRole("user");
            simpleAuthorizationInfo.addStringPermission("user:find:*");
            simpleAuthorizationInfo.addStringPermission("user:update:*");
            return simpleAuthorizationInfo;
        }

        return null;
    }
```

![image-20210121102950489](shiro.assets/image-20210121102950489.png)





##### 5.2.2 代码和注解实现



```java
@Controller
@RequestMapping("order")
public class OrderController {

    @RequestMapping("save")
    //@RequiresRoles(value = {"admin","user"}) // 用来判断角色 这里需要主体有 admin 和 user 两个才行
    @RequiresPermissions("user:update:01") // 用来判断权限字符串
    public String save(){
        // 代码方式 （判断当前的主体对象是否具有订单保存的操作权限）

        // 获取主体对象
        Subject subject = SecurityUtils.getSubject();
        if (subject.hasRole("admin")) {
            System.out.println("保存订单！");
        }else{
            System.out.println("无权访问！");
        }
        // 上面是基于角色的 代码方式也可以基于权限字符串

        return "redirect:/index.jsp";
    }

}
```



##### 5.2.3 数据库获取身份信息



> 数据库设计
>
> ![image-20210121141309281](shiro.assets/image-20210121141309281.png)
>
> 资源和权限一一对应 所以是一张表中的两个字段



![image-20210121160716027](shiro.assets/image-20210121160716027.png)

![image-20210121160817446](shiro.assets/image-20210121160817446.png)





![image-20210121160841576](shiro.assets/image-20210121160841576.png)

![image-20210121160904599](shiro.assets/image-20210121160904599.png)

![image-20210121160914127](shiro.assets/image-20210121160914127.png)



编写 mapper

![image-20210121160954953](shiro.assets/image-20210121160954953.png)



编写 dao service serviceImpl

![image-20210121161033173](shiro.assets/image-20210121161033173.png)

![image-20210121161047107](shiro.assets/image-20210121161047107.png)

![image-20210121161127214](shiro.assets/image-20210121161127214.png)



重写 doGetAuthorizationInfo 方法

```java
protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
    /* 获取身份信息 (用户名)*/
    String primaryPrincipal = (String) principalCollection.getPrimaryPrincipal();
    System.out.println("调用授权认证:"+primaryPrincipal);
    // 根据主身份信息获取角色和权限信息
    // 工厂工具类获取 userService业务对象
    UserService userService = (UserService) ApplicationContextUtils.getBean("userService");
    User user= userService.findRolesByUserName(primaryPrincipal);
    // 授权角色信息
    if(!CollectionUtils.isEmpty(user.getRoles())){
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        user.getRoles().forEach(role -> {
            simpleAuthorizationInfo.addRole(role.getName());
                }
        );
        return simpleAuthorizationInfo;
    }
    return null;
}
```



##### 5.2.4 数据库获取权限信息



1.在Role角色实体类中定义权限的集合

![image-20210122125752844](shiro.assets/image-20210122125752844.png)



2. dao

   ![image-20210122125745260](shiro.assets/image-20210122125745260.png)



3.编写mapper

```xml
<select id="findPermsByRoleId" parameterType="String" resultType="Perms">
    select p.id,p.name,p.url,r.name from t_role r
        LEFT JOIN t_role_perms rp on r.id = rp.roleid
        LEFT JOIN t_perms p on rp.permsid = p.id
    where r.id =#{id}
</select>
```

4. service

   ```java
   // 根据角色id查询权限集合
   List<Perms> findPermsByRoleId(String id);
   ```

5. serviceImpl

   ```java
   @Override
   public List<Perms> findPermsByRoleId(String id) {
       return userDao.findPermsByRoleId(id);
   }
   ```

6. CusdtomerRealm  doGetAuthorization方法

   ```java
   // 授权角色信息
   if(!CollectionUtils.isEmpty(user.getRoles())){
       SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
       user.getRoles().forEach(role -> {
           simpleAuthorizationInfo.addRole(role.getName());
   
           // 权限信息
           List<Perms> perms = userService.findPermsByRoleId(role.getId());
           if(!CollectionUtils.isEmpty(perms)){
         perms.forEach(perm ->{
             simpleAuthorizationInfo.addStringPermission(perm.getName());
         });
           }
               }
       );
   ```

shiro 根据  principal 获取实体





### 6.使用 CacheManager 缓存



* Cache缓存： 计算机内存中一段数据 （数据库数据在磁盘上)

* 作用:  减轻数据库的访问压力，提高系统的查询效率

* 流程：

  ![image-20210130130913032](shiro.assets/image-20210130130913032.png)



#### 6.1  EhCache 实现缓存

shiro默认使用EhCache实现缓存



1. 引入依赖

```xml
<!--引入shiro和ehcache-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-ehcache</artifactId>
    <version>1.5.3</version>
</dependency>
```

2. 开启缓存    config包 ShiroConfig类

```java
@Bean
public Realm getRealm(){
    CustomerRealm customerRealm = new CustomerRealm();
    // 修改凭证校验匹配器
    HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
    // 设置加密算法为 md5
    credentialsMatcher.setHashAlgorithmName("MD5");
    // 设置散列次数
    credentialsMatcher.setHashIterations(1024);
    customerRealm.setCredentialsMatcher(credentialsMatcher);

    // 全局开启缓存管理
    customerRealm.setCacheManager(new EhCacheManager());
    customerRealm.setCachingEnabled(true);
    // 开启认证缓存
    customerRealm.setAuthenticationCachingEnabled(true);
    // 给认证的缓存数据设置名字
    customerRealm.setAuthenticationCacheName("authenticationCache");

    // 开启授权缓存
    customerRealm.setAuthorizationCachingEnabled(true);
    // 给授权的缓存数据设置名字
    customerRealm.setAuthorizationCacheName("authorizationCache");
    return customerRealm;
}
```

设置好之后，只有第一次会操作我们的数据库

重新启动即可

EhCache是本地缓存，在应用内部的缓存，一旦应用停止再次登录时需要重新访问数据库。



#### 6.2 Redis 分布式缓存

1. 引入redis 依赖  (用springboot-starter-data去操作redis)

   ```xml
   <!--redis整合springboot-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 配置 redis 连接

![image-20210130145534651](shiro.assets/image-20210130145534651.png)

3.启动redis服务

4.在 shiro包下新建cache包 

```java
/**
 * 自定义 Shiro-redis 缓存管理器
 * @params String cacheName ：认证或者授权缓存的名字
 */
public class RedisCacheManager implements CacheManager {
    @Override
    public <K, V> Cache<K, V> getCache(String cacheName) throws CacheException {
        System.out.println(cacheName);
        return new RedisCache<K,V>();
    }
}
```

更改 ShiroConfig  改为 RedisCacheManager

![image-20210130152408963](shiro.assets/image-20210130152408963.png)

5. 自定义redisCache 去实现Cache接口

```java
package com.cqp.springboot_jsp_shiro.shiro.cache;


import org.apache.shiro.cache.Cache;
import org.apache.shiro.cache.CacheException;

import java.util.Collection;
import java.util.Set;

/**
 * 自定义redis缓存的实现
 */
public class RedisCache<k,v> implements Cache<k,v> {
   

    @Override
    // 从redis中获取缓存
    public v get(k k) throws CacheException {
        System.out.println("get key:"+k);
        return null;
    }

    @Override
    // 把查询数据库的东西放入缓存
    public v put(k k, v v) throws CacheException {

        System.out.println("put key:"+k);
        System.out.println("put value:"+v);

        return null;
    }

    @Override
    // 清空指定的缓存
    public v remove(k k) throws CacheException {
        return null;
    }

    @Override
    public void clear() throws CacheException {

    }

    @Override
    public int size() {
        return 0;
    }

    @Override
    public Set<k> keys() {
        return null;
    }

    @Override
    public Collection<v> values() {
        return null;
    }
}
```



在 springboot中配置了redis之后，会自动生成redisTemplate去操作redis

使用 ApplicationContextUtils.getBean（"redisTemplate"） 去获取



![image-20210204095217349](shiro.assets/image-20210204095217349.png)



6. 进一步完善 RedisCacheManage 和 RedisCache

```java
/**
 * 自定义 Shiro-redis 缓存管理器
 * @params String cacheName ：认证或者授权缓存的名字
 */
public class RedisCacheManager implements CacheManager {
    @Override
    public <K, V> Cache<K, V> getCache(String cacheName) throws CacheException {
        System.out.println(cacheName);
        return new RedisCache<K,V>(cacheName);
    }
}
```

这里可以传入参数 cacheName 这样采用 redis的hash存储结构，可以给缓存起名字。

一个hash结构可以放入许多个用户的认证,授权信息。

![image-20210204095811598](shiro.assets/image-20210204095811598.png)

```java
package com.cqp.springboot_jsp_shiro.shiro.cache;


import com.cqp.springboot_jsp_shiro.utils.ApplicationContextUtils;
import org.apache.shiro.cache.Cache;
import org.apache.shiro.cache.CacheException;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.util.Collection;
import java.util.Map;
import java.util.Set;

/**
 * 自定义redis缓存的实现
 */
public class RedisCache<k,v> implements Cache<k,v>  {

    private String cacheName;

    public RedisCache() {
    }

    public RedisCache(String cacheName) {
        this.cacheName = cacheName;
    }
    // 换成redis的方法

    @Override
    // 从redis中获取缓存
    public v get(k k) throws CacheException {
        System.out.println("redis get key:"+k);
        return (v) getRedisTemplate().opsForHash().get(this.cacheName,k.toString());

    }

    @Override
    // 把查询数据库的东西放入缓存
    public v put(k k, v v) throws CacheException {

        System.out.println("redis put key:"+k);
        System.out.println("redis put value:"+v);
        getRedisTemplate().opsForHash().put(this.cacheName,k.toString(),v);
        return null;
    }

    @Override
    // 清空指定的缓存
    public v remove(k k) throws CacheException {
        System.out.println("=====remove======");
        return (v) getRedisTemplate().opsForHash().delete(this.cacheName,k.toString());
    }

    @Override
    public void clear() throws CacheException {
        System.out.println("=====clear=====");
          getRedisTemplate().delete(this.cacheName);
    }

    @Override
    public int size() {
        return getRedisTemplate().opsForHash().size(this.cacheName).intValue();
    }

    @Override
    public Set<k> keys() {
        return getRedisTemplate().opsForHash().keys(this.cacheName);
    }

    @Override
    public Collection<v> values() {
        return getRedisTemplate().opsForHash().values(this.cacheName);
    }

    private RedisTemplate getRedisTemplate(){
        RedisTemplate redisTemplate = (RedisTemplate) ApplicationContextUtils.getBean("redisTemplate");
        // RedisTemplate默认对对象进行操作  k是字符串 v就是对象 所以改变redisTemplte k的序列化方式 改为String的序列化方式
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        // Redis 的 Hash 结构   Map<String,Map<String, Object>>;
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```



7. 解决salt的序列化问题

![image-20210204100036880](shiro.assets/image-20210204100036880.png)

  我们要自己写一个ByteSource去处理盐的序列化问题。

```java
package com.cqp.springboot_jsp_shiro.shiro.salt;

import org.apache.shiro.codec.Base64;
import org.apache.shiro.codec.CodecSupport;
import org.apache.shiro.codec.Hex;
import org.apache.shiro.util.ByteSource;
import org.apache.shiro.util.SimpleByteSource;

import java.io.File;
import java.io.InputStream;
import java.io.Serializable;
import java.util.Arrays;
//自定义salt实现 实现序列化接口
public class MyByteSource implements ByteSource,Serializable {
    private byte[] bytes;
    private String cachedHex;
    private String cachedBase64;

    //实现序列化和反序列化
    public MyByteSource() {
    }

    public MyByteSource(byte[] bytes) {
        this.bytes = bytes;
    }

    public MyByteSource(char[] chars) {
        this.bytes = CodecSupport.toBytes(chars);
    }

    public MyByteSource(String string) {
        this.bytes = CodecSupport.toBytes(string);
    }

    public MyByteSource(ByteSource source) {
        this.bytes = source.getBytes();
    }

    public MyByteSource(File file) {
        this.bytes = (new MyByteSource.BytesHelper()).getBytes(file);
    }

    public MyByteSource(InputStream stream) {
        this.bytes = (new MyByteSource.BytesHelper()).getBytes(stream);
    }

    public static boolean isCompatible(Object o) {
        return o instanceof byte[] || o instanceof char[] || o instanceof String || o instanceof ByteSource || o instanceof File || o instanceof InputStream;
    }

    public byte[] getBytes() {
        return this.bytes;
    }

    public boolean isEmpty() {
        return this.bytes == null || this.bytes.length == 0;
    }

    public String toHex() {
        if(this.cachedHex == null) {
            this.cachedHex = Hex.encodeToString(this.getBytes());
        }
        return this.cachedHex;
    }

    public String toBase64() {
        if(this.cachedBase64 == null) {
            this.cachedBase64 = Base64.encodeToString(this.getBytes());
        }

        return this.cachedBase64;
    }

    public String toString() {
        return this.toBase64();
    }

    public int hashCode() {
        return this.bytes != null && this.bytes.length != 0? Arrays.hashCode(this.bytes):0;
    }

    public boolean equals(Object o) {
        if(o == this) {
            return true;
        } else if(o instanceof ByteSource) {
            ByteSource bs = (ByteSource)o;
            return Arrays.equals(this.getBytes(), bs.getBytes());
        } else {
            return false;
        }
    }

    private static final class BytesHelper extends CodecSupport {
        private BytesHelper() {
        }

        public byte[] getBytes(File file) {
            return this.toBytes(file);
        }

        public byte[] getBytes(InputStream stream) {
            return this.toBytes(stream);
        }
    }
}
```



最后在 customrealm 的doGetAuthenticationInfo方法中将盐的序列化改为我们自己的。

```java
// 这里传salt shiro自动处理salt
if(!ObjectUtils.isEmpty(user)){
    return new SimpleAuthenticationInfo(user.getUsername(),user.getPassword(), new MyByteSource(user.getSalt()),this.getName());
}
```





ps:

在做项目的时候遇到一个坑，使用Redis模板类无法持久化自定义类

注入RedisTemplate对象在指定泛型类型的时候不能直接使用@Autowire,要使用@Resource根据名字注入

因为在SpringBoot中，RedisTemplate是自动配置的，相当于容器中有一个默认的RedisTemplate实例，文档中描述如下：

> If you add your own @Bean of any of the auto-configured types, it replaces the default (except in the case of RedisTemplate, when the exclusion is based on the bean name, redisTemplate, not its type).

也就是说，通常情况下，如果针对自动配置类型添加自己的Bean，它将取代默认的，**但是RedisTemplate恰好不在"通常情况**之列，因此扫描进入容器之后不能用@Aautowire类型匹配，而是bean的名字，因此用@Resource

```
@Resource//不能使用@Autowired，如果非要使用，不注明泛型类型即可



private RedisTemplate<String, Object> redisTemplate;
```

但是在注入成功之后，我再对计算结果序列化的时候报了标题所示的错误，之后实现了Serializable接口仍无济于事，最后发现我的包装类是以内部类的形式存在的，**把要序列化的内部类独立出去，问题解决**。





### 7.图片验证码

1. 验证码工具类

```java
package com.cqp.springboot_jsp_shiro.utils;

import javax.imageio.ImageIO;
import java.awt.*;
import java.awt.geom.AffineTransform;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.util.Arrays;
import java.util.Random;

/**
 *@创建人  cx
 *@创建时间  2018/11/27 17:36
 *@描述   验证码生成
 */
public class VerifyCodeUtils{

    //使用到Algerian字体，系统里没有的话需要安装字体，字体只显示大写，去掉了1,0,i,o几个容易混淆的字符
    public static final String VERIFY_CODES = "23456789ABCDEFGHJKLMNPQRSTUVWXYZ";
    private static Random random = new Random();


    /**
     * 使用系统默认字符源生成验证码
     * @param verifySize    验证码长度
     * @return
     */
    public static String generateVerifyCode(int verifySize){
        return generateVerifyCode(verifySize, VERIFY_CODES);
    }
    /**
     * 使用指定源生成验证码
     * @param verifySize    验证码长度
     * @param sources   验证码字符源
     * @return
     */
    public static String generateVerifyCode(int verifySize, String sources){
        if(sources == null || sources.length() == 0){
            sources = VERIFY_CODES;
        }
        int codesLen = sources.length();
        Random rand = new Random(System.currentTimeMillis());
        StringBuilder verifyCode = new StringBuilder(verifySize);
        for(int i = 0; i < verifySize; i++){
            verifyCode.append(sources.charAt(rand.nextInt(codesLen-1)));
        }
        return verifyCode.toString();
    }

    /**
     * 生成随机验证码文件,并返回验证码值
     * @param w
     * @param h
     * @param outputFile
     * @param verifySize
     * @return
     * @throws IOException
     */
    public static String outputVerifyImage(int w, int h, File outputFile, int verifySize) throws IOException{
        String verifyCode = generateVerifyCode(verifySize);
        outputImage(w, h, outputFile, verifyCode);
        return verifyCode;
    }

    /**
     * 输出随机验证码图片流,并返回验证码值
     * @param w
     * @param h
     * @param os
     * @param verifySize
     * @return
     * @throws IOException
     */
    public static String outputVerifyImage(int w, int h, OutputStream os, int verifySize) throws IOException{
        String verifyCode = generateVerifyCode(verifySize);
        outputImage(w, h, os, verifyCode);
        return verifyCode;
    }

    /**
     * 生成指定验证码图像文件
     * @param w
     * @param h
     * @param outputFile
     * @param code
     * @throws IOException
     */
    public static void outputImage(int w, int h, File outputFile, String code) throws IOException{
        if(outputFile == null){
            return;
        }
        File dir = outputFile.getParentFile();
        if(!dir.exists()){
            dir.mkdirs();
        }
        try{
            outputFile.createNewFile();
            FileOutputStream fos = new FileOutputStream(outputFile);
            outputImage(w, h, fos, code);
            fos.close();
        } catch(IOException e){
            throw e;
        }
    }

    /**
     * 输出指定验证码图片流
     * @param w
     * @param h
     * @param os
     * @param code
     * @throws IOException
     */
    public static void outputImage(int w, int h, OutputStream os, String code) throws IOException{
        int verifySize = code.length();
        BufferedImage image = new BufferedImage(w, h, BufferedImage.TYPE_INT_RGB);
        Random rand = new Random();
        Graphics2D g2 = image.createGraphics();
        g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING,RenderingHints.VALUE_ANTIALIAS_ON);
        Color[] colors = new Color[5];
        Color[] colorSpaces = new Color[] { Color.WHITE, Color.CYAN,
                Color.GRAY, Color.LIGHT_GRAY, Color.MAGENTA, Color.ORANGE,
                Color.PINK, Color.YELLOW };
        float[] fractions = new float[colors.length];
        for(int i = 0; i < colors.length; i++){
            colors[i] = colorSpaces[rand.nextInt(colorSpaces.length)];
            fractions[i] = rand.nextFloat();
        }
        Arrays.sort(fractions);

        g2.setColor(Color.GRAY);// 设置边框色
        g2.fillRect(0, 0, w, h);

        Color c = getRandColor(200, 250);
        g2.setColor(c);// 设置背景色
        g2.fillRect(0, 2, w, h-4);

        //绘制干扰线
        Random random = new Random();
        g2.setColor(getRandColor(160, 200));// 设置线条的颜色
        for (int i = 0; i < 20; i++) {
            int x = random.nextInt(w - 1);
            int y = random.nextInt(h - 1);
            int xl = random.nextInt(6) + 1;
            int yl = random.nextInt(12) + 1;
            g2.drawLine(x, y, x + xl + 40, y + yl + 20);
        }

        // 添加噪点
        float yawpRate = 0.05f;// 噪声率
        int area = (int) (yawpRate * w * h);
        for (int i = 0; i < area; i++) {
            int x = random.nextInt(w);
            int y = random.nextInt(h);
            int rgb = getRandomIntColor();
            image.setRGB(x, y, rgb);
        }

        shear(g2, w, h, c);// 使图片扭曲

        g2.setColor(getRandColor(100, 160));
        int fontSize = h-4;
        Font font = new Font("Algerian", Font.ITALIC, fontSize);
        g2.setFont(font);
        char[] chars = code.toCharArray();
        for(int i = 0; i < verifySize; i++){
            AffineTransform affine = new AffineTransform();
            affine.setToRotation(Math.PI / 4 * rand.nextDouble() * (rand.nextBoolean() ? 1 : -1), (w / verifySize) * i + fontSize/2, h/2);
            g2.setTransform(affine);
            g2.drawChars(chars, i, 1, ((w-10) / verifySize) * i + 5, h/2 + fontSize/2 - 10);
        }

        g2.dispose();
        ImageIO.write(image, "jpg", os);
    }

    private static Color getRandColor(int fc, int bc) {
        if (fc > 255){
            fc = 255;}
        if (bc > 255){
            bc = 255;}
        int r = fc + random.nextInt(bc - fc);
        int g = fc + random.nextInt(bc - fc);
        int b = fc + random.nextInt(bc - fc);
        return new Color(r, g, b);
    }

    private static int getRandomIntColor() {
        int[] rgb = getRandomRgb();
        int color = 0;
        for (int c : rgb) {
            color = color << 8;
            color = color | c;
        }
        return color;
    }

    private static int[] getRandomRgb() {
        int[] rgb = new int[3];
        for (int i = 0; i < 3; i++) {
            rgb[i] = random.nextInt(255);
        }
        return rgb;
    }

    private static void shear(Graphics g, int w1, int h1, Color color) {
        shearX(g, w1, h1, color);
        shearY(g, w1, h1, color);
    }

    private static void shearX(Graphics g, int w1, int h1, Color color) {

        int period = random.nextInt(2);

        boolean borderGap = true;
        int frames = 1;
        int phase = random.nextInt(2);

        for (int i = 0; i < h1; i++) {
            double d = (double) (period >> 1)
                    * Math.sin((double) i / (double) period
                    + (6.2831853071795862D * (double) phase)
                    / (double) frames);
            g.copyArea(0, i, w1, 1, (int) d, 0);
            if (borderGap) {
                g.setColor(color);
                g.drawLine((int) d, i, 0, i);
                g.drawLine((int) d + w1, i, w1, i);
            }
        }

    }

    private static void shearY(Graphics g, int w1, int h1, Color color) {

        int period = random.nextInt(40) + 10; // 50;

        boolean borderGap = true;
        int frames = 20;
        int phase = 7;
        for (int i = 0; i < w1; i++) {
            double d = (double) (period >> 1)
                    * Math.sin((double) i / (double) period
                    + (6.2831853071795862D * (double) phase)
                    / (double) frames);
            g.copyArea(i, 0, 1, h1, 0, (int) d);
            if (borderGap) {
                g.setColor(color);
                g.drawLine(i, (int) d, i, 0);
                g.drawLine(i, (int) d + h1, i, h1);
            }

        }

    }
    public static void main(String[] args) throws IOException {
        //获取验证码
        String s = generateVerifyCode(4);
        //将验证码放入图片中
        outputImage(260,60,new File("/Users/chenyannan/Desktop/安工资料/aa.jpg"),s);
        System.out.println(s);
    }
}
```



2. 开发页面

```jsp
<%@page contentType="text/html; UTF-8" pageEncoding="UTF-8" isELIgnored="false" %>
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
      <h1>用户登录</h1>
      <form action="${pageContext.request.contextPath}/user/login" method="post">
          用户名:<input type="text" name="username"> <br/>
          密码:  <input type="text" name="password"><br/>
          请输入验证码: <input type="text" name="code"><img src="${pageContext.request.contextPath}/user/getImage" alt=""><br/>
          <input type="submit" value="登录">
      </form>

</body>
</html>
```



3. 开发控制器





```java
/**
 * 验证码方法
 */
@RequestMapping("getImage")
public void getImage(HttpSession session, HttpServletResponse response) throws IOException {
    // 生成验证码
    String code = VerifyCodeUtils.generateVerifyCode(4);
    // 验证码放入 session
    session.setAttribute("code",code);
    // 验证码存入图片
    ServletOutputStream os = response.getOutputStream();
    response.setContentType("image/png");
    VerifyCodeUtils.outputImage(220,60,os,code);
}
```



4. 开放 shiro允许

![image-20210204105435339](shiro.assets/image-20210204105435339.png)



5. 重写登录逻辑



```java
/**
 * 用来处理身份认证
 * @param username
 * @param password
 * @return
 */
@RequestMapping("login")
public String login(String username,String password,String code,HttpSession session){
    //比较验证码
    String SessionCode = (String) session.getAttribute("code");
        try {
            if (SessionCode.equalsIgnoreCase(code)) {
                // 获取主体对象
                Subject subject = SecurityUtils.getSubject();
                // 验证通过什么都不做 验证不通过报错
                subject.login(new UsernamePasswordToken(username, password));
                return "redirect:/index.jsp";       }
            else {
                throw new RuntimeException("验证码错误!");
                 }
            } catch(UnknownAccountException e){
                e.printStackTrace();
                System.out.println("用户名错误!");
            }catch(IncorrectCredentialsException e){
                e.printStackTrace();
                System.out.println("密码错误!");
            }catch (Exception e){
                e.printStackTrace();
                System.out.println(e.getMessage());
                                }
    return "redirect:/login.jsp";
}
```

