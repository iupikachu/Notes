## maven







```xml
  <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.0.2</version>
<!--                <configuration>-->
<!--                    <archive>-->
<!--                        <manifest>-->
<!--                            <addClasspath>true</addClasspath>-->
<!--                            <mainClass>zjut.iiotplatform.io.ibe.IBEApplication</mainClass>-->
<!--                        </manifest>-->
<!--                    </archive>-->
<!--                </configuration>-->
            </plugin>

            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <classifier>exec</classifier>
                    <includeSystemScope>true</includeSystemScope>
                </configuration>
            </plugin>

            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                    <compilerArguments>
<!--                        <verbose />-->
                        <!-- 将jdk的依赖jar打入项目中，这样项目中使用的jdk的依赖就尅正常使用 -->
                        <extdirs>${project.basedir}/src/main/resources/META-INF/lib/</extdirs>
                    </compilerArguments>
                </configuration>
            </plugin>
        </plugins>
        <resources>
            <resource>
                <directory>${project.basedir}/src/main/resources/META-INF/lib/</directory>
                <targetPath>BOOT-INF/lib/</targetPath>
                <includes>
                    <include>**/*.jar</include>
                </includes>
            </resource>
        </resources>
    </build>
```

