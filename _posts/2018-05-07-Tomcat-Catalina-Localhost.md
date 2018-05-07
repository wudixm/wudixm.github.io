### tomcat config 目录下的catalina/localhost

```
有一个conf文件夹它下面存在一个Catalina/localhost路径，tomcat启动时会默认去这个路径扫描你的配置，如果我们在这个路径下配置了我们的配置文件ROOT.xml[注：这里默认使用ROOT名称，也可以使用自己的工程名，但我不建议这么做]

<?xml version="1.0" encoding="utf-8"?> 
<Context docBase="/opt/web/test" path="/"  workDir="work/Catalina/test" reload="true" />
1
2
docBase：属性表示具体的工程也就是部署的war解压后的路径 
path：为你要访问的工程路径 
workDir： 在上图中有个work文件夹，然后创建一个这个目录work/Catalina/m 
这样配置好后我们输入地址访问工程tomcat会找到ROOT.xml通过配置找到opt/web/test的工程具体位置。 
这是一种部署方式，当然我们也可以直接在tomcat路径下webapps下解压war包进行部署。 
以上这些都是tomcat静态部署。动态部署以后补上。。。


```

### 文件不存在

```
近日在启动Tomcat下的sh文件时报错，报错如下：

Cannot find ./catalina.sh
The file is absent or does not have execute permission
This file is needed to run this program
解决方法：

在tomcat 的bin目录下 执行这条命令
chmod +x *.sh
再次执行 sh catalina.sh通过，
然后用sh startup.sh启动成功

作者：梦沉薇露
链接：https://www.jianshu.com/p/9c7f087e1c72
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

