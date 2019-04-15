# .net Core with microservice

如何基于 .net core 搭建微服务架构?

## 入门阅读

.NET-Microservices-Architecture-for-Containerized-NET-Applications-(Microsoft-eBook)

> 微软发布的一个简单教程，一方面是教程，一方面主要还是推销微软系产品

## 基础设施

- consul 注册中心，.net core 对其支持比较友好
- ocelot 微软开源网关服务，可以与 consul 结合使用
- thrift 跨语言 RPC 框架
- zipkin， 分布式追踪系统，可以看到各个服务之间的延迟
- ELK (beats - logstash - elasticsearch - kibana),监控、日志
- Apollo，配置中心

## 容器化

- Ansible，环境配置搭建工具
- Puppet， 发布工具
- docker-compose 服务编排工具，也可以基于注册中心
- 容器运维平台

## 工具

主要指如何做 CI/CD 流程

- Jenkins
- GitLab

## 进阶

- 私有云+混合云+公有云部署
- 多数据中心部署

## 下一代微服务

- service mesh
  >  Consul 1.2 已经支持