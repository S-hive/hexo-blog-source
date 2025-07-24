---
title: Tomcat面试
cssclasses:
  - Serve
  - Tomcat
---
#### 服务的底层原理是什么？
Tomcat 是一个开源的 Servlet 容器，属于 Apache 软件基金会项目，它主要用于运行 Java Web 应用程序。下面来剖析其底层工作原理：
1. **请求处理流程**  
    客户端向服务器发起 HTTP 请求，请求首先会到达 Tomcat 的连接器（Connector）。  
    连接器把请求转换为 ServletRequest 对象，接着将该对象传递给容器（Container）进行处理。  
    容器依据 Servlet 映射配置，找到对应的 Servlet，并调用其 service () 方法。  
    Servlet 处理完请求后，会返回一个 ServletResponse 对象，连接器再把这个对象转换为 HTTP 响应返回给客户端。
2. **核心组件**
	* **连接器（Connector）*：负责处理底层的网络通信，支持像 HTTP/1.1、HTTP/2 等多种协议。
	- **容器（Container）**：由 Engine、Host、Context 和 Wrapper 这四个层次的组件构成，主要负责 Servlet 的生命周期管理以及请求的路由。
	- **Servlet 引擎**：按照 Servlet 规范来执行 Servlet 和 JSP。
	- **生命周期管理**：借助 Lifecycle 接口对组件的生命周期（如初始化、启动、停止等）进行管理。
3. **线程模型**  
    Tomcat 支持多种线程模型，例如：
	- **BIO（阻塞 I/O）**：一个线程处理一个连接，这种方式在并发性能上表现较差。
	- **NIO（非阻塞 I/O）**：利用 Selector 实现多路复用，一个线程能够处理多个连接。
	- **APR（Apache Portable Runtime）**：基于本地库实现，在高并发场景下性能最佳。

#### 有什么软件或者服务跟要学习的服务类似，他们的异同点是什么，是否可以替代，不可替代点是什么？
下面是与 Tomcat 类似的常见 Web 服务器和 Servlet 容器：

| 产品                     | 类型             | 特点                                          |
| ---------------------- | -------------- | ------------------------------------------- |
| **Apache HTTP Server** | Web 服务器        | 主要用于静态内容服务，不过可以通过 mod_jk 等模块与 Servlet 容器集成。 |
| **Nginx**              | Web 服务器 / 反向代理 | 轻量级且高性能，擅长处理静态内容和反向代理，同样能和 Servlet 容器配合使用。  |
| **Jetty**              | Servlet 容器     | 轻量级、模块化，在嵌入式场景中应用广泛，和 Tomcat 高度兼容。          |
| **WildFly (JBoss)**    | 应用服务器          | 功能全面，支持 EJB 等 Java EE 标准，但资源占用相对较多。         |
| **GlassFish**          | 应用服务器          | 是 Java EE 的参考实现，不过在企业级应用中的使用频率逐渐降低。         |
**替代分析**：
- **部分替代**：Nginx + 独立 Servlet 容器（如 Tomcat）的组合可以替代 Tomcat 的部分功能，特别是在负载均衡和静态内容处理方面。
- **完全替代**：Jetty 由于和 Servlet 规范兼容，并且更加轻量级，所以在大多数场景下可以替代 Tomcat。
- **不可替代点**：
    - Tomcat 对 Apache HTTP Server 和 Nginx 起到补充作用，后两者主要侧重于静态内容服务，而 Tomcat 专注于动态内容处理。
    - 企业级应用可能会依赖 WildFly 等应用服务器提供的完整 Java EE 功能。
    - 对于已经深度集成 Tomcat 特定功能（如自定义 Realm、Valve）的应用，替换成本较高。
#### 我们在公司里都会在哪些场景下使用

1. **Web 应用服务器**
- 作为 Java Web 应用的运行环境，比如 Spring Boot 应用默认就内嵌 Tomcat。
- 部署传统的 WAR 包应用，像基于 Struts、JSF 框架开发的应用。
2. **微服务架构**
- 为微服务提供轻量级的运行容器。
- 与 Spring Cloud 等框架结合，构建分布式系统。
3. **开发测试环境**
- 开发阶段进行本地调试。
- CI/CD 流程中作为测试服务器，例如在 Jenkins、GitLab CI 中使用。
4. **与其他组件集成**
- 和 Nginx 搭配，由 Nginx 负责负载均衡和静态内容处理，Tomcat 专注于动态请求。
- 与数据库、消息队列等后端服务集成。
5. **嵌入式场景**
- 作为库嵌入到 Java 应用中，为应用提供 HTTP 服务，例如在 Spring Boot 应用中。
**典型案例**：
- 中小型企业网站，由于流量不大，会直接使用 Tomcat 作为 Web 服务器。
- 大型企业系统中的独立服务模块，可能会选择部署在 Tomcat 上。
- 开发团队使用 Tomcat 作为开发和测试环境，生产环境则切换到更复杂的应用服务器。
#### 服务在使用或者安装中遇到过什么问题

在使用或安装 Tomcat 服务时，常见的问题包括：

1. **安装配置问题**
    
    - **端口冲突**：默认使用 8080 端口，若被其他应用（如 Skype、另一个 Tomcat 实例）占用，启动会失败。
    - **环境变量配置错误**：未正确设置 JAVA_HOME 或 CATALINA_HOME 环境变量，导致启动脚本无法找到 Java 运行时。
    - **权限不足**：在 Linux 系统中，非 root 用户启动时可能无法绑定低于 1024 的端口（如 80）。
2. **性能与内存问题**
    
    - **内存溢出（OOM）**：默认内存配置较低（如 128MB 堆内存），高并发下易触发 OutOfMemoryError。
    - **线程池配置不合理**：默认线程数不足（如 maxThreads=200），导致请求堆积。
    - **GC 频繁**：堆内存分配不合理，导致频繁 Full GC，影响响应时间。
3. **安全漏洞**
    
    - **默认配置风险**：未修改默认的 manager/host-manager 应用密码，可能被远程攻击。
    - **Servlet 容器漏洞**：如 Ghostcat 漏洞（CVE-2020-1938）允许读取 WEB-INF 目录下的文件。
    - **过时依赖**：使用的 Apache Commons FileUpload 等组件存在已知漏洞。
4. **应用部署问题**
    
    - **WAR 包冲突**：多个应用依赖同一库的不同版本，导致类加载冲突。
    - **JSP 编译错误**：JSP 文件修改后未重新编译，或编译时依赖缺失。
    - **上下文路径冲突**：多个应用配置相同的 Context Path，导致访问异常。
5. **集群与负载均衡**
    
    - **Session 复制失败**：集群环境中 Session 未能正确复制，导致用户登录状态丢失。
    - **反向代理配置错误**：Nginx/Apache 与 Tomcat 集成时，未正确配置 proxy_pass 或 sticky session。
#### 服务的其他使用场景是什么 (在别的公司如何用）
1. **API 网关 / 服务网关**
    
    - 作为轻量级网关，处理 RESTful API 请求，集成 Spring Cloud Gateway 等组件。
    - 某金融科技公司使用 Tomcat 部署 API 网关，通过 Filter 实现请求鉴权和流量控制。
2. **企业级 SOA 平台**
    
    - 在传统企业中，作为 SOAP Web 服务的容器，支撑遗留系统间的集成。
    - 某制造业公司使用 Tomcat 运行基于 Axis2 的 Web 服务，与 ERP 系统对接。
3. **IoT 平台后端**
    
    - 处理 IoT 设备的 HTTP/MQTT 请求，结合数据库存储设备数据。
    - 某智能家居公司使用 Tomcat 接收设备状态数据，并通过 WebSocket 推送至客户端。
4. **教学与培训**
    
    - 高校或培训机构作为 Java Web 开发教学的基础环境。
    - 某培训机构使用 Tomcat 配合 Eclipse，教授 Servlet/JSP 开发。
5. **嵌入式系统**
    
    - 在机顶盒、智能终端等资源受限设备中嵌入 Tomcat，提供 Web 管理界面。
    - 某网络设备厂商在路由器中集成 Tomcat 微内核，提供 Web 配置界面。
6. **爬虫与数据采集**
    
    - 部署爬虫管理平台，调度和监控分布式爬虫任务。
    - 某电商公司使用 Tomcat 运行 Scrapy 爬虫管理系统，定时抓取竞品数据。
#### 目前我们用的版本号什么，有哪些版本，最新版本号更新到什么程度，各个版本有什么异同

|版本系列|支持的 Servlet/JSP 规范|状态|适用场景|
|---|---|---|---|
|**10.x**|Servlet 5.0/JSP 3.0|活跃版本|基于 Jakarta EE 9 + 的应用|
|**9.x**|Servlet 4.0/JSP 2.3|LTS 版本|大多数生产环境|
 **最新版本（截至 2025 年 7 月）**
- **Tomcat 10.1.x**：最新稳定版，完全支持 Jakarta EE 10，移除了对 Java EE 的支持。
- **Tomcat 9.0.83**：LTS 版本，仍被广泛使用，支持 Java 8-17。
- **Tomcat 11.x**：开发中，将支持 Jakarta EE 11，计划引入 HTTP/3 等新特性。

#### 这个服务或者软件有哪些常见的模块
作以支持 Java Web 应用的运行：
 1.**核心功能模块**

- **Catalina**：Tomcat 的核心容器模块，包含以下子模块：
    
    - **Connector（连接器）**：处理网络协议（HTTP、AJP 等），负责接收客户端请求并转换为容器可处理的格式。
    - **Container（容器）**：由 Engine（引擎）、Host（虚拟主机）、Context（应用上下文）、Wrapper（Servlet 包装器）四级组件构成，管理 Servlet 生命周期和请求路由。
    - **Realm**：负责用户认证和授权，支持基于文件、数据库、LDAP 等多种认证方式。
    - **Valve**：类似拦截器，可在请求处理的不同阶段插入逻辑（如日志记录、访问控制）。
- **Jasper**：JSP 引擎模块，负责将 JSP 文件编译为 Servlet 类（.java→.class），支持热部署（修改 JSP 后自动重新编译）。
    
- **Coyote**：处理底层 I/O 通信的模块，提供 BIO、NIO、NIO2、APR 等多种 I/O 模型，支持 HTTP/1.1、HTTP/2、AJP 等协议。
    
- **Cluster**：集群模块，支持 Session 复制、负载均衡和故障转移，依赖组播或静态列表发现集群节点。
    
- **Naming**：JNDI（Java 命名与目录接口）实现模块，用于管理数据源、邮件会话等资源。
    
2. **扩展模块**

- **WebSocket 支持**：通过`org.apache.tomcat.websocket`包实现 WebSocket 协议（RFC 6455），支持双向通信。
- **Security**：提供 SSL/TLS 配置、CSP（内容安全策略）、CSRF 防护等安全增强功能。
- **Manager**：Web 管理界面模块，用于部署应用、监控服务器状态（需配置用户权限）。
- **Host-Manager**：虚拟主机管理模块，支持通过 Web 界面添加 / 删除虚拟主机。
#### 服务的日志，服务的数据都是如何处理的
**1.日志处理**
Tomcat 的日志分为多种类型，通过`conf/logging.properties`配置：
- **Catalina 日志**：记录服务器启动、关闭、组件加载等系统级信息，默认输出到`logs/catalina.out`（Linux）或`logs/catalina.yyyy-MM-dd.log`（按日期滚动）。
- **访问日志**：记录 HTTP 请求详情（IP、URL、状态码、响应时间等），默认通过`conf/server.xml`中的`AccessLogValve`配置，输出到`logs/access_log.yyyy-MM-dd.txt`，格式可自定义（如 Common Log Format）。
- **应用日志**：Web 应用通过`java.util.logging`或 Log4j 等框架输出的日志，默认与 Catalina 日志合并，可通过配置分离到独立文件。
- **[localhost](https://localhost/)日志**：记录虚拟主机`localhost`相关的信息，如应用部署失败的原因。
- **manager/host-manager 日志**：记录管理界面的操作日志，用于审计管理行为。
**日志轮转策略**：默认按日期分割日志，可配置`maxDays`限制保留天数，或通过第三方工具（如 Logrotate）实现日志归档和清理。
#### 服务的升级是怎么做的，每个版本都有哪些相应的漏洞

| Tomcat 8.5-10 | CVE-2022-23181 | 拒绝服务：HTTP/2 请求处理不当导致内存泄漏                          | 10.0.20+、9.0.62+ |
| ------------- | -------------- | ------------------------------------------------- | ---------------- |
| Tomcat 9-10   | CVE-2023-28709 | 远程代码执行：`WebSocket`处理不当，恶意请求可触发 ClassCastException | 10.1.8+、9.0.74+  |
#### 这个服务可以与哪些服务结合有意想不到的功能或者解决方案
Tomcat 作为 Java Web 容器，与其他服务结合可扩展出多样化的企业级解决方案，以下是典型场景：

1. **与反向代理 / 负载均衡服务结合**

- **搭配 Nginx/Apache HTTP Server**：
    
    - Nginx 作为前端反向代理，处理静态资源（CSS/JS/ 图片），将动态请求转发给 Tomcat，提升静态资源加载速度并减轻 Tomcat 压力。
    - 利用 Nginx 的负载均衡功能（如`upstream`模块），将请求分发到多台 Tomcat 服务器，实现集群扩容和高可用（例如电商平台秒杀场景的流量分流）。
- **搭配 HAProxy**：
    
    - 针对 TCP 层的负载均衡（如 AJP 协议），支持更精细的健康检查和会话保持，适合对性能要求极高的金融交易系统。
2. **与缓存 / 消息队列服务结合**

- **搭配 Redis**：
    
    - 将 Tomcat 的 Session 数据存储在 Redis 中（通过`tomcat-redis-session-manager`插件），解决集群环境下 Session 共享问题，同时利用 Redis 的高读写性能提升 Session 访问速度。
    - 结合 Redis 作为应用级缓存，Tomcat 部署的 Java Web 应用可直接读写 Redis 缓存热点数据（如商品详情），减少数据库访问压力。
- **搭配 RabbitMQ/Kafka**：
    
    - Tomcat 应用作为消息生产者 / 消费者，通过消息队列异步处理非实时任务（如订单日志写入、邮件发送），避免同步操作阻塞 Tomcat 线程，提升系统吞吐量（例如电商订单提交后异步通知仓库）。

3. **与容器 / 云服务结合**

- **搭配 Docker/Kubernetes**：
    
    - 将 Tomcat 打包为 Docker 镜像，通过 Kubernetes 实现自动扩缩容、滚动更新和故障自愈，适合微服务架构（例如 Spring Cloud 应用部署在 Tomcat 容器中，通过 K8s 管理多实例）。
    - 结合 Docker Compose 快速搭建本地开发环境（Tomcat+MySQL+Redis 一键启动），降低环境配置成本。
- **搭配云服务（如 AWS ECS / 阿里云容器服务）**：
    
    - 利用云厂商的负载均衡、日志服务和监控告警功能，实现 Tomcat 应用的全托管运维，无需关注底层服务器部署。

 4. **与监控 / 日志服务结合**

- **搭配 Prometheus+Grafana**：
    
    - 通过`tomcat_exporter`采集 Tomcat 的 JVM 内存、线程数、请求响应时间等指标，结合 Grafana 可视化监控面板，实时预警性能瓶颈（如线程池满导致请求阻塞）。
- **搭配 ELK Stack（Elasticsearch+Logstash+Kibana）**：
    
    - 用 Logstash 收集 Tomcat 的访问日志和应用日志，存储到 Elasticsearch，通过 Kibana 分析用户行为（如热门访问路径）或排查异常（如频繁 500 错误的触发条件）。

5. **与安全工具结合**

- **搭配 ModSecurity**：
    - 作为 Web 应用防火墙（WAF），与 Nginx+Tomcat 联动，拦截 SQL 注入、XSS 等攻击请求，保护 Tomcat 部署的应用（例如政务系统的敏感数据接口防护）。
#### 从安全的角度来考虑这个软件是否安全或者需要在哪里调优？
Tomcat 的核心安全风险点包括：
1. **默认配置风险**：默认安装包含`manager`、`host-manager`等管理界面，且可能存在弱密码（如默认用户`admin`无密码），易被未授权访问。
2. **协议 / 组件漏洞**：历史版本存在高危漏洞（如 CVE-2020-1938 Ghostcat 文件读取漏洞、CVE-2023-28709 远程代码执行漏洞），多与组件缺陷或配置疏漏相关。
3. **权限控制不足**：文件系统权限宽松（如`conf/`目录可被非 root 用户修改）、应用部署目录权限过高，可能导致恶意文件写入。

