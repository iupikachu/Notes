## 部署sprigboot项目



### 1.jar包部署

jar包方式启动，也就是使用spring boot内置的tomcat运行。服务器上面只要你配置了jdk1.8及以上，就ok。不需要外置tomcat

* maven打包 (直接install)
* 上传到服务器
* nohup java -jar test.jar >temp.txt  (后台运行jar包，并把日志输出到temp.txt中)





### 2.war包部署

将项目打成war包，放入tomcat 的webapps目录下面，启动tomcat，即可访问。

* pom文件

![image-20210303085842141](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20210303085842141.png)

```xml
<build>
    <defaultGoal>compile</defaultGoal>
    <finalName>lab</finalName>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.19.1</version>
            <configuration>
                <skip>true</skip>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>

        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.0.0</version>
        </plugin>
    </plugins>
</build>
```



* 启动类

```java
@SpringBootApplication
public class LabApplication extends SpringBootServletInitializer {

    public static void main(String[] args) {
        SpringApplication.run(LabApplication.class, args);
    }
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(LabApplication.class);
    }

}
```



* maven clean  package 然后有了war包
* 进入服务器，到 tomcat bin 目录下关闭 tomcat ./shutdown.sh
* 进入tomcat webapps 目录下上传war包
* 进入 bin 目录，重启tomcat ./startup.sh （会自动解压war包生成项目文件）
* 查看日志文件看是否成功启动 tomcat 的 logs目录  tail -f catalina.out  （Ctrl+c退出tail）

