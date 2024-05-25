# ServiceEntry和WorkloadEntry
本示例主要用于演示ServiceEntry和WorkloadEntry的作用~

### 前提
开始本节内容的测试之前，需要首先于Kubernetes集群外部的节点上，基于docker和docker-compose运行00-Deploy-Nginx目录中定义的项目。该项目要求：
- 节点上事先准备有多个IP地址，本示例默认使用172.29.1.201、172.29.1.202和172.29.1.203；本节的部署案例中也会直接引用这几个地址，如果有必要，请进行相应的修改；
- 允许客户端访问这些地址的8091端口；

各Nginx实例将通过特定IP地址之上的8091端口将服务向外暴露。

### 各示例简要说明
- 01：ServiceEntry的用法； 
- 02：ServiceEntry和WorkloadEntry的用法；
- 03：将Workload注册的工作负载视为同网格中的Pod一样的负载实例；
- 04：使用Egress Gateway管理出向流量；

