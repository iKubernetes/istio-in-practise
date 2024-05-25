# ServiceEntry and Egress Gateway
ServiceEntry、WorkloadEntry和Egress Gateway使用示例。
### 前提条件
需要基于相应的docker-compose定义，于Kubernetes集群外部的主机上启动几个外部nginx实例；
#### 注意
- 各实例监听的地址需要事先进行配置
- 如果有必要，可根据实际情况修改相应的IP地址


### 测试命令
于启动的专用测试客户端Pod的交互式的接口中运行如下命令

```bash
v25=0; v26=0; while true; do if curl -s nginx.magedu.com | grep "^Nginx 25.*" &> /dev/null; then let v25++; else let v26++; fi; echo ${v25}:${v26}; sleep 0.0$RANDOM; done
```
