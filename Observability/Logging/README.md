# 可观测性——网格访问日志



配置步骤：

1. 部署Loki Server和Grafana服务组件
2. 部署opentelemetry collector服务组件
3. 配置网格，添加extensionProvider
4. 创建Telemetry资源对象，配置网格中的工作负载将日志发往opentelemetry collector



### 部署OpenTelemetry Collector

Istio提供了用于部署负责收集Envoy日志流的opentelemetry collector示例文件，即samples/open-telemetry/loki/otel.yaml。运行如下命令，即可直接部署该示例中定义的应用。

```bash
kubectl apply -f samples/open-telemetry/loki/otel.yaml -n istio-system
```

需要注意的是，该示例的配置中，其所引用的Loki服务的URL endpoint指向了“http://loki.istio-system.svc:3100/loki/api/v1/push”，该地址，同"samples/addons/loki.yaml"配置文件中定义部署的Loki Server相匹配。因此，若使用了自定义的Loki Server，需要进行相应的修改。



### 配置网格，添加extensionProvier

配置Istio网格，在Mesh Config配置中，定义extensionProvider，确保其支持将日志流发往前面配置的opentelemetry colloctor，从而最终由Loki Server索引并存储。下面的示例，是基于demo profile所给出的定义，它保存于文件demo-profile.yaml文件中。

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
spec:
  components:
    base:
      enabled: true
    egressGateways:
    - enabled: true
      name: istio-egressgateway
    ingressGateways:
    - enabled: true
      name: istio-ingressgateway
    pilot:
      enabled: true
  hub: docker.io/istio
  profile: demo
  tag: 1.22.2
  values:
    defaultRevision: ""
    gateways:
      istio-egressgateway: {}
      istio-ingressgateway: {}
    global:
      configValidation: true
      istioNamespace: istio-system
    profile: demo
  meshConfig:
    #accessLogFile: /dev/stdout
    #accessLogEncoding: JSON
    enableTracing: true
    extensionProviders:
    - name: otel
      envoyOtelAls:
        service: opentelemetry-collector.istio-system.svc.cluster.local
        port: 4317
        logFormat:
          labels:
            pod: "%ENVIRONMENT(POD_NAME)%"
            namespace: "%ENVIRONMENT(POD_NAMESPACE)%"
            cluster: "%ENVIRONMENT(ISTIO_META_CLUSTER_ID)%"
            mesh: "%ENVIRONMENT(ISTIO_META_MESH_ID)%"
    defaultProviders:
      metrics:
      - prometheus
```

运行如下命令，更新Istio网格配置。

```bash
istioctl install -f demo-profile.yaml --skip-confirmation
```



### Telemetry API

若需要在网格全局启用日志收集功能，则在根名称空间中创建如下资源对象即可。

```yaml
apiVersion: telemetry.istio.io/v1
kind: Telemetry
metadata:
  name: mesh-logging-default
  namespace: istio-system
spec:
  accessLogging:
  - providers:
    - name: otel
```























