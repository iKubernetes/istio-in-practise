# 部署Keycloak



## 使用helm部署Keycloak

本示例将基于helm工具来部署KeyCloak。

### 部署PostgreSQL

KeyCloak依赖于PostgreSQL。本示例将会在keycloak名称空间中部署主从复制功能的PostgreSQL数据库。

```bash
helm install postgresql oci://registry-1.docker.io/bitnamicharts/postgresql -f deploy/postgresql-values.yaml -n keycloak --create-namespace
```

### 部署Keycloak

本示例将会在keycloak名称空间中部署面向生产环境的Keycloak，这会强制要求启用HTTPS服务，helm在其部署过程中支持自动创建一个测试使用的自签证书。

```bash
helm install keycloak oci://registry-1.docker.io/bitnamicharts/keycloak -f deploy/keycloak-values.yaml -n keycloak --create-namespace
```



## 发布Keycloak UI

本示例提供了基于Istio API开放Keycloak UI至集群外部的资源对象。需要注意的是，Keycloak自身提供了HTTPS服务，国此，服务发布需要基于SSL Paththrough的方式进行。

```bash
kubectl apply -f istio/
```



