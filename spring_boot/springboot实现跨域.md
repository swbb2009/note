# springboot实现跨域
**CORS实现跨域访问**
授权方式

方式1：返回新的CorsFilter
方式2：重写WebMvcConfigurer
方式3：使用注解（@CrossOrigin）
方式4：手工设置响应头（HttpServletResponse ）
注：CorsFilter / WebMvcConfigurer / @CrossOrigin 需要SpringMVC 4.2 以上的版本才支持，对应SpringBoot 1.3 版本以上都支持这些CORS特性。不过，使用SpringMVC4.2 以下版本的小伙伴也不用慌，直接使用方式4通过手工添加响应头来授权CORS跨域访问也是可以的。附：在SpringBoot 1.2.8 + SpringMVC 4.1.9 亲测成功。

**1. 返回新的CorsFilter（全局跨域）**

在任意配置类，返回一个新的CorsFilter Bean，并添加映射路径和具体的CORS配置信息。
```
package com.hehe.yyweb.config;
@Configuration
public class GlobalCorsConfig {
@Bean
public CorsFilter corsFilter() {
//1.添加CORS配置信息
CorsConfiguration config = new CorsConfiguration();
//放行哪些原始域
config.addAllowedOrigin("*");
//是否发送Cookie信息
config.setAllowCredentials(true);
//放行哪些原始域(请求方式)
config.addAllowedMethod("*");
//放行哪些原始域(头部信息)
config.addAllowedHeader("*");
//暴露哪些头部信息（因为跨域访问默认不能获取全部头部信息）
config.addExposedHeader("*");
//2.添加映射路径
UrlBasedCorsConfigurationSource configSource = new UrlBasedCorsConfigurationSource();
configSource.registerCorsConfiguration("/**", config);
//3.返回新的CorsFilter.
return new CorsFilter(configSource);
}
}
```
**2. 重写WebMvcConfigurer（全局跨域）**

在任意配置类，返回一个新的WebMvcConfigurer Bean，并重写其提供的跨域请求处理的接口，目的是添加映射路径和具体的CORS配置信息。
```
package com.hehe.yyweb.config;
@Configuration
public class GlobalCorsConfig {
@Bean
public WebMvcConfigurer corsConfigurer() {
return new WebMvcConfigurer() {
@Override
//重写父类提供的跨域请求处理的接口
public void addCorsMappings(CorsRegistry registry) {
//添加映射路径
registry.addMapping("/**")
//放行哪些原始域
.allowedOrigins("*")
//是否发送Cookie信息
.allowCredentials(true)
//放行哪些原始域(请求方式)
.allowedMethods("GET","POST", "PUT", "DELETE")
//放行哪些原始域(头部信息)
.allowedHeaders("*")
//暴露哪些头部信息（因为跨域访问默认不能获取全部头部信息）
.exposedHeaders("Header1", "Header2");
}
};
}
}
```
**3. 使用注解（局部跨域）**

在方法上（@RequestMapping）使用注解 @CrossOrigin ：
```
@RequestMapping("/hello")
@ResponseBody
@CrossOrigin("http://localhost:8080")
public String index( ){
return "Hello World";
}
```
或者在控制器（@Controller）上使用注解 @CrossOrigin ：
```
@Controller
@CrossOrigin(origins = "http://xx-domain.com", maxAge = 3600)
public class AccountController {
@RequestMapping("/hello")
@ResponseBody
public String index( ){
return "Hello World";
}
}
```
**4. 手工设置响应头（局部跨域 ）**

使用HttpServletResponse对象添加响应头（Access-Control-Allow-Origin）来授权原始域，这里Origin的值也可以设置为”*” ，表示全部放行。
```
@RequestMapping("/hello")
@ResponseBody
public String index(HttpServletResponse response){
response.addHeader("Access-Control-Allow-Origin", "http://localhost:8080");
return "Hello World";
}
```


三、测试跨域访问

首先使用 Spring Initializr 快速构建一个Maven工程，什么都不用改，在static目录下，添加一个页面：index.html 来模拟跨域访问。目标地址: http://localhost:8090/hello
```
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<title>Page Index</title>
</head>
<body>
<h2>前台系统</h2>
<p id="info"></p>
</body>
<script src="webjars/jquery/3.2.1/jquery.js"></script>
<script>
$.ajax({
url: 'http://localhost:8090/hello',
type: "POST",
success: function (data) {
$("#info").html("跨域访问成功:"+data);
},
error: function (data) {
$("#info").html("跨域失败!!");
}
})
</script>
</html>
```
然后创建另一个工程，在Root Package添加Config目录并创建配置类来开启全局CORS。
```
package com.hehe.yyweb.config;
@Configuration
public class GlobalCorsConfig {
@Bean
public WebMvcConfigurer corsConfigurer() {
return new WebMvcConfigurer() {
@Override
public void addCorsMappings(CorsRegistry registry) {
registry.addMapping("/**");
}
};
}
}
```
接着，简单编写一个Rest接口 ，并指定应用端口为8090。
```
package com.hehe.yyweb;
@SpringBootApplication
@RestController
public class YyWebApplication {
@Bean
public TomcatServletWebServerFactory tomcat() {
TomcatServletWebServerFactory tomcatFactory = new TomcatServletWebServerFactory();
tomcatFactory.setPort(8090); //默认启动8090端口
return tomcatFactory;
}
@RequestMapping("/hello")
public String index() {
return "Hello World";
}
public static void main(String[] args) {
SpringApplication.run(YyWebApplication.class, args);
}
}
```
最后分别启动两个应用，然后在浏览器访问：http://localhost:8080/index.html ，可以正常接收JSON数据，说明跨域访问成功！！



