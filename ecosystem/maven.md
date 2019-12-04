
## maven 命令

#### 创建项目

        mvn archetype:create 
                -DgroupId=com.zang.maven  
                -DartifactId=system-parent 
                -DarchetypeArtifactId=maven-archetype-quickstart 
                -DinteractiveMode=false 
> maven3.0.5以上版本舍弃了create，使用generate生成项目

#### 发布第三方Jar到本地库中：

        mvn install:install-file 
                -DgroupId=com 
                -DartifactId=test 
                -Dversion=0.1.0 
                -Dpackaging=jar 
                -Dfile=d:\test-0.1.0.jar

#### 查看依赖项树

        mvn dependency:tree
        
        
#### scope 分类

| 代码 | 说明 |
| ---- | ------------------ |
|compile|默认的范围；如果没有提供一个范围，那该依赖的范围就是编译范围。编译范围依赖在所有的classpath 中可用，同时它们也会被打包|
|provided|依赖只有在当JDK 或者一个容器已提供该依赖之后才使用。例如， 如果你开发了一个web 应用，你可能在编译 classpath 中需要可用的Servlet API 来编译一个servlet，但是你不会想要在打包好的WAR 中包含这个Servlet API；这个Servlet API JAR 由你的应用服务器或者servlet 容器提供。已提供范围的依赖在编译classpath （不是运行时）可用。它们不是传递性的，也不会被打包。|
|runtime|依赖在运行和测试系统的时候需要，但在编译的时候不需要。比如，你可能在编译的时候只需要JDBC API JAR，而只有在运行的时候才需要JDBC驱动实现。|
| test|test范围依赖 在一般的编译和运行时都不需要，它们只有在测试编译和测试运行阶段可用|
|system|system范围依赖与provided 类似，但是你必须显式的提供一个对于本地系统中JAR 文件的路径。这么做是为了允许基于本地对象编译，而这些对象是系统类库的一部分。这样的构件应该是一直可用的，Maven 也不会在仓库中去寻找它。如果你将一个依赖范围设置成系统范围，你必须同时提供一个 systemPath 元素。注意该范围是不推荐使用的（你应该一直尽量去从公共或定制的 Maven 仓库中引用依赖）|
***

## <a id="lifecycle">[maven 生命周期](https://www.cnblogs.com/EasonJim/p/6816340.html)</a>    
生命周期（lifecycle）由各个阶段组成，每个阶段由maven的插件plugin来执行完成。生命周期（lifecycle）主要包括clean、resources、complie、install、package、testResources、testCompile等，其中带test开头的都是用业编译测试代码或运行单元测试用例的。 

### package、install、deploy 区别
* mvn package
依次执行了resources、compile、testResources、testCompile、test、jar(打包)等阶段。
package命令完成了项目编译、单元测试、打包功能；但没有把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库

* mvn install
依次执行了resources、compile、testResources、testCompile、test、jar(打包)、install等阶段。   
install命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库；但没有布署到远程maven私服仓库

* mvn deploy
依次执行了resources、compile、testResources、testCompile、test、jar(打包)、install、deploy等阶段。
deploy命令完成了项目编译、单元测试、打包功能，同时把打好的可执行jar包（war包或其它形式的包）布署到本地maven仓库和远程maven私服仓库

各阶段执行对应的插件

| 命令 | 插件 | 说明 |
| -- | ----- | -------------- |
|resources|maven-resources-plugin| 无 |
|compile|maven-compiler-plugin||
|testResources|maven-resources-plugin||
|testCompile|maven-compiler-plugin||
|test|maven-surefire-plugin||
|jar|maven-jar-plugin||
|install|maven-install-plugin||
|repackage|spring-boot-maven-plugin|使用 Spring Boot 打包的插件|


## maven 插件

####  maven-jar-plugin
maven 默认的打包插件。使用 Maven 构建一个 JAR 文件比较容易：只要定义项目包装为 “jar”，然后执行包装[生命周期](#lifecycle)阶段即可。但是定义一个可执行 JAR 文件却比较麻烦。

```java
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <configuration>
        <archive>
            <manifest>
                <addClasspath>true</addClasspath>
                <classpathPrefix>lib/</classpathPrefix>
                <mainClass>com.mypackage.MyClass</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```


#### spring-boot-maven-plugin
maven springboot 打包插件：

```java
<plugins>
    <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <version>${spring.boot.version}</version>
        <executions>
            <execution>
                <goals>
                    <goal>repackage</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
</plugins>    
```
> 打完包会多一个目录BOOT-INF

## maven 错误
* Remove all your failed downloads  

Linux                   

        find ~/.m2  -name "*.lastUpdated" -exec grep -q "Could not transfer" {} \; -print -exec rm {} \;

Windows                 

        cd %userprofile%\.m2\repository
        for /r %i in (*.lastUpdated) do del %i

* maven 项目关于Eclipse报:" cannot be read or is not a valid ZIP file"     

这中情况 说明依赖的jar包没有完全下好。到repository 中删去相应的jar目录 让maven重新去下载。
如果还报错，删除maven报错，eclipse中打开pom.xml 编辑状态 重新保存。看看最新的报错信息，进入对应的目录，删除即可
