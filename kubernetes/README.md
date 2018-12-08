# Kubernetes

因为首尾字母中间有8个字符，所以被简写成 K8s

- K8s 是底层资源与容器间的一个抽象层
- K8s 是 Google 开源的容器集群管理系统。
- K8s 是一个完备的分布式系统支撑平台，具有完备的集群管理能力，多层次的安全防护和准入机制、多租户应用支撑能力、透明的服务注册和发现机制、內建负载均衡器、强大的故障发现和自我修复能力、服务滚动升级和在线扩容能力、可扩展的资源自动调度机制以及多粒度的资源配额管理能力。同时 K8s 提供完善的管理工具，涵盖了包括开发、部署测试、运维监控在内的各个环节。

## 架构

- Master 节点主要负责控制和维护整个集群的状态，比如创建资源、删除资源。
- Node 节点主要负责运行任务，比如启动容器

## 案例学习

- `example/k8s-mastery-master`

## readings

- [Kubernetes 入门简明教程](https://gitbook.cn/books/5aadcf4f984e353193a90ddb/index.html)
- [三小时攻克 Kubernetes！](https://www.itcodemonkey.com/article/4261.html)