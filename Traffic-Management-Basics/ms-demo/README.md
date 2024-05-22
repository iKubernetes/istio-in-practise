# MicroService流量治理示例

本示例有两个应用：

- frontend：前端应用，可经由BACKEND_URL接入后端服务，该示例配置其监听于“80/TCP”端口；
  - /services：接收到请求后，会自动从后端服务取得必要的信息，并生成响应报文发送给客户端；
  - /api/get_service：响应当前应用的名称；
  - /：除以上两个path外，起始于“/”的其它路径会被代理给BACKEND_URL环境变量指定的后端；v2.3.1版本，支持将“x-canary”和"x-user"标头转发至后端服务；
- demoapp：后端应用，该示例配置其监听于“8080/TCP”端口；
  - /：根路径，会显示客户端实例IP、服务端实例IP和主机名等信息；
  - /api/get_server：以json格式响应当前服务的服务名称、版本号和实例名称，该路径默认会由fronted/services应用所依赖；
  - /hostname：当前实例的主机名；
  - /user-agent：客户端的Agent名称；



