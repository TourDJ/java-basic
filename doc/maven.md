
添加jar包到本地Maven仓库
```
mvn install:install-file -DgroupId=com.baidu -DartifactId=ueditor -Dversion=1.0.0 -Dpackaging=jar -Dfile=ueditor-1.1.2.jar
```
注意：这个命令不能换行，中间用空格来分割，添加完成后就可以在 maven 中引入了。



需要在打包的时候依赖本地jar，需要修改增加如下配置（maven3.1+）：
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
        <encoding>UTF-8</encoding>
        <compilerArgs> 
            <arg>-extdirs</arg> 
            <arg>${project.basedir}/src/lib</arg>
        </compilerArgs> 
    </configuration>
</plugin>
```
