# part3

## 33 下一代微服务架构 service mesh

简单的讲，就是在消费者和服务者都部属个代理，代理实现了微服务架构需要的各种监控啊、重试啊等等，据说是未来发展的方向

## 34 Istio

特点

- 引入 Control Plane 理念 服务治理能力更加强大、是 Linkerd 的进化
  - Pilot 实现流量控制，提供 Rules API 与 Envoy API，并且通过抽象层和平台适配层抹平了平台之间调用的差异
  - Mixer 实现策略控制和监控日志收集
  - Citadel，用来保证服务的安全
- SideCar（Proxy） 采用 Envoy，性能更好
  - 基于 c++ 实现
  - 可拓展性高，可以定制
  - 动态可配置，无需重启
- 可以和 kubernetes 很自然的整合

## 35 微博 Service 实践之路（上）

从经典微服务架构切换为 Istio 对大多数公司来说并不现实，所以会走出自己特有的一条道路

- 多语言，PHP vs Java
  - 中间链路消耗大
  - 全链路扩容难
  - 混合云部署难
- PHP 支持 motan yar 协议
  - 需要经过复杂的协议转换
  - 必须依赖 nginx
- gRPC
  - 高度依赖 PB 序列化，而 PHP 对 PB 支持性不好
  - gRPC 还不支持 PHP 作为 server
- 代理才是出路
  - 使用部署在 Client 端的 Agent
- 向 Service Mesh 迈进
  - 都使用 SideCar 实现流量转发，微博的 Weibo Mesh 对业务有一定的侵入性

## 35 微博 Service 实践之路（下）

- Motan-go Agent
  - High Available
  - Load Balance
  - Filter Chain
  - Serialize
  - Transport
  - EndPoint 
- 统一服务治理中心
  - Vintage，动态服务注册与发现
  - Graphite，监控系统
  - SGcenter 与 Vintage，自动切流量降级
  - Diviner，容量评估系统，基于 Vintage 与 Graphite 的数据
- 收益
  - 跨语言服务调用能力
  - 统一服务治理能力
  - 业务无感知的持续功能更新能力

## 36 结束语

对文章的反复阅读结合实践应该可以出真知
