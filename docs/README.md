## 环境部署
项目使用的是若依框架，官方文档(http://doc.ruoyi.vip/ruoyi/)
## 准备工作
**JDK >= 1.8 (推荐1.8版本)**  
**Mysql >= 5.7.0 (推荐5.7版本)**  
**Maven >= 3.0**

## 安装教程

1、前往Gitee下载页面(https://e.gitee.com/shenzhen-hetuo-innovation/repos/shenzhen-hetuo-innovation/rear-end-code-of-car-net-pile/sources) 下载解压到工作目录  
2、导入到Eclipse，菜单 File -> Import，然后选择 Maven -> Existing Maven  Projects，点击 Next> 按钮，选择工作目录，然后点击 Finish 按钮，即可成功导入。  
Eclipse会自动加载Maven依赖包，初次加载会比较慢（根据自身网络情况而定）  
3、创建数据库ry并导入数据脚本ry_2021xxxx.sql，quartz.sql  
4、打开项目运行com.ruoyi.RuoYiApplication.java，出现如下图表示启动成功。   
5、打开浏览器，输入：(http://localhost:8088  （默认账户/密码 admin/admin888）
若能正确展示登录页面，并能成功登录，菜单及页面展示正常，则表明环境搭建成功。

## 必要配置

* 修改数据库连接，编辑resources目录下的application-druid.yml  
***spring:**  
&emsp;**datasource:**  
&emsp;&emsp;**type: com.alibaba.druid.pool.DruidDataSource**  
&emsp;&emsp;**driverClassName: com.mysql.cj.jdbc.Driver**  
&emsp;&emsp;**druid:**  
&emsp;&emsp;&emsp;&emsp;# 主库数据源  
&emsp;&emsp;&emsp;&emsp;**master:**  
&emsp;&emsp;&emsp;&emsp;&emsp;**url: 数据库地址**  
&emsp;&emsp;&emsp;&emsp;&emsp;**username: 数据库账号**  
&emsp;&emsp;&emsp;&emsp;&emsp;**password: 数据库密码**  
* 修改服务器配置，编辑resources目录下的application.yml  
***server:**  
&emsp;**# 服务器的HTTP端口，默认为8088**  
&emsp;**port: 端口**  
&emsp;**servlet:**  
&emsp;&emsp;# 应用的访问路径  
&emsp;&emsp;**context-path: /应用路径** 

