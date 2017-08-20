title: SpringMVC表单验证与Velocity整合
date: 2015-12-28 08:26:18
tags:
- java
- spring
- velocity
- web
categories:
- web
---
网络上关于SpringMvc和Velocity整合的内容较少，学习探索同时，做一下整理
# 定义表单类
以Login为例，有username和password两个字段
<!-- more -->
```
import javax.validation.constraints.NotNull;
import javax.validation.constraints.Size;


public class LoginForm {
	@Size(min=2,max=30,message="长度在2和30之间")
	@NotNull
	private String username;
	@NotNull(message="不能为空")
	private String password;
	
	public LoginForm(){}
	public LoginForm(String username,String password){
		this.username = username;
		this.password = password;
	}
	
	public String getUsername() {
		return username;
	}
	public void setUsername(String username) {
		this.username = username;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
}
```
属性上的注解，属于JSR-303规范，是JAVA EE 6中的一项子规范，称作Bean Validation.
@NotNull代表字段不能为空，@Size定义字段字符长度，message定义返回的错误信息，不定义的话，返回默认错误信息。
另外，还有很多
1.@NotNull/@Null
验证字段： 引用数据类型
注解说明：注解元素必须是非空或空
2.@Digits
验证字段：byte、short、int、long及各自的包装类型以及BigDecimal、BigInteger、String
注解说明：验证数字构成是否合法
属性说明：integer:指定整数部分数字位数，fraction:指定小数部分数字位数
3.@Future/Past
验证字段：java.util.Date,java.util.Calendar
注解说明：验证是否在当前系统时间之后/之前
4.@Max/@Min
验证字段：byte、short、int、long及对应的包装类型以及BigDecimal、BigInteger
注解说明：验证值是否小于等于最大指定整数值或大于等于最小指定整数值
5.@Pattern
验证字段：String
注解说明：验证字符串是否匹配指定的正则表达式
属性说明：regexp:匹配的正则表达式，flags:指定Pattern.Flag的数值，表示正则表达式的选项
6.@Size
验证字段：String、Collection、Map和数组
注解说明：验证元素大小是否在指定范围内
属性说明：max:最大长度，min:最小长度，message:提示信息,默认：{constraint.size}
7.@DecimalMax/@DecimalMin
验证字段：byte、short、int、long及对应的包装类型以及BigDecimal、BigInteger、String
属性说明：验证值是否小于等于最大指定小数值或大于等于最小指定小数值
8.@Valid
属性说明：验证值是否需要递归调用
Hibernate Validator 附加的 constraint
9.@Email 被注释的元素必须是电子邮箱地址
10.@Length 被注释的字符串的大小必须在指定的范围内
11.@NotEmpty 被注释的字符串的必须非空
12.@Range 被注释的元素必须在合适的范围内
# 定义Controller类
```
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.servlet.ModelAndView;

import crazy.form.LoginForm;

@RestController
@RequestMapping(value="/login")
public class LoginAction {

	@RequestMapping(method=RequestMethod.GET)
	public Model showLogin(Model model,LoginForm loginForm) {	
		return model;
	}

	@RequestMapping(method=RequestMethod.POST)
	public ModelAndView doLogin(@Valid LoginForm loginForm,BindingResult bindlingResult,ModelAndView model) {	

		if(bindlingResult.hasErrors()){
			model.setViewName("login");
			return model;
		}
		model.setViewName("success");
		return model;
	}
}

```
在表单类前加@Valid，绑定数据并验证，加入参数BindingResult，校验的结果就在这个对象中
doLogin执行后，表单信息和校验的错误信息都隐式的被框架放入响应中，这时可以从页面中拿到这些信息了
# 验证扩展
对于一般的验证，注解的方式可以优雅的完成。但有一些稍微复杂的验证，例如密码和验证密码是否一致，用户名是否存在这样的需求，仅仅靠注解就不够了。
如果希望自己在代码里实现这个逻辑，但错误的显示依靠原有框架，可以使用BindingResult的rejectValue方法。
代码示例：
```
@RequestMapping(method = RequestMethod.POST)
public ModelAndView doRegister(@Valid RegisterForm registerForm, BindingResult bindlingResult, ModelAndView model, HttpSession httpSession) {

    if (!registerForm.getPassword().equals(registerForm.getRePassword())) {
        bindlingResult.rejectValue("rePassword", "两次密码不一致", "两次密码不一致");
        model.setViewName("register");
        return model;
    }

    if (bindlingResult.hasErrors()) {
        log.info("表单错误");
        model.setViewName("register");
        return model;
    }

    return model;
}
```
# velocity表单页面
spring 给velocity提供了一些宏，可以显示表单，这里只用来绑定表单数据和错误信息.
```
<form method="POST" name="loginFrom">

  #springBind("registerForm.name")
  <input type="text" name="${status.expression}" id="name" class="form-control" placeholder="姓名" value="$!status.value"  autofocus>
    $status.errorMessage
  #springBind("registerForm.password")
  <input type="password" name="${status.expression}" id="inputPassword" class="form-control" value="$!status.value" placeholder="密码" >
  	$status.errorMessage
  <button type="submit">Sign in</button>
</form>
```
status.errorMessage这个字段，status.errorMessage不显示，而status.errorMessage不显示，而status.errorMessage显示
