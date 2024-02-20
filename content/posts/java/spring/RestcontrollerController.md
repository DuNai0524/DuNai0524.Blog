---
title: "@RestController 与 @Controller"
date: 2024-01-12T21:30:08+08:00
tags: ['Java','Spring','八股']
categories: ['Java']
featuredImage: https://dunaifujian-1308176953.cos.ap-guangzhou.myqcloud.com/artilce_cover/114979249_p0_master1200.jpg
license: CC BY-NC-SA 4.0
---

# @RestController vs @Controller
## Controller 返回一个页面

单独使用 `@Controller` 不加 `@ResponseBody` 的话一般使用在要返回一个视图的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况

![image.png](https://dunaiobsidian-1308176953.cos.ap-guangzhou.myqcloud.com/obsidian20240112212014.png)

## @RestController 返回 JSON 或者 XML 形式数据

`@RestController` 只返回对象，对象数据直接以 JSON 或者 XML 形式写入 HTTP 响应(Response)中，这种情况输入 RESTful Web 服务，也就是前后端分离的情况

![image.png](https://dunaiobsidian-1308176953.cos.ap-guangzhou.myqcloud.com/obsidian20240112212221.png)

## @Controller + @ResponseBody 返回 JSON 或者 XML 形式数据

如果要在 Spring4 之前开发 RESTful Web 服务的话，需要使用 `@Controller` 并结合 `@ResponseBody` 注解，也就是说

`@Controller` + `@ResponseBody` = `@RestController` (Spring4之后新加的注解)

>`@ResponseBody` 注解的作用是将 `Controller` 的方法返回的对象通过适当的转换器转换为指定的格式之后，写入 HTTP 响应(Response)对象的 Body 中，通常用来返回 JSON 或者 XML 数据，返回 JSON 数据的情况比较多

![image.png](https://dunaiobsidian-1308176953.cos.ap-guangzhou.myqcloud.com/obsidian20240112212616.png)


# 示例
## 示例1: @Controller 返回一个页面

当我们需要直接在后端返回一个页面的时候，Spring 推荐使用 Thymeleaf 模板引擎。Spring MVC中`@Controller`中的方法可以直接返回模板名称，接下来 Thymeleaf 模板引擎会自动进行渲染,模板中的表达式支持Spring表达式语言（Spring EL)。**如果需要用到 Thymeleaf 模板引擎，注意添加依赖！不然会报错。**

Gradle:

```
 compile 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

Maven:

```XML
<dependency>  
	<groupId>org.springframework.boot</groupId>  
	<artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

`src/main/java/com/example/demo/controller/HelloController.java`

```Java
@Controllerpublic class HelloController {    
	@GetMapping("/hello")	public String greeting(@RequestParam(name = "name", required = false, defaultValue = "World") String name, Model model){        
	model.addAttribute("name", name);        
	return "hello";    
	}
}
```

`src/main/resources/templates/hello.html`

Spring 默认会去 resources 目录下 templates 目录下找，所以建议把页面放在 resources/templates 目录下

```HTML
<!DOCTYPE HTML>
<html xmlns:th="http://www.thymeleaf.org">
	<head>    
		<title>Getting Started: Serving Web Content</title>    
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
	</head>
	<body>
		<p th:text="'Hello, ' + ${name} + '!'"/>
	</body>
</html>
```

访问：http://localhost:8999/hello?name=team-c ，你将看到下面的内容

```
Hello, team-c!
```

如果要对页面在templates目录下的hello文件夹中的话，返回页面的时候像下面这样写就可以了。

`src/main/resources/templates/hello/hello.html`

```java
   return "hello/hello";
```

## 示例2: @Controller+@ResponseBody 返回 JSON 格式数据

**SpringBoot 默认集成了 jackson ,对于此需求你不需要添加任何相关依赖。**

`src/main/java/com/example/demo/controller/Person.java`

```Java
public class Person {    
	private String name;    
	private Integer age;    
	......    省略getter/setter ，有参和无参的construtor方法
}
```

`src/main/java/com/example/demo/controller/HelloController.java`

```
@Controllerpublic 
class HelloController {    
	@PostMapping("/hello")    
	@ResponseBody    
	public Person greeting(@RequestBody Person person) {        
		return person;    
	}
}
```

使用 post 请求访问 http://localhost:8080/hello ，body 中附带以下参数,后端会以json 格式将 person 对象返回。

```json
{   
    "name": "teamc",
	"age": 1
}
```

## 示例3: @RestController 返回 JSON 格式数据

只需要将`HelloController`改为如下形式：

```Java
@RestControllerpublic 
class HelloController {    
	@PostMapping("/hello")    
	public Person greeting(@RequestBody Person person) {        
		return person;    
	}
}
```

>参考文章：
>[Spring&SpringBoot常用注解总结 | JavaGuide(Java面试+学习指南)](https://javaguide.cn/system-design/framework/spring/spring-common-annotations.html#_2-spring-bean-%E7%9B%B8%E5%85%B3)
>[@RestController vs @Controller (qq.com)](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485544&idx=1&sn=3cc95b88979e28fe3bfe539eb421c6d8&chksm=cea247a3f9d5ceb5e324ff4b8697adc3e828ecf71a3468445e70221cce768d1e722085359907&token=1725092312&lang=zh_CN#rd)

