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
3、创建数据库hotway-station并导入数据脚本  
4、打开项目运行com.ruoyi.RuoYiApplication.java，出现如下图表示启动成功。 
![](/_media/1.png)
5、打开浏览器，输入：(http://localhost:8088  （默认账户/密码 admin/admin888）
![](/_media/4.png)
若能正确展示登录页面，并能成功登录，菜单及页面展示正常，则表明环境搭建成功。
![](/_media/5.png)
## 必要配置

* 修改数据库连接，编辑resources目录下的application-druid.yml  
![](/_media/2.png) 
* 修改服务器配置，编辑resources目录下的application.yml  
![](/_media/3.png) 

