# Microservices

https://www.martinfowler.com/articles/microservices.html  
https://www.jianshu.com/p/df87c6b4d3b7 

## What are microservices
the microservice architectural style is an approach to developing a single application as a suite of small services, each running in its own process and communicating with lightweight mechanisms, often an HTTP resource API. These services are built around business capabilities and independently deployable by fully automated deployment machinery. There is a bare minimum of centralized management of these services, which may be written in different programming languages and use different data storage technologies.  
微服务架构是一种将一个单一应用程序开发为一组小型服务的方法，每个服务运行在自己的进程中，服务间通信采用轻量级通信机制(通常使用HTTP资源API)。这些服务围绕业务能力构建，并且可通过全自动部署机制独立部署。这些服务共用一个最小型的集中式的管理，服务可用不同的语言开发，使用不同的数据存储技术。

## Compare monolithic with microservices 
monolithic server runs your logic in a single process. 
单体服务器的所有逻辑都运行在一个单一进程中
Monolithic applications can be successful, but increasingly people are feeling frustrations with them - especially as more applications are being deployed to the cloud . Change cycles are tied together - a change made to a small part of the application, requires the entire monolith to be rebuilt and deployed.
变更周期被捆绑在一起 —— 即使只变更应用程序的一部分，也需要重新构建并部署整个单体。长此以往，通常将很难保持一个良好的模块架构，这使得变更很难只发生在需要变更的模块内。

## Componentization via Services
component is a unit of software that is independently replaceable and upgradeable.  
组件是一个可独立替换和独立升级的软件单元。  
We define libraries as components that are linked into a program and called using in-memory function calls, while services are out-of-process components who communicate with a mechanism such as a web service request, or remote procedure call.  
libraries库被视作组件，调用方式是内存内的函数调用。Services 在这里是指远程的服务。

One main reason for using services as components (rather than libraries) is that services are independently deployable.  
使用服务作为组件而不是使用库的主要原因：
服务是可独立部署的  

Another consequence of using services as components is a more explicit component interface.  
更加明确的组件接口：服务通过明确的远程调用机制可以避免组件间的紧耦合

Using services like this does have downsides. Remote calls are more expensive than in-process calls, and thus remote APIs need to be coarser-grained, which is often more awkward to use. 
缺点：
远程调用比进程内调用更昂贵
导致远程API设计成粗粒度，不便于使用(为了屏蔽一些对用户透明的内部实现细节)

## Organized around Business Capabilities
Any organization that designs a system (defined broadly) will produce a design whose structure is a copy of the organization's communication structure.  
一个组织所产生的系统的设计结构实际是复制了该组织通讯结构。

The microservice approach to division is different, splitting up into services organized around business capability. Such services take a broad-stack implementation of software for that business area, including user-interface, persistant storage, and any external collaborations. Consequently the teams are cross-functional, including the full range of skills required for the development: user-experience, database, and project management.  
微服务采用不同的分割方法，划分成围绕业务能力组织的服务。这些服务采取该业务领域软件的宽栈实现，包括用户接口、持久化存储和任何外部协作。因此，团队都是跨职能的，包括开发需要的全方位技能(用户体验、数据库、项目管理)。

## Products not Projects
Most application development efforts that we see use a project model: where the aim is to deliver some piece of software which is then considered to be completed. On completion the software is handed over to a maintenance organization and the project team that built it is disbanded.  
Microservice proponents tend to avoid this model, preferring instead the notion that a team should own a product over its full lifetime.  
项目模式：目标是交付将要完成的一些软件。完成后的软件被交接给维护组织，然后它的构建团队就解散了。  
微服务支持者倾向于避免这种模式，而是认为一个团队应该负责产品的整个生命周期。

## Smart endpoints and dumb pipes
It does not suggest ESB as ESB products often include sophisticated facilities for message routing, choreography, transformation, and applying business rules.  
The microservice community favours an alternative approach: smart endpoints and dumb pipes. Applications built from microservices aim to be as decoupled and as cohesive as possible
The two protocols used most commonly are HTTP request-response with resource API's and lightweight messaging

## Decentralized Governance

## Decentralized Data Management

## Infrastructure Automation
- automated tests 自动化测试
- automated deployment 自动化部署
- managing microservices in production 在生产环境中管理微服务

## Design for failure

## Evolutionary Design
The key property of a component is the notion of independent replacement and upgradeability, which implies we look for points where we can imagine rewriting a component without affecting its collaborators.  
分割应用的原则：组件可独立地更换和升级

This emphasis on replaceability is a special case of a more general principle of modular design, which is to drive modularity through the pattern of change. You want to keep things that change at the same time in the same module. Parts of a system that change rarely should be in different services to those that are currently undergoing lots of churn. If you find yourself repeatedly changing two services together, that's a sign that they should be merged.  
微服务强调可替代性，通过变更模式来驱动模块化：同时变化的东西保持在同一模块中。系统中很少变更的部分应该和正在经历大量扰动的部分放在不同的服务里。如果你发现自己不断地一起改变两个服务，这是它们应该被合并的一个标志。

# Microservice related implemmentaions

## Eureka： service registration and locating
Eureka is a REST based service that is primarily for locating services for the purpose of load balancing and failover of middle-tier servers.  
服务的注册与发现  
eureka是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳（因此可以在内存中完成），在默认情况下erureka server也是一个eureka client ,必须要指定一个 server
```
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```
通过eureka.client.registerWithEureka：false和fetchRegistry：false来表明自己是一个eureka server.

client, to register itself as a service on the eureka server
```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
server:
  port: 8762
spring:
  application:
    name: service-hi
```

##　断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话

## Ribbon + restTemplate Or Feign: load balance
Ribbon is a client side load balancer.  
在程序的启动类ServiceRibbonApplication 加@EnableHystrix注解开启Hystrix

Feign is a Java to HTTP client binder, which reduce the complexity
- Feign is based on interface annotation
- Feign integrates Ribbon, so it has load balancer
- Feign integrates Hystrix, so it has circuit breaker

## Hystrix: fault tolerance and latency tolerance
The Hystrix framework library helps to control the interaction between services by providing fault tolerance and latency tolerance. It improves overall resilience of the system by isolating the failing services and stopping the cascading effect of failures.  
https://www.baeldung.com/introduction-to-hystrix

## Zuul:  router and filter
Zuul is a JVM based router and server side load balancer by Netflix.
```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
server:
  port: 8769
spring:
  application:
    name: service-zuul
zuul:
  routes:
    api-a:
      path: /api-a/**
      serviceId: service-ribbon
    api-b:
      path: /api-b/**
      serviceId: service-feign
```

## spring cloud bus