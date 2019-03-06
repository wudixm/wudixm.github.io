## tomcat 中autodeploy 和reloadable

```xml
<Context docBase="xxx" path="/xxx" reloadable="true"/> 

<Context docBase="xxx" path="/xxx" autoDeploy="true"/>  
```

替换`WEB-INF/lib`目录中的jar文件或`WEB-INF/classes`目录中的class文件时，`reloadable="true"`会让修改生效（但代价不小），该选项适合调试。参阅：
[https://tomcat.apache.org/tom...](https://tomcat.apache.org/tomcat-9.0-doc/config/context.html)

在webapps目录中增加新的目录、war文件、修改`WEB-INF/web.xml`，`autoDeploy="true"`会新建或重新部署应用，该选项方便部署。参阅：
[https://tomcat.apache.org/tom...](https://tomcat.apache.org/tomcat-9.0-doc/deployer-howto.html)

### 注意

我个人建议产品环境将这两个选项都关闭掉。

tomcat自身的热部署并不可靠，实际体验发现热部署成功率很低，所以干脆关掉以节省资源，事实上很少有人会使用tomcat的热部署更新应用，大多都选择重启tomcat替换war包的形式。

reloadable的作用是当你修改了jsp之后会自动编译class文件，等于即改即生效，但是代价并不低，适合本地开发调试，产品发布一般都会选择打包war包，里面都是编译好的class，也就不需要这个参数了，甚至连JDK都不需要，只需要JRE就能运行war包。