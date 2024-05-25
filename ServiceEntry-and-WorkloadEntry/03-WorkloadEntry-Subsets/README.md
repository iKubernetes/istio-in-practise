# Traffic Control demo for ServiceEntry and WorkloadEntry



### ServiceEntry的默认配置

Istio发现并配置经由ServiceEntry所定义的服务，同经那些由Service所发现的服务没有本质上的区别，并且同样会为其生成默认的配置，包括Listener、Cluster和Route等。

测试步骤如下：

- 首先部署"01-workloadentry-nginx.yaml"文件中定义的各WordloadEntry对象至Kubernetes集群；
- 接着，创建“02-serviceentry-nginx.yaml”文件中定义的ServiceEntry对象至Kubernetes集群；
- 随后， 即可于client的交互式接口请求访问“nginx.magedu.com”主机上的服务，并于Kiali中查看请求的结果；

### 流量治理

这些由Workload定义的实例所对应的端点，同实际运行于Kubernetes集群上的Pod所对应端点，在引用方式上并无不同。因此，我们还可以根据其版本创建实例的子集，并由VirtualService定义的流量策略等进行引用。

- 创建“03-destinationrule-subsets.yaml”文件中定义对象至Kubernetes集群，它会把两个v1.25版本的nginx实例定义成v25子集，把一个v1.26版本的nginx实例定义成v26子集；
- 创建“04-virtualservice-weighted-based-routing.yaml”文件中定义的VirtualService对象至Kubernetes集群，它提代了一个基于权重的流量分配策略，并将95%的流量路由至v25子集，将余下的5%的流量路由至v26子集，实例流量分割；

