# 特使代理中基于文件的路由动态配置

> 原文：<https://medium.com/compendium/file-based-dynamic-configuration-of-routes-in-envoy-proxy-6234dae968d2?source=collection_archive---------1----------------------->

![](img/f49d3760b37fa81ef06899eb097dbae8.png)

随着开发人员越来越多地参与边缘代理的配置，如[特使](https://www.envoyproxy.io/)，这些设备提供支持现代开发人员工作流程的开发人员体验变得非常重要。凭借动态服务发现功能，Envoy 使这一切成为可能。除了静态引导配置之外，代理的所有方面都可以[动态配置](https://www.envoyproxy.io/docs/envoy/v1.9.0/intro/arch_overview/dynamic_configuration)。这意味着无需冷启动或热重启即可更新特使代理的配置。在许多情况下，这是处理频繁配置更改的首选和最简单的方法。

配置可以由配置服务器提供，也可以由文件提供。这种服务器有一些开源和商业实现。如果需要，项目可以基于可用的库、实现和示例([envoy proxy](https://github.com/envoyproxy)/[go-control-plane](https://github.com/envoyproxy/go-control-plane)，[envoy proxy](https://github.com/envoyproxy)/[Java-control-plane](https://github.com/envoyproxy/java-control-plane))实现自己的功能。如果需求不需要基于服务器的解决方案，简单的基于文件的方法可能就足够了。将创建一个具有新配置的文件，在文件系统移动操作中，当前文件将被新配置覆盖。使用`inotify`来监控文件的变化，Envoy 将加载新的配置，如果有效的话就应用它。如果无效，先前的状态将保持活动状态，并且相应的消息将被写入日志。

发现服务的整个家族被指定为 xDS。这篇文章展示了如何使用文件作为配置源而不是服务器来实现路由发现服务(RDS)。

这篇文章附带的代码可以在 [GitHub](https://github.com/mgfeller/xds-envoy) 上找到。它基于[特使代理库](https://github.com/envoyproxy/envoy)的前端代理示例代码，记录在
[特使文档](https://www.envoyproxy.io/docs/envoy/v1.9.0/start/sandboxes/front_proxy.html)中。

在特使配置文件中，找到您想要用动态配置替换的字段`route_config`和`envoy.http_connection_manager`过滤器:

```
- name: envoy.http_connection_manager
  config:
    codec_type: auto
    stat_prefix: ingress_http
    route_config:
      name: local_route
      virtual_hosts:
      - name: backend
        domains:
        - "*"
        routes:
        - match:
            prefix: "/service/1"
          route:
            cluster: service1
        - match:
            prefix: "/service/2"
          route:
            cluster: service2
    http_filters:
    - name: envoy.router
      config: {}
```

将该字段替换为一个名为`[rds](https://www.envoyproxy.io/docs/envoy/v1.9.0/api-v2/config/filter/network/http_connection_manager/v2/http_connection_manager.proto#envoy-api-msg-config-filter-network-http-connection-manager-v2-rds)`的字段，该字段指向包含相应配置的文件:

```
- name: envoy.http_connection_manager
  config:
    codec_type: auto
    stat_prefix: ingress_http
    rds:
      route_config_name: local_route
      config_source:
        path: /etc/rds/rds.yaml
    http_filters:
    - name: envoy.router
      config: {}
```

文件`rds.yaml`的内容是

```
version_info: "0"
resources:
- "[@type](http://twitter.com/type)": type.googleapis.com/envoy.api.v2.RouteConfiguration
  name: local_route
  virtual_hosts:
  - name: backend
    domains:
    - "*"
    routes:
    - match:
        prefix: "/service/1"
      route:
        cluster: service1
    - match:
        prefix: "/service/2"
      route:
        cluster: service2
```

现在，如果需要更改配置，可以创建一个具有新配置的文件，比如说`rds-new.yaml`，然后在现有文件上移动:

```
mv rds-new.yaml rds.yaml
```

Envoy 将获取更改，验证并激活新配置。

**进一步阅读和资源:**

*   [特使代理:动态配置](https://www.envoyproxy.io/docs/envoy/v1.9.0/intro/arch_overview/dynamic_configuration)
*   [特使代理:通用数据平面 API](https://blog.envoyproxy.io/the-universal-data-plane-api-d15cec7a)
*   [特使代理:xDS REST 和 gRPC 协议](https://github.com/envoyproxy/data-plane-api/blob/master/XDS_PROTOCOL.md)
*   [Katacoda: envoyproxy —基于文件的动态路由配置(EDS)](https://www.katacoda.com/envoyproxy/scenarios/file-based-dynamic-routing-configuration)
*   [Envoy 和可编程边缘:边缘代理和开发者体验](https://thenewstack.io/envoy-and-the-programmable-edge-edge-proxies-and-the-developer-experience/)

*原载于*[*medium.com*](/p/5ec5998f7902/)*。*