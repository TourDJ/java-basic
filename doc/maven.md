
添加jar包到本地Maven仓库
```
mvn install:install-file -DgroupId=com.baidu -DartifactId=ueditor -Dversion=1.0.0 -Dpackaging=jar -Dfile=ueditor-1.1.2.jar
```
注意：这个命令不能换行，中间用空格来分割，添加完成后就可以在 maven 中引入了。
