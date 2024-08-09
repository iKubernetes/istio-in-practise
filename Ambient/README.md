# Ambient

前提：准备就绪的Kubernetes集群，以及必要的附件，例如Metrics Server、Ingress Nginx、MetalLB和OpenEBS等。

注意：Ambient结合Cilium网络插件时，依赖于多个额外的配置步骤，因此，建议使用Calico等网络插件。

## 部署Istio Ambient

### 部署方式一（Kubernetes Gateway API）

运行如下命令，部署Istio Ambient。

```bash
istioctl install --set profile=ambient --skip-confirmation
```

上面的命令会创建“daemonset/ztunnel”、“daemonset/istio-cni-node”和“deployment/istiod”等几个应用。运行如下命令，查看各应用的Pod的运行状态并确认其运行正常。

```
kubectl get pods -n istio-system
NAME                          READY   STATUS    RESTARTS   AGE
istio-cni-node-2sqzk          1/1     Running   0          47m
istio-cni-node-864wb          1/1     Running   0          47m
istio-cni-node-tkjfm          1/1     Running   0          47m
istio-cni-node-vcf6r          1/1     Running   0          47m
istiod-745c76ff44-bv74p       1/1     Running   0          47m
ztunnel-957vm                 1/1     Running   0          47m
ztunnel-lplhf                 1/1     Running   0          47m
ztunnel-rx2fk                 1/1     Running   0          47m
ztunnel-v5bk5                 1/1     Running   0          47m
```

随后，运行如下命令，在Kubernetes集群上部署Gateway CRD。其背后的Controller，是由Istio提供的“istio.io/gateway-controller”。

```bash
kubectl get crd gateways.gateway.networking.k8s.io &> /dev/null || \
  { kubectl kustomize "github.com/kubernetes-sigs/gateway-api/config/crd/experimental?ref=v1.1.0" | kubectl apply -f -; }
```

设定完成后，需要暴露服务至Kubernetes集群外部时，可通过Kubernetes Gateway API进行。而在集群外部访问暴露的服务时，需要经由专用Gateway资源对象的Service进行。



### 部署方式二（Istio Ingress Gateway API）

运行如下命令，部署Istio Ambient，并启用Ingress Gateway。

```bash
istioctl install --set profile=ambient --set "components.ingressGateways[0].enabled=true" \
             --set "components.ingressGateways[0].name=istio-ingressgateway" --skip-confirmation
```

上面的命令会创建“daemonset/ztunnel”、“daemonset/istio-cni-node”、“deployment/istiod”和“deployment/istio-ingressgateway”等几个应用。运行如下命令，查看各应用的Pod的运行状态并确认其运行正常。

```
kubectl get pods -n istio-system
NAME                                    READY   STATUS    RESTARTS   AGE
istio-cni-node-2v9bs                    1/1     Running   0          28m
istio-cni-node-bg9dz                    1/1     Running   0          28m
istio-cni-node-q7hs6                    1/1     Running   0          28m
istio-cni-node-thn7h                    1/1     Running   0          28m
istio-ingressgateway-654778f948-ftkjm   1/1     Running   0          7m35s
istiod-745c76ff44-s9msv                 1/1     Running   0          29m
ztunnel-24jc7                           1/1     Running   0          28m
ztunnel-dcmfl                           1/1     Running   0          28m
ztunnel-hxn4r                           1/1     Running   0          28m
ztunnel-rtdq9                           1/1     Running   0          28m
```

该部署方式中，需要暴露服务至Kubernetes集群外部时，可通过Istio Ingress Gateway API进行。而在集群外部访问暴露的服务时，需要创建Istio Gateway资源对象，并经由Ingress Gateway相关Service对象的入口进行访问。



### 启用Ambient网格

在名称空间上添加特定的标签，即可将该名称空间中的应用加入到Ambient网格中。下面的命令，在default namespace上添加了相应的标签。

```bash
kubectl label namespace default istio.io/dataplane-mode=ambient
```





## 在网格中运行示例应用

将Istio提供的Bookinfo示例创建于添加了“istio.io/dataplane-mode=ambient”的名称空间中，即可托管至Istio服务网格。

```bash
cd /usr/local/istio/
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
```

若需要将Bookinfo的productpage服务暴露至集群外部，可通过如下方式进行。

- 使用Kubernetes Gateway API的环境

  该方式的使用，依赖于Kubernetes Gateway CRD。

  ```
  kubectl apply -f samples/bookinfo/gateway-api/bookinfo-gateway.yaml
  ```

  列出相关的Gateway资源对象，如下命令结果中的“ADDRESS”段的地址即为访问该服务的入口。

  ```
  kubectl get gateways
  NAME               CLASS   ADDRESS       PROGRAMMED   AGE
  bookinfo-gateway   istio   172.29.7.81   True         58m
  ```

- 使用Istio Ingress Gateway API的环境

  ```
  kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
  ```



> 提示：Istio官方文档目前（v1.22.3）已经主推基于Kubernetes Gateway API的方式发布应用。



## 启用Waypoint

运行如下命令，即可在启用了ambient网格的名称空间上启用waypoint，从而激活L7策略和HTTP高级路由功能。

```
istioctl x waypoint apply --enroll-namespace --wait
```

该命令会在启用了ambient网格的名称空间中创建Waypoint Pod，并由其负责该名称空间的L7代理。

