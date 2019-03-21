# Spring Cloud 微服务架构学习

参考仓库: https://github.com/ityouknow/spring-cloud-examples  

> 作者为每个话题都搭建了一个小应用，方便读者理解与对照实现，这一点还是很不错的，不过内容自然也不会太深入，细节还得自己的实践中去发现，不过对于入门 spring cloud 还是非常不错的，看完之后也会对这个大家族有个比较全面的了解。

## 阅读记录

### 2019-03-20

- [springcloud(一)：大话Spring Cloud](http://www.ityouknow.com/springcloud/2017/05/01/simple-springcloud.html)
- [springcloud(二)：注册中心Eureka](http://www.ityouknow.com/springcloud/2017/05/10/springcloud-eureka.html)
- [springcloud(三)：服务提供与调用](http://www.ityouknow.com/springcloud/2017/05/12/eureka-provider-constomer.html)
- [springcloud(四)：熔断器Hystrix](http://www.ityouknow.com/springcloud/2017/05/16/springcloud-hystrix.html)
- [springcloud(十)：服务网关zuul初级篇](http://www.ityouknow.com/springcloud/2017/06/01/gateway-service-zuul.html)
- [springcloud(十三)：注册中心 Consul 使用详解](http://www.ityouknow.com/springcloud/2018/07/20/spring-cloud-consul.html)
  > 比 Eureka 后出来，不同点是 consul 保证了强一致性！支持多数据中心，服务发现、健康检查、Key/Value 存储。

### 2019-03-21

- [springcloud(十五)：服务网关 Spring Cloud GateWay 入门](http://www.ityouknow.com/springcloud/2018/12/12/spring-cloud-gateway-start.html)
  > 与 zuul 的区别是它是异步的，而且支持长连接，顺便还继承了 spring cloud 的许多功能，可以直接替换 zuul