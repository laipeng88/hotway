## 分页实现
* 前端采用基于bootstrap的轻量级表格插件bootstrap-table
* 后端采用基于mybatis的轻量级分页插件pageHelper

## 前端调用实现
~~~
var options = {
	url: prefix + "/list",
	columns: [{
		field: 'id',
		title: '主键'
	},
	{
		field: 'name',
		title: '名称'
	}]
};
$.table.init(options);
~~~
* 自定义查询条件参数（特殊情况提前设置查询条件下使用）
~~~
var options = {
	url: prefix + "/list",
	queryParams: queryParams,
	columns: [{
		field: 'id',
		title: '主键'
	},
	{
		field: 'name',
		title: '名称'
	}]
};
$.table.init(options);
function queryParams(params) {
	var search = $.table.queryParams(params);
	search.userName = $("#userName").val();
	return search;
}
~~~

## 后台逻辑实现
~~~
@PostMapping("/list")
@ResponseBody
public TableDataInfo list(User user)
{
    startPage();  // 此方法配合前端完成自动分页
    List<User> list = userService.selectUserList(user);
    return getDataTable(list);
}
~~~

## 导出实现流程
1、前端调用封装好的方法$.table.init，传入后台exportUrl
~~~
var options = {
	exportUrl: prefix + "/export",
	columns: [{
		field: 'id',
		title: '主键'
	},
	{
		field: 'name',
		title: '名称'
	}]
};
$.table.init(options);
~~~
2、添加导出按钮事件
~~~
<a class="btn btn-warning" onclick="$.table.exportExcel()">
	<i class="fa fa-download"></i> 导出
</a>
~~~
3、在实体变量上添加@Excel注解
~~~
@Excel(name = "用户序号", prompt = "用户编号")
private Long userId;

@Excel(name = "用户名称")
private String userName;
	
@Excel(name = "用户性别", readConverterExp = "0=男,1=女,2=未知")
private String sex;

@Excel(name = "最后登陆时间", width = 30, dateFormat = "yyyy-MM-dd HH:mm:ss")
private Date loginDate;
~~~
4、在Controller添加导出方法
~~~
@PostMapping("/export")
@ResponseBody
public AjaxResult export(User user)
{
	List<User> list = userService.selectUserList(user);
	ExcelUtil<User> util = new ExcelUtil<User>(User.class);
	return util.exportExcel(list, "用户数据");
}
~~~

## 导入实现流程
1、前端调用封装好的方法$.table.init，传入后台importUrl。
~~~
var options = {
	importUrl: prefix + "/importData",
	columns: [{
		field: 'id',
		title: '主键'
	},
	{
		field: 'name',
		title: '名称'
	}]
};
$.table.init(options);
~~~
2、添加导入按钮事件
~~~
<a class="btn btn-info" onclick="$.table.importExcel()">
	<i class="fa fa-upload"></i> 导入
</a>
~~~
3、添加导入前端代码，form默认id为importForm，也可指定importExcel(id)
~~~
<!-- 导入区域 -->
<script id="importTpl" type="text/template">
<form enctype="multipart/form-data" class="mt20 mb10">
	<div class="col-xs-offset-1">
		<input type="file" id="file" name="file"/>
		<div class="mt10 pt5">
			<input type="checkbox" id="updateSupport" name="updateSupport" title="如果登录账户已经存在，更新这条数据。"> 是否更新已经存在的用户数据
			 &nbsp;	<a onclick="$.table.importTemplate()" class="btn btn-default btn-xs"><i class="fa fa-file-excel-o"></i> 下载模板</a>
		</div>
		<font color="red" class="pull-left mt10">
			提示：仅允许导入“xls”或“xlsx”格式文件！
		</font>
	</div>
</form>
</script>
~~~
4、在实体变量上添加@Excel注解，默认为导出导入，也可以单独设置仅导入Type.IMPORT
~~~
@Excel(name = "用户序号")
private Long id;

@Excel(name = "部门编号", type = Type.IMPORT)
private Long deptId;

@Excel(name = "用户名称")
private String userName;

/** 导出部门多个对象 */
@Excels({
	@Excel(name = "部门名称", targetAttr = "deptName", type = Type.EXPORT),
	@Excel(name = "部门负责人", targetAttr = "leader", type = Type.EXPORT)
})
private SysDept dept;

/** 导出部门单个对象 */
@Excel(name = "部门名称", targetAttr = "deptName", type = Type.EXPORT)
private SysDept dept;
~~~
5、在Controller添加导入方法，updateSupport属性为是否存在则覆盖（可选）
~~~
@PostMapping("/importData")
@ResponseBody
public AjaxResult importData(MultipartFile file, boolean updateSupport) throws Exception
{
	ExcelUtil<SysUser> util = new ExcelUtil<SysUser>(SysUser.class);
	List<SysUser> userList = util.importExcel(file.getInputStream());
	String operName = ShiroUtils.getSysUser().getLoginName();
	String message = userService.importUser(userList, updateSupport, operName);
	return AjaxResult.success(message);
}
~~~

## 权限注解
##### Shiro注解权限控制

* RequiresAuthentication使用该注解标注的类，实例，方法在访问或调用时，当前Subject必须在当前session中已经过认证
* RequiresGuest使用该注解标注的类，实例，方法在访问或调用时，当前Subject可以是gust身份，不需要经过认证或者在原先的session中存在记录。
* 当前Subject需要拥有某些特定的权限时，才能执行被该注解标注的方法。如果当前Subject不具有这样的权限，则方法不会被执行。
* 当前Subject必须拥有所有指定的角色时，才能访问被该注解标注的方法。如果当天Subject不同时拥有所有指定角色，则方法不会执行还会抛出AuthorizationException异常。
* 当前Subject必须是应用的用户，才能访问或调用被该注解标注的类，实例，方法。
1. RequiresRoles可以用在Controller或者方法上。可以多个roles，多个roles时默认逻辑为AND也就是所有具备所有role才能访问。
~~~
// 属于user角色
@RequiresRoles("user")
// 必须同时属于user和admin角色
@RequiresRoles({"user", "admin"})
// 属于user或者admin之一;修改logical为OR 即可
@RequiresRoles(value={"user", "admin"}, logical=Logical.OR)
~~~
2. RequiresPermissions与RequiresRoles类似
~~~
// 符合system:user:view权限要求
@RequiresPermissions("system:user:view")
// 必须同时复核system:user:view和system:user:list权限要求
@RequiresPermissions({"system:user:view", "system:user:list"}) 
// 符合system:user:view或system:user:list权限要求即可
@RequiresPermissions(value={"system:user:view", "system:user:list"}, logical=Logical.OR)
~~~
3. RequiresAuthentication，RequiresUser，RequiresGuest这三个的使用方法一样
~~~
@RequiresAuthentication
@RequiresUser
@RequiresGusst
~~~

## 异常处理
通常一个web框架中，有大量需要处理的异常。比如业务异常，权限不足等等。前端通过弹出提示信息的方式告诉用户出了什么错误。 通常情况下我们用try.....catch....对异常进行捕捉处理，但是在实际项目中对业务模块进行异常捕捉，会造成代码重复和繁杂， 我们希望代码中只有业务相关的操作，所有的异常我们单独设立一个类来处理它。全局异常就是对框架所有异常进行统一管理。 我们在可能发生异常的方法里throw抛给控制器。然后由全局异常处理器对异常进行统一处理。 如此，我们的Controller中的方法就可以很简洁了。

所谓全局异常处理器就是使用@ControllerAdvice注解。示例如下：
1、统一返回实体定义
~~~
package com.ruoyi.common.core.domain;

import java.util.HashMap;

/**
 * 操作消息提醒
 * 
 * @author ruoyi
 */
public class AjaxResult extends HashMap<String, Object>
{
    private static final long serialVersionUID = 1L;
    /**
     * 返回错误消息
     * 
     * @param code 错误码
     * @param msg 内容
     * @return 错误消息
     */
    public static AjaxResult error(String msg)
    {
        AjaxResult json = new AjaxResult();
        json.put("msg", msg);
        json.put("code", 500);
        return json;
    }
    /**
     * 返回成功消息
     * 
     * @param msg 内容
     * @return 成功消息
     */
    public static AjaxResult success(String msg)
    {
        AjaxResult json = new AjaxResult();
        json.put("msg", msg);
        json.put("code", 0);
        return json;
    }
}
~~~
2、定义登录异常定义
~~~
package com.ruoyi.common.exception;

/**
 * 登录异常
 * 
 * @author ruoyi
 */
public class LoginException extends RuntimeException
{
    private static final long serialVersionUID = 1L;

    protected final String message;

    public LoginException(String message)
    {
        this.message = message;
    }

    @Override
    public String getMessage()
    {
        return message;
    }
}
~~~
3、基于@ControllerAdvice注解的Controller层的全局异常统一处理
~~~
package com.ruoyi.framework.web.exception;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import com.ruoyi.common.core.domain.AjaxResult;
import com.ruoyi.common.exception.LoginException;

/**
 * 全局异常处理器
 * 
 * @author ruoyi
 */
@RestControllerAdvice
public class GlobalExceptionHandler
{
    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);
	
	/**
     * 登录异常
     */
    @ExceptionHandler(LoginException.class)
    public AjaxResult loginException(LoginException e)
    {
        log.error(e.getMessage(), e);
        return AjaxResult.error(e.getMessage());
    }
}
~~~
4、测试访问请求
~~~
@Controller
public class SysIndexController 
{
    /**
     * 首页方法
     */
    @GetMapping("/index")
    public String index(ModelMap mmap)
    {
        /**
         * 模拟用户未登录，抛出业务逻辑异常
         */
        SysUser user = ShiroUtils.getSysUser();
        if (StringUtils.isNull(user))
		{
            throw new LoginException("用户未登录，无法访问请求。");
        }
		mmap.put("user", user);
        return "index";
    }
}
~~~
根据上面代码含义，当我们未登录访问/index时就会发生LoginException业务逻辑异常，按照我们之前的全局异常配置以及统一返回实体实例化，访问后会出现AjaxResult格式JSON数据， 下面我们运行项目访问查看效果。
界面输出内容如下所示：
~~~
{
    "msg": "用户未登录，无法访问请求。",
    "code": 500
}
~~~
对于一些特殊情况，如接口需要返回json，页面请求返回html可以使用如下方法：
~~~
@ExceptionHandler(LoginException.class)
public Object loginException(HttpServletRequest request, LoginException e)
{
	log.error(e.getMessage(), e);

	if (ServletUtils.isAjaxRequest(request))
	{
		return AjaxResult.error(e.getMessage());
	}
	else
	{
		return new ModelAndView("/error/500");
	}
}
~~~
若依系统的全局异常处理器GlobalExceptionHandler
注意：如果全部异常处理返回json，那么可以使用@RestControllerAdvice代替@ControllerAdvice，这样在方法上就可以不需要添加@ResponseBody。

## 代码生成
大部分项目里其实有很多代码都是重复的，几乎每个基础模块的代码都有增删改查的功能，而这些功能都是大同小异， 如果这些功能都要自己去写，将会大大浪费我们的精力降低效率。所以这种重复性的代码可以使用代码生成。
1、修改代码生成配置
* 单应用编辑resources目录下的application.yml
* 多模块编辑ruoyi-generator中的resources目录下的generator.yml
* author: # 开发者姓名，生成到类注释上
* packageName: # 默认生成包路径
* autoRemovePre: # 是否自动去除表前缀
* tablePrefix: # 表前缀

2、新建数据库表结构（单表）
~~~
drop table if exists sys_student;
create table sys_student (
  student_id           int(11)         auto_increment    comment '编号',
  student_name         varchar(30)     default ''        comment '学生名称',
  student_age          int(3)          default null      comment '年龄',
  student_hobby        varchar(30)     default ''        comment '爱好（0代码 1音乐 2电影）',
  student_sex          char(1)         default '0'       comment '性别（0男 1女 2未知）',
  student_status       char(1)         default '0'       comment '状态（0正常 1停用）',
  student_birthday     datetime                          comment '生日',
  primary key (student_id)
) engine=innodb auto_increment=1 comment = '学生信息表';
~~~

2、新建数据库表结构（树表）
~~~
drop table if exists sys_product;
create table sys_product (
  product_id        bigint(20)      not null auto_increment    comment '产品id',
  parent_id         bigint(20)      default 0                  comment '父产品id',
  product_name      varchar(30)     default ''                 comment '产品名称',
  order_num         int(4)          default 0                  comment '显示顺序',
  status            char(1)         default '0'                comment '产品状态（0正常 1停用）',
  primary key (product_id)
) engine=innodb auto_increment=1 comment = '产品表';
~~~

2、新建数据库表结构（主子表）
~~~
-- ----------------------------
-- 客户表
-- ----------------------------
drop table if exists sys_customer;
create table sys_customer (
  customer_id           bigint(20)      not null auto_increment    comment '客户id',
  customer_name         varchar(30)     default ''                 comment '客户姓名',
  phonenumber           varchar(11)     default ''                 comment '手机号码',
  sex                   varchar(20)     default null               comment '客户性别',
  birthday              datetime                                   comment '客户生日',
  remark                varchar(500)    default null               comment '客户描述',
  primary key (customer_id)
) engine=innodb auto_increment=1 comment = '客户表';
-- ----------------------------
-- 商品表
-- ----------------------------
drop table if exists sys_goods;
create table sys_goods (
  goods_id           bigint(20)      not null auto_increment    comment '商品id',
  customer_id        bigint(20)      not null                   comment '客户id',
  name               varchar(30)     default ''                 comment '商品名称',
  weight             int(5)          default null               comment '商品重量',
  price              decimal(6,2)    default null               comment '商品价格',
  date               datetime                                   comment '商品时间',
  type               char(1)         default null               comment '商品种类',
  primary key (goods_id)
) engine=innodb auto_increment=1 comment = '商品表';
~~~
3、登录系统（系统工具 -> 代码生成 -> 导入对应表）

4、代码生成列表中找到需要表（可预览、修改、删除生成配置）

5、点击生成代码会得到一个ruoyi.zip执行sql文件，按照包内目录结构复制到自己的项目中即可多模块所有代码生成的相关业务逻辑代码在ruoyi-generator模块，可以自行调整或剔除