维护者：<a href="http://weibo.com/2482348040/" target="_blank">淘宝周方</a>  
##宙斯(zeus)是什么
宙斯是一个完整的Hadoop的作业平台  
从Hadoop任务的调试运行到生产任务的周期调度
宙斯支持任务的整个生命周期

从功能上来说，支持：  
Hadoop MapReduce任务的调试运行  
Hive任务的调试运行  
Shell任务的运行    
Hive元数据的可视化查询与数据预览  
Hadoop任务的自动调度  
完整的文档管理  


 
###宙斯开源，不仅仅是开源技术，更是开源产品  
开发中心，一个文档管理，开发调试的环境，在任务上线前的主要工作区域  
<a href="http://xuhengfei.github.io/assets/images/articles/zeus/snapshot-dev.png" target="_blank"><img src="http://xuhengfei.github.io/assets/images/articles/zeus/snapshot-dev.png" /></a>
调度中心，生产任务的调度环境，当任务调试通过后，在此处配置调度信息进行生产调度  
<a href="http://xuhengfei.github.io/assets/images/articles/zeus/snapshot-schedule.png" target="_blank"><img src="http://xuhengfei.github.io/assets/images/articles/zeus/snapshot-schedule.png" /></a>  

##宙斯运行原理
<a href="http://xuhengfei.github.io/assets/images/articles/zeus/graph-network.png" target="_blank"><img src="http://xuhengfei.github.io/assets/images/articles/zeus/graph-network.png" /></a>  

<a href="http://xuhengfei.github.io/assets/images/articles/zeus/graph-struct.png" target="_blank"><img src="http://xuhengfei.github.io/assets/images/articles/zeus/graph-struct.png" /></a>  

<a href="http://xuhengfei.github.io/assets/images/articles/zeus/graph-workflow.png" target="_blank"><img src="http://xuhengfei.github.io/assets/images/articles/zeus/graph-workflow.png" /></a>  

<a href="http://xuhengfei.github.io/assets/images/articles/zeus/graph-schedule.png" target="_blank"><img src="http://xuhengfei.github.io/assets/images/articles/zeus/graph-schedule.png" /></a>   

##使用指南    
快速启动(Quick Start)：  

###快速预览方案：  
0.安装 dos2unix 命令  
1.下载war包 <a href="http://xuhengfei.com/assets/downloads/zeus.war" target="_blank">下载</a>  
2.本地创建Mysql数据库，创建用户:zeus,密码:zeus,创建数据库:zeus  
3.将war包放在web容器下启动  
快速预览方案能够启动web应用，使用shell脚本。但是不能使用其他更多功能(因为没有配置好),更多功能请下载源码按照下面说明进行配置  

###源码下载配置方案：  
1.设置配置项  
在/web/src/main/filter/antx.properties 中对配置项进行设置  
设置完成后，复制到${user.home}/antx.properties处  
2.pom.xml本地jar地址修改  
在/web/pom.xml中修改properties中的local.highcharts  
因为此jar不在maven仓库中，此jar已经在/web/libs/highcharts-1.4.0.jar  
将systemPath路径设置为绝对路径  
3.数据库配置  
创建zeus数据库和hive数据库，启动后数据库里面的表会自动创建  
zeus数据库:/web/src/main/resources/persistence.xml中对数据库进行配置  
hive元数据库:HIVE_HOME/conf 下的hive-site.xml进行配置  
4.环境配置  
安装 dos2unix 命令  
解压安装hadoop,配置HADOOP_HOME/conf下的文件，确保在命令行可以正常使用 hadoop 命令  
解压安装hive，配置HIVE_HOME/conf下的文件，确保在命令行可以正常使用 hive 命令，并且hive产生的元数据存放在mysql的hive数据库中  
导出环境变量 HADOOP_HOM HIVE_HOME  
5.打包  
```shell
mvn clean:clean
mvn package -Dmaven.test.skip=true
cd deploy
mvn assembly:assembly
```
(maven中央仓库有时无法下载，可以尝试使用镜像：http://mirrors.ibiblio.org/maven2)   
产出的war包在/deploy/target/zeus.war处 
使用tomcat之类容器运行即可  


以上步骤可以保证这个web项目正常启动，如果需要正式上线此项目，还需要配置以下内容：  
1.动态模板配置  
宙斯系统中有很多模板是可以动态修改的，包括以下一些，建议在正式运行之前都配置好  
(1)首页展示内容 启动后参见页面指南  
(2)首页通知内容 启动后参见页面指南  
(3)hive 默认udf函数 com.taobao.zeus.jobs.sub.HiveJob实现TODO内容  

2.登陆系统  
宙斯不包含单独的注册系统  
建议使用单点登陆来实现登陆  
大致原理：   
(1) web.xml添加一个filter，用来跳转到单点登陆系统  
(2) Spring容器中添加一个Bean，实现com.taobao.zeus.web.Login.Filter.SSOLogin接口  

3.超级管理员配置  
在com.taobao.zeus.store.Super中进行配置

4.关于浏览器兼容性  
默认只支持webkit内核的浏览器，建议使用chrome  
可以扩大浏览器范围，方法：/web/src/main/java/com/taobao/zeus/web/platform/Platform.gwt.xml 中注释掉 user.agent 这一行  
当然这样会大致打包时间加长(gwt为了兼容不同的浏览器会编译更多的代码，导致打包变慢)  
即便如此，我们也不保证IE等浏览器能够正常使用！  
