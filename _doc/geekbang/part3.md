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