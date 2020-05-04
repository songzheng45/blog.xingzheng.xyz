---
title: 如何利用 Docker 部署 Skywalking OAP 和 UI
tags:
    - Skywalking
    - docker
category: 技术
---

 本篇总结如何使用 Docker 部署 Skywalking OAP server 和 UI.

<!--more-->

>**SkyWalking**: an APM(application performance monitor) system, especially designed for microservices, cloud native and container-based (Docker, Kubernetes, Mesos) architectures.

参考资料：

- GitHub： [Skywalking Docer](https://github.com/apache/skywalking-docker)



我使用 `docker-compose` 来部署 Skywalking OAP 和 UI。

`docker-compose.yml`配置如下：

```yaml
version: '3'

services:

  skywalking.oap:
    image: apache/skywalking-oap-server
    container_name: skywalking.oap
    restart: always
    ports:
      - 11800:11800
      - 12800:12800
    networks: 
      - apm

  skywalking.ui:
    image: apache/skywalking-ui
    container_name: skywalking.ui
    depends_on:
      - skywalking.oap
    links:
      - skywalking.oap
    restart: always
    ports:
      - 8080:8080
    environment:
      SW_OAP_ADDRESS: skywalking.oap:12800
    networks: 
      - apm

networks:
  apm:
    driver: bridge
```

> 本次部署，使用默认的 H2 数据库存储，使用默认配置。



