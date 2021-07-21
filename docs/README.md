## 项目网站

* **[电动汽车充电站全寿命周期服务系统](http://39.108.136.17?_blank)**

## 项目框架

项目使用的是若依框架，官方文档(http://doc.ruoyi.vip/ruoyi/)

## 技术选型

##### 1、系统环境

* Java EE 8
* Servlet 3.0
* Apache Maven 3

##### 2、主框架
* Spring Boot 2.2.x
* Spring Framework 5.2.x
* Apache Shiro 1.7

##### 3、持久层
* Apache MyBatis 3.5.x
* Hibernate Validation 6.0.x
* Alibaba Druid 1.2.x

##### 4、视图层
* Bootstrap 3.3.7
* Thymeleaf 3.0.x

## 准备工作

**JDK >= 1.8 (推荐1.8版本)**  
**Mysql >= 5.7.0 (推荐5.7版本)**  
**Maven >= 3.0**

## 安装教程

* 前往[Gitee](https://e.gitee.com/shenzhen-hetuo-innovation/repos/shenzhen-hetuo-innovation/power-station/sources)下载解压到工作目录  
* 导入到Eclipse，菜单 File -> Import，然后选择 Maven -> Existing Maven  Projects，点击 Next> 按钮，选择工作目录，然后点击 Finish 按钮，即可成功导入,Eclipse会自动加载Maven依赖包，初次加载会比较慢（根据自身网络情况而定）  
* 创建数据库hotway-station并导入数据脚本  
![](_media/27.png)
* 修改数据库连接，编辑resources目录下的application-druid.yml  
![](_media/2.png) 
* 打开项目运行com.ruoyi.RuoYiApplication.java，出现如下图表示启动成功。 
![](_media/1.png)  
* 打开浏览器，输入：localhost（默认账户/密码 admin/admin888）
![](_media/4.png)  
* 若能正确展示登录页面，并能成功登录，菜单及页面展示正常，则表明环境搭建成功。
![](_media/5.png)

## 文件结构
~~~
com.ruoyi     
├── common            // 工具类
│       └── annotation                    // 自定义注解
│       └── config                        // 全局配置
│       └── constant                      // 通用常量
│       └── core                          // 核心控制
│       └── enums                         // 通用枚举
│       └── exception                     // 通用异常
│       └── json                          // JSON数据处理
│       └── utils                         // 通用类处理
│       └── xss                           // XSS过滤处理
├── framework         // 框架核心
│       └── aspectj                       // 注解实现
│       └── config                        // 系统配置
│       └── datasource                    // 数据权限
│       └── interceptor                   // 拦截器
│       └── manager                       // 异步处理
│       └── shiro                         // 权限控制
│       └── web                           // 前端控制
├── ruoyi-generator   // 代码生成（不用可移除）
├── ruoyi-quartz      // 定时任务（不用可移除）
├── ruoyi-system      // 系统代码
├── ruoyi-admin       // 后台服务
├── ruoyi-cdz         // 电站模块
├── sql               // 数据脚本
~~~  

## 配置文件

* 通用配置 application.yml
~~~
# 项目相关配置
ruoyi:
  # 名称
  name: RuoYi
  # 版本
  version: 4.6.0
  # 版权年份
  copyrightYear: 2021
  # 实例演示开关
  demoEnabled: true
  # 文件路径 示例（ Windows配置D:/ruoyi/uploadPath，Linux配置 /home/ruoyi/uploadPath）
  profile: D:/ruoyi/uploadPath
  # 获取ip地址开关
  addressEnabled: false
# 开发环境配置
server:
  # 服务器的HTTP端口，默认为80
  port: 80
  servlet:
    # 应用的访问路径
    context-path: /
  tomcat:
    # tomcat的URI编码
    uri-encoding: UTF-8
    # tomcat最大线程数，默认为200
    max-threads: 800
    # Tomcat启动初始化的线程数，默认值25
    min-spare-threads: 30
# 日志配置
logging:
  level:
    com.ruoyi: debug
    org.springframework: warn
# 用户配置
user:
  password:
    # 密码错误{maxRetryCount}次锁定10分钟
    maxRetryCount: 5
# Spring配置
spring:
  # 模板引擎
  thymeleaf:
    mode: HTML
    encoding: utf-8
    # 禁用缓存
    cache: false
  # 资源信息
  messages:
    # 国际化资源文件路径
    basename: static/i18n/messages
  jackson:
    time-zone: GMT+8
    date-format: yyyy-MM-dd HH:mm:ss
  profiles: 
    active: druid
  # 文件上传
  servlet:
     multipart:
       # 单个文件大小
       max-file-size:  10MB
       # 设置总上传的文件大小
       max-request-size:  20MB
  # 服务模块
  devtools:
    restart:
      # 热部署开关
      enabled: true
# MyBatis
mybatis:
    # 搜索指定包别名
    typeAliasesPackage: com.ruoyi.**.domain
    # 配置mapper的扫描，找到所有的mapper.xml映射文件
    mapperLocations: classpath*:mapper/**/*Mapper.xml
    # 加载全局的配置文件
    configLocation: classpath:mybatis/mybatis-config.xml
# PageHelper分页插件
pagehelper: 
  helperDialect: mysql
  reasonable: true
  supportMethodsArguments: true
  params: count=countSql 
# Shiro
shiro:
  user:
    # 登录地址
    loginUrl: /login
    # 权限认证失败地址
    unauthorizedUrl: /unauth
    # 首页地址
    indexUrl: /index
    # 验证码开关
    captchaEnabled: true
    # 验证码类型 math 数组计算 char 字符
    captchaType: math
  cookie:
    # 设置Cookie的域名 默认空，即当前访问的域名
    domain: 
    # 设置cookie的有效访问路径
    path: /
    # 设置HttpOnly属性
    httpOnly: true
    # 设置Cookie的过期时间，天为单位
    maxAge: 30
    # 设置密钥，务必保持唯一性（生成方式，直接拷贝到main运行即可）KeyGenerator keygen = KeyGenerator.getInstance("AES"); SecretKey deskey = keygen.generateKey(); System.out.println(Base64.encodeToString(deskey.getEncoded()));
    cipherKey: zSyK5Kp6PZAAjlT+eeNMlg==
  session:
    # Session超时时间，-1代表永不过期（默认30分钟）
    expireTime: 30
    # 同步session到数据库的周期（默认1分钟）
    dbSyncPeriod: 1
    # 相隔多久检查一次session的有效性，默认就是10分钟
    validationInterval: 10
    # 同一个用户最大会话数，比如2的意思是同一个账号允许最多同时两个人登录（默认-1不限制）
    maxSession: -1
    # 踢出之前登录的/之后登录的用户，默认踢出之前登录的用户
    kickoutAfter: false
# 防止XSS攻击
xss: 
  # 过滤开关
  enabled: true
  # 排除链接（多个用逗号分隔）
  excludes: /system/notice/*
  # 匹配链接
  urlPatterns: /system/*,/monitor/*,/tool/*
# Swagger配置
swagger:
  # 是否开启swagger
  enabled: true
~~~
* 数据源配置 application-druid.yml
~~~
# 数据源配置
spring:
    datasource:
        type: com.alibaba.druid.pool.DruidDataSource
        driverClassName: com.mysql.cj.jdbc.Driver
        druid:
            # 主库数据源
            master:
                url: jdbc:mysql://localhost:3306/ry?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8
                username: root
                password: password
            # 从库数据源
            slave:
                # 从数据源开关/默认关闭
                enabled: false
                url: 
                username: 
                password: 
            # 初始连接数
            initialSize: 5
            # 最小连接池数量
            minIdle: 10
            # 最大连接池数量
            maxActive: 20
            # 配置获取连接等待超时的时间
            maxWait: 60000
            # 配置间隔多久才进行一次检测，检测需要关闭的空闲连接，单位是毫秒
            timeBetweenEvictionRunsMillis: 60000
            # 配置一个连接在池中最小生存的时间，单位是毫秒
            minEvictableIdleTimeMillis: 300000
            # 配置一个连接在池中最大生存的时间，单位是毫秒
            maxEvictableIdleTimeMillis: 900000
            # 配置检测连接是否有效
            validationQuery: SELECT 1 FROM DUAL
            testWhileIdle: true
            testOnBorrow: false
            testOnReturn: false
            webStatFilter: 
                enabled: true
            statViewServlet:
                enabled: true
                # 设置白名单，不填则允许所有访问
                allow:
                url-pattern: /druid/*
                # 控制台管理用户名和密码
                login-username: 
                login-password: 
            filter:
                stat:
                    enabled: true
                    # 慢SQL记录
                    log-slow-sql: true
                    slow-sql-millis: 1000
                    merge-sql: true
                wall:
                    config:
                        multi-statement-allow: true
~~~
* 代码生成配置 generator.yml
~~~
# 代码生成
gen: 
  # 作者
  author: ruoyi
  # 默认生成包路径 system 需改成自己的模块名称 如 system monitor tool
  packageName: com.ruoyi.system
  # 自动去除表前缀，默认是false
  autoRemovePre: false
  # 表前缀（生成类名不会包含表前缀，多个用逗号分隔）
  tablePrefix: sys_
~~~

## 核心技术

#### SpringBoot框架

1、介绍  
Spring Boot是一款开箱即用框架，提供各种默认配置来简化项目配置。让我们的Spring应用变的更轻量化、更快的入门。 在主程序执行main函数就可以运行。你也可以打包你的应用为jar并通过使用java -jar来运行你的Web应用。它遵循"约定优先于配置"的原则， 使用SpringBoot只需很少的配置，大部分的时候直接使用默认的配置即可。同时可以与Spring Cloud的微服务无缝结合。

2、优点
* 使编码变得简单： 推荐使用注解。
* 使配置变得简单： 自动配置、快速集成新技术能力 没有冗余代码生成和XML配置的要求
* 使部署变得简单： 内嵌Tomcat、Jetty、Undertow等web容器，无需以war包形式部署
* 使监控变得简单： 提供运行时的应用监控
* 使集成变得简单： 对主流开发框架的无配置集成。
* 使开发变得简单： 极大地提高了开发快速构建项目、部署效率。

#### Shiro安全控制

1、介绍  
Apache Shiro是Java的一个安全框架。Shiro可以帮助我们完成：认证、授权、加密、会话管理、与Web集成、缓存等。其不仅可以用在 JavaSE环境，也可以用在 JavaEE 环境。  

2、优点  
* 易于理解的 Java Security API
* 简单的身份认证，支持多种数据源
* 对角色的简单的授权，支持细粒度的授权
* 不跟任何的框架或者容器捆绑，可以独立运行

3、特性  
* Authentication身份认证/登录，验证用户是不是拥有相应的身份
* Authorization授权，即验证权限，验证某个已认证的用户是否拥有某个权限，即判断用户是否能做事情 
* SessionManagement会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中
* Cryptography加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储
* Caching缓存，比如用户登录后，其用户信息，拥有的角色/权限不必每次去查，提高效率
* ConcurrencyShiro支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去
* Testing提供测试支持
* RunAs允许一个用户假装为另一个用户（如果他们允许）的身份进行访问
* RememberMe记住我，这是非常常见的功能，即一次登录后，下次再来的话不用登录了

4、  
* Subject主体，代表了当前的“用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是Subject，如网络爬虫， 机器人等；即一个抽象概念；所有Subject都绑定到SercurityManager，与Subject的所有交互都会委托给SecurityManager；可以把Subject认为是一个门面；SecurityManager才是实际的执行者
* SecurityManage安全管理器；即所有与安全有关的操作都会与SecurityManager交互；且它管理着所有Subject； 可以看出它是Shiro的核心，它负责与后边介绍的其他组件进行交互
* Realm域，Shiro从Realm获取安全数据（如用户，角色，权限），就是说SecurityManager要验证用户身份， 那么它需要从Realm获取相应的用户进行比较以确定用户身份是否合法；也需要从Realm得到用户相应的角色/权限进行验证用户是否能进行操作；可以有1个或多个Realm，我们一般在应用中都需要实现自己的Realm
* SessionManager如果写过Servlet就应该知道Session的概念，Session需要有人去管理它的生命周期，这个组件就是SessionManager
* SessionDAO DAO大家都用过，数据库访问对象，用于会话的CRUD，比如我们想把Session保存到数据库，那么可以实现自己的SessionDAO，也可以写入缓存，以提高性能
* CacheManager缓存控制器，来管理如用户，角色，权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能
* 应用代码通过Subject来进行认证和授权，而Subject又委托给SecurityManager； 我们需要给Shrio的SecurityManager注入Realm，从而让SecurityManager能得到合法的用户及其权限进行判断，Shiro不提供维护用户/权限，而是通过Realm让开发人员自己注入。

#### Thymeleaf模板

1、介绍  
Thymeleaf是一个用于Web和独立Java环境的模板引擎，能够处理HTML、XML、JavaScript、CSS甚至纯文本。能轻易的与Spring MVC等Web框架进行集成作为Web应用的模板引擎。 与其它模板引擎（比如FreeMaker）相比，Thymeleaf最大的特点是能够直接在浏览器中打开并正确显示模板页面，而不需要启动整个Web应用（更加方便前后端分离，比如方便类似VUE前端设计页面），抛弃JSP吧。 Thymeleaf 3.0是一个完全彻底重构的模板引擎，极大的减少内存占用和提升性能和并发性，避免v2.1版因大量的输出标记的集合产生的资源占用。 Thymeleaf 3.0放弃了大多数面向DOM的处理机制，变成了一个基于事件的模板处理器，它通过处理模板标记或文本并立即生成其输出，甚至在新事件之前响应模板解析器/缓存事件。Thymeleaf是Spring Boot官方的推荐使用模板。

2、优点
* 国际化支持非常简单
* 语法简单，功能强大。内置大量常用功能，使用非常方便
* 可以很好的和Spring集成
* 静态html嵌入标签属性，浏览器可以直接打开模板文件，便于前后端联调
* Spring Boot 官方推荐，用户群广