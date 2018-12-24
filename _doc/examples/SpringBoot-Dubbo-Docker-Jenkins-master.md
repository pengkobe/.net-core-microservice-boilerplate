# SpringBoot Dubbo Docker Jenkins

地址： https://github.com/pengkobe/SpringBoot-Dubbo-Docker-Jenkins

## 开发工具

- 基于 IntelliJ idea 进行开发，下载的是旗舰版试用版，试用期为 1 月。
- 下载 docker, 直接在官网下载相关安装包就 ok

## docker 初始化

```bash
# 拉取 tomcat
docker pull chaimm/tomcat:1.1
# 运行各个服务的 tomcat 容器，这里以 gaoxi-user 作为示例，，使用 http://192.168.99.101:8082 访问
docker run --name gaoxi-user-1 -p 8082:8080 -v /usr/web/gaoxi-log:/opt/tomcat/gaoxi-log chaimm/tomcat:1.1
# 依次建立其它几个服务对应的容器
docker run --name gaoxi-product-1 -p 8083:8080 -v /usr/web/gaoxi-log:/opt/tomcat/gaoxi-log chaimm/tomcat:1.1
docker run --name gaoxi-order-1 -p 8084:8080 -v /usr/web/gaoxi-log:/opt/tomcat/gaoxi-log chaimm/tomcat:1.1
docker run --name gaoxi-analysis-1 -p 8084:8080 -v /usr/web/gaoxi-log:/opt/tomcat/gaoxi-log chaimm/tomcat:1.1
docker run --name gaoxi-controller-1 -p 8085:8080 -v /usr/web/gaoxi-log:/opt/tomcat/gaoxi-log chaimm/tomcat:1.1
docker run --name gaoxi-redis-1 -p 8086:8080 -v /usr/web/gaoxi-log:/opt/tomcat/gaoxi-log chaimm/tomcat:1.1
# 拉取 zookeeper
docker pull chaimm/zookeeper-dubbo:1.0
# 结果发现是无法通过 http://192.168.99.101:10000/dubbo-admin-2.8.4/ 访问的，原因是网络不通，网上说需要对 VirtualBox 进行相关设置才行，结果直接整坏了。
# 重启 Kitematic (Alpha) 说只能删除 VM 再重来，于是遵循指导，进入漫长的等待阶段。实际上并不是该问题
# 后来去 Issue 中找，有人发现是 dubbo-admin 没有默认启动，参见:https://github.com/bz51/SpringBoot-Dubbo-Docker-Jenkins/issues/13 
docker run --name zookeeper-debug -p 2182:2181 -p 10000:8080 chaimm/zookeeper-dubbo:1.0
# 在 bash 中运行容器
docker exec -i -t  zookeeper-debug /bin/bash
# 启动 tomacat，发现 10000 端口可以正式访问，只是提示输入密码
cd /zookeeper-3.4.10/tomcat/apache-tomcat-8.5.23/bin
./startup.sh
# 进入 tomcat 配置目录
cd /zookeeper-3.4.10/tomcat/apache-tomcat-8.5.23/webapps/dubbo-admin-2.8.4/WEB-INF
# 显示密码
cat dubbo.properties


# 拉取 jenkins 并运行容器，使用 http://192.168.99.101:10080 访问
docker pull docker.io/jenkins/jenkins
docker run --name jenkins -p 10080:8080 docker.io/jenkins/jenkins
```

## 代码修改

需要对 redisService 做相关修改才能正常运行，原因是实现类(Impl) 中没有覆盖父类的方法


## 配置 jenkins

如果只安装了默认插件，需要在插件管理中添加 maven-release-plugin，安装好后，在 pre-step 中增加配置

```bash
# 目标
clean install -Dmaven.test.skip=true
# pom
Gaoxi-Common-Service-Facade/pom.xml
```  

在 build 中增加配置

```bash
# Root POM
Gaoxi-User/pom.xml
# Goals and options
clean install -Dmaven.test.skip=true -P test
```  

下载远程部署插件， 地址: https://wiki.jenkins.io/display/JENKINS/Deploy+Plugin , 其实就是 `Deploy to container`,我直接在插件市场通过搜索安装
配置构建后执行的操作，上传 war 包到 tomcat，主要设置好 tomcat 登录的账号名/密码


## dubbo 配置

### 服务发布

以在 Gaoxi-User 的 application.properties 中配置服务提供者的信息为例

```bash
spring.dubbo.application.name=user-provider # 本服务的名称
spring.dubbo.registry.address=zookeeper://192.168.99.101:2182 # ZooKeeper所在服务器的IP和端口号
spring.dubbo.protocol.name=dubbo # RPC通信所采用的协议
spring.dubbo.protocol.port=20883 # 本服务对外暴露的端口号
spring.dubbo.scan=com.gaoxi.user.service # 服务实现类所在的路径
```

### 服务引用

在 controller 中进行引用

```bash
spring.dubbo.application.name=controller-consumer # 本服务的名称
spring.dubbo.registry.address=zookeeper://IP:2182 # zookeeper所在服务器的IP和端口号
spring.dubbo.scan=com.gaoxi # 引用服务的路径
```