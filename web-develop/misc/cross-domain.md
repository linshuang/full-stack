# 跨域
跨域问题是由于浏览器为了防止CSRF攻击，避免恶意攻击而带来的风险而采取的同源策略限制。当一个页面中使用XMLHTTPRequest对象发送HTTP请求时（XHR请求），必须保证当前页面和请求的对象是同源的，即协议、域名和端口号要完全一致，否则浏览器就会阻止此跨域请求返回的数据。

## 同源策略<sup>[1]</sup>
同源策略是一种约定，它是浏览器最核心也最基本的安全功能。可以说Web是构建在同源策略的基础之上的。其目的是为了保护用户信息的安全。

浏览器的同源策略，限制了来自不同源的“document”或脚本，对当前“document”读取或设置某些属性。

试想如果没有同源策略，可能a.com的一段脚本，在b.com未曾加载此脚本时，也可以随意涂改b.com的页面（在浏览器的显示中）。因此，为了不让浏览器的页面行为发生混乱，浏览器提出了“Origin”(源)这一概念，让来自不同Origin的对象无法互相干扰。

影响“源”的因素有三个：host(域名或IP地址)、端口、协议。例如对于http://aaa.example.com/dir/page.html来说

| URL | 是否同源 | 理由 |
| ---- | ---- | ---- |
| http://aaa.example.com/dir/page2.html | 是 | |
| http://aaa.example.com/dir/dir2/page3.html | 是 | |
| https://aaa.example.com/dir/dir2/page3.html | 不是 | 协议不同 |
| http://aaa.example.com:81/dir/page2.html | 不是 | 端口不同 |
| http://bbb.example.com:81/dir/page2.html | 不是 | host不同 |

同源策略限制对下述资源的访问，当发现跨域访问如下资源时，浏览器会抛出跨域错误，并阻止访问：
- DOM
- Cookie
- localStorage
- IndexDB
- Ajax请求
- 第三方插件

### 反向代理跨域
反向代理是解决所有跨域问题的不二法宝，做法是在原本提供不同host、端口、协议的服务器前面架一个代理服务器，对这些服务器的访问，都经由代理服务器来转发，这些服务器对外暴露的协议、域名、端口都是此代理服务器的，因此，对浏览器来说，所有的资源都来自于同源，自然就不会有跨域的问题了。

### iframe跨域
iframe跨域的场景是指：页面加载了iframe，但是iframe和父页面不在同一个域，在同源情况下，iframe是可以与父页面互相调用全局函数、使用全局变量的；而跨域时，数据交换会被浏览器阻止，这个时候，需要借助一些手段绕过浏览器，实现iframe跨域的数据访问。

### JSONP跨域
浏览器只对XHR(XMLHttpRequest)请求有同源请求限制，而对script标签src属性、link标签ref属性和img标签src属性没有这这种限制，利用这个“漏洞”就可以很好的解决跨域请求。JSONP就是利用了script标签无同源限制的特点来实现的，当向第三方站点请求时，我们可以将此请求放在script标签的src属性里。这就如同我们请求一个普通的JS脚本，可以自由的向不同的站点请求：
```
<script src="http://www.b.com/request?para1=1"></script>
```
服务端返回JS脚本，脚本的内容就是对处理程序的回调，服务返回的数据通过参数的形式传回。

JQuery的ajax方法对JSONP方法进行了封装，使用JQuery提供的方法变得非常简单

## CORS(跨域资源共享)<sup>[2][3]</sup>
CORS是W3c工作草案，它定义了在跨域访问资源时浏览器和服务器之间如何通信。CORS背后的基本思想是使用自定义的HTTP头部允许浏览器和服务器相互了解对方，从而决定请求或响应成功与否。

浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。

### 简单请求
对于简单请求，浏览器直接发出CORS请求。具体来说，就是在头信息之中，增加一个Origin字段。
```
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
上面的头信息中，Origin字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口）。服务器根据这个值，决定是否同意这次请求。

如果Origin指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含Access-Control-Allow-Origin字段（详见下文），就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

如果Origin指定的域名在许可范围内，服务器返回的响应，会多出几个头信息字段。
```
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```
CORS请求默认不发送Cookie和HTTP认证信息。如果要把Cookie发到服务器，一方面要服务器同意，指定Access-Control-Allow-Credentials字段。

### 非简单请求
非简单请求是那种对服务器有特殊要求的请求，比如请求方法是PUT或DELETE，或者Content-Type字段的类型是application/json。

非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求（preflight）。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。

浏览器发现，这是一个非简单请求，就自动发出一个"预检"请求，要求服务器确认可以这样请求。下面是这个"预检"请求的HTTP头信息。
```
OPTIONS /cors HTTP/1.1
Origin: http://api.bob.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
预检"请求用的请求方法是OPTIONS，表示这个请求是用来询问的。头信息里面，关键字段是Origin，表示请求来自哪个源。

服务器收到"预检"请求以后，检查了Origin、Access-Control-Request-Method和Access-Control-Request-Headers字段以后，确认允许跨源请求，就可以做出回应。
```
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```
上面的HTTP回应中，关键的是Access-Control-Allow-Origin字段，表示http://api.bob.com可以请求数据。该字段也可以设为星号，表示同意任意跨源请求。

如果浏览器否定了"预检"请求，会返回一个正常的HTTP回应，但是没有任何CORS相关的头信息字段。这时，浏览器就会认定，服务器不同意预检请求，因此触发一个错误，被XMLHttpRequest对象的onerror回调函数捕获。

一旦服务器通过了"预检"请求，以后每次浏览器正常的CORS请求，就都跟简单请求一样，会有一个Origin头信息字段。服务器的回应，也都会有一个Access-Control-Allow-Origin头信息字段。

下面是"预检"请求之后，浏览器的正常CORS请求。
```
PUT /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
X-Custom-Header: value
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
上面头信息的Origin字段是浏览器自动添加的。
```
下面是服务器正常的回应。
```
Access-Control-Allow-Origin: http://api.bob.com
Content-Type: text/html; charset=utf-8
```
上面头信息中，Access-Control-Allow-Origin字段是每次回应都必定包含的。

### 开启CROS（java）
如果想要对某一接口配置 CORS，可以在方法上添加 CrossOrigin 注解
```
@CrossOrigin(origins = {"http://localhost:9000", "null"})
@RequestMapping(value = "/test", method = RequestMethod.GET)
public String greetings() {
    return "{\"project\":\"just a test\"}";
}
```
如果想对一系列接口添加 CORS 配置，可以在类上添加注解，对该类声明所有接口都有效：
```
CrossOrigin(origins = {"http://localhost:9000", "null"})
@RestController
@SpringBootApplication
public class SpringBootCorsTestApplication {
    // xxx
}
```
第三种情况，添加全局配置，则需要添加一个配置类：
```
@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {
 
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("http://localhost:9000", "null")
                .allowedMethods("POST", "GET", "PUT", "OPTIONS", "DELETE")
                .maxAge(3600)
                .allowCredentials(true);
    }
}
```
还可以通过添加 Filter 的方式，配置 CORS 规则，并手动指定对哪些接口有效。
```
@Bean
public FilterRegistrationBean corsFilter() {
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    CorsConfiguration config = new CorsConfiguration();
    config.setAllowCredentials(true);	config.addAllowedOrigin("http://localhost:9000");
    config.addAllowedOrigin("null");
    config.addAllowedHeader("*");
    config.addAllowedMethod("*");
    source.registerCorsConfiguration("/**", config); // CORS 配置对所有接口都有效
    FilterRegistrationBean bean = newFilterRegistrationBean(new CorsFilter(source));
    bean.setOrder(0);
    return bean;
}
```


## 参考
- [1] [跨域那些事儿](http://www.sohu.com/a/166245888_465944)
- [2] [CORS 跨域 实现思路及相关解决方案](https://www.cnblogs.com/sloong/p/cors.html)
- [3] [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
- [4] [Spring MVC配置CORS](https://blog.csdn.net/switch513/article/details/54612411)