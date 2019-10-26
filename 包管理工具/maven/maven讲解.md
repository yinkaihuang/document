# maven常用操作

> [maven打包](#package)
>
> [上传包到私服](#uploadJar)



## <span id="package">maven打包</span>



**第一种方式（这种不会将依赖包加入进去**

```java
 <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <mainClass>com.intellif.Application</mainClass>
                            <classpathPrefix>lib</classpathPrefix>
                            <useUniqueVersions>false</useUniqueVersions>
                        </manifest>
                        <manifestEntries>
                            <Class-Path>lib/</Class-Path>
                        </manifestEntries>
                    </archive>
                </configuration>
       </plugin>
```

**第二种方式（这种会将依赖包整合到当前jar里面**

```java
 <plugins>
            <plugin>
                <artifactId>maven-assembly-plugin</artifactId>
                <configuration>
                    <appendAssemblyId>false</appendAssemblyId>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                    <archive>
                        <manifest>
                            <!-- 此处指定main方法入口的class -->
                            <mainClass>MongoDBTest</mainClass>
                        </manifest>
                    </archive>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id>
                        <phase>package</phase>
                        <goals>
                            <goal>assembly</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
   </plugins>
```



## <span id="uploadJar">上传包到私服</span>

**在本地maven的settings.xml添加账号和密码**

```
 <servers>
  	<server>
      <id>if_release</id>
      <username>ifuser</username>
      <password>123456</password>
    </server>
	<server>
      <id>if_snapshots</id>
      <username>ifuser</username>
      <password>123456</password>
    </server>
  </servers>
```



**在项目pom文件添加如下内容**

```
<properties>
   <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
   <java.version>1.8</java.version>
</properties>

<repositories>
        <repository>
            <id>Central</id>
            <url>http://192.168.2.34:8081/nexus/content/groups/public/</url>
        </repository>
 </repositories>
 <distributionManagement>
        <repository>
            <id>if_release</id>
            <url>http://192.168.2.34:8081/nexus/content/repositories/if_release</url>
        </repository>
        <snapshotRepository>
            <id>if_snapshots</id>
            <url>http://192.168.2.34:8081/nexus/content/repositories/if_snapshots</url>
        </snapshotRepository>
 </distributionManagement>
    
    
  <build>
   <pluginManagement>
      <plugins>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.1</version>
            <configuration>
               <source>1.8</source>
               <target>1.8</target>
               <encoding>${project.build.sourceEncoding}</encoding>
            </configuration>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>2.2.1</version>
            <executions>
               <execution>
                  <id>attach-sources</id>
                  <goals>
                     <goal>jar</goal>
                  </goals>
               </execution>
            </executions>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-javadoc-plugin</artifactId>
            <version>2.8.1</version>
            <configuration>
               <charset>UTF-8</charset>
               <encoding>UTF-8</encoding>
               <docencoding>UTF-8</docencoding>
               <quiet>true</quiet>
               <serialwarn>false</serialwarn>
            </configuration>
            <executions>
               <execution>
                  <id>attach-javadocs</id>
                  <phase>package</phase>
                  <goals>
                     <goal>jar</goal>
                  </goals>
               </execution>
            </executions>
         </plugin>
         <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>versions-maven-plugin</artifactId>
            <version>2.1</version>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.12.4</version>
            <configuration>
               <skipTests>true</skipTests>
            </configuration>
         </plugin>
         <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-jar-plugin</artifactId>
            <version>3.0.0</version>
            <executions>
               <execution>
                  <phase>test-compile</phase>
                  <goals>
                     <goal>test-jar</goal>
                  </goals>
               </execution>
            </executions>
            <configuration>
               <excludes>
                  <exclude>**/*.properties</exclude>
               </excludes>
            </configuration>
         </plugin>
      </plugins>
   </pluginManagement>
   <plugins>
      <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-source-plugin</artifactId>
      </plugin>
   </plugins>
</build>
```

**修改intellij的maven的setting为第一步修改的settings.xml**

![](G:\document\resource\maven_1.png)



**执行maven deploy即可**

![](G:\document\resource\maven2.png)