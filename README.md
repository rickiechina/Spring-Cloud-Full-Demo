# Spring Cloud微服务项目完整示例

目的是为了演示基于Spring Cloud微服务框架，搭建一个接近业务实际场景的项目，只实现了后端项目，前端可以使用VUE或者其他框架。后端框架包括注册中心 Eureka、配置中心Spring Cloud Config、API网关Zuul、客户端负载均衡Ribbon、断路器Hystrix，同时还包括2个后端业务服务，一个用户服务sc-user-service，另一个是数据服务 sc-data-service。

## 技术架构图

用户从浏览器发起请求，经过浏览器，请求到达Nginx，打开前端页面，由前端页面发起请求后端服务，当请求到达后端Nginx后，Nginx对网关层进行负载，因为网关也需要做HA。此时网关Gateway 接收到请求后，根据请求路径进行动态路由，根据服务发现是User Service 中的服务，则从Ribbon 中选择一台 User Service的实例进行调用，由User Service 返回数据，如果此时User Service 需要使用Data Service的数据，则选择一台Data Service的实例进行调用，然后数据给前端，流程结束。

![https://raw.githubusercontent.com/rickiechina/Spring-Cloud-Full-Demo/master/images/architecture.png]()

## 具体实现

项目结构如下所示，从module名称，即可理解每个项目的作用了。

其中common是一个公共基础包，方便给后台服务引用。

![https://raw.githubusercontent.com/rickiechina/Spring-Cloud-Full-Demo/master/images/source-code.png]()

## 运行微服务应用

按顺序启动 eureka、zuul-server、data-service、user-service，另外的hystrix-dashboard和config-server 是可行的module。如下图所示，可以看到每个应用的运行端口号。

![https://raw.githubusercontent.com/rickiechina/Spring-Cloud-Full-Demo/master/images/application-run.png]()

访问 eureka UI，查看服务注册情况。

<http://localhost:8761/>

每个服务都正常注册到 eureka 注册中心了。

![https://raw.githubusercontent.com/rickiechina/Spring-Cloud-Full-Demo/master/images/eureka.png]()

通过网关，访问如下URL，获取context user 信息：

http://localhost:7777/sc-user-service/getContextUserId

返回如下结果：{"exceptionType":"[cn.springcloud.book.common.exception.BaseException","code":10001,"businessId":1,"businessMessage":"the user is null, please check","codeEN":"AuthEmptyError"}

这是鉴权 filter 返回的异常信息，因为没有在请求头中获取到x-customs-user信息。

![https://raw.githubusercontent.com/rickiechina/Spring-Cloud-Full-Demo/master/images/auth-filter.png]()

通过Postman，在请求头中添加 x-customs-user=rickie 信息，然后再次访问URL。

<http://localhost:7777/sc-user-service/getContextUserId>
如下图所示，目前返回结果正常。

![https://raw.githubusercontent.com/rickiechina/Spring-Cloud-Full-Demo/master/images/postman.png]()