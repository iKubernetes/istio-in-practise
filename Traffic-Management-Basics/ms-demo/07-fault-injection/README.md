# Fault Injection

本示例用于测试Istio的故障注入功能。

### 注入延迟

测试命令：

```bash
$ while true; do curl frontend/services; sleep 0.$RANDOM; done
```

命令结果可显示整个请求的耗时时长，如下所示的结果中，超过5000毫秒的响应，正是因为注入的延迟故障所致。

```
frontend (frontend-7c55c868cf-srbzr) successfully connects to demoapp-v2.1 (demoapp-68575f5545-cx6nl) in 7.88 ms.
frontend (frontend-7c55c868cf-jtbf4) successfully connects to demoapp-v2.1 (demoapp-68575f5545-n6nfr) in 6.64 ms.
frontend (frontend-7c55c868cf-jtbf4) successfully connects to demoapp-v2.1 (demoapp-68575f5545-cx6nl) in 5.73 ms.
frontend (frontend-7c55c868cf-srbzr) successfully connects to demoapp-v2.1 (demoapp-68575f5545-n6nfr) in 5004.68 ms.
frontend (frontend-7c55c868cf-srbzr) successfully connects to demoapp-v2.1 (demoapp-68575f5545-n6nfr) in 5.24 ms.
frontend (frontend-7c55c868cf-jtbf4) successfully connects to demoapp-v2.1 (demoapp-68575f5545-n6nfr) in 5007.77 ms.
frontend (frontend-7c55c868cf-jtbf4) successfully connects to demoapp-v2.1 (demoapp-68575f5545-n6nfr) in 4.94 ms.
frontend (frontend-7c55c868cf-jtbf4) successfully connects to demoapp-v2.1 (demoapp-68575f5545-9w5vq) in 5.82 ms. 
……
```

### 注入中断

测试命令：

```bash
while true; do CONTENT=$(curl -s -H "x-canary: true" frontend); echo $CONTENT; sleep 0.$RANDOM; done 
```

命令结果可显示整个请求的耗时时长，如下所示的结果中，结果中显示的“fault filter abort”，正是因为注入的中断故障所致。

```
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.2.82 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.1.240 ~
fault filter abort
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.1.240 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.1.240 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.3.130 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.1.240 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.3.130 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.2.82 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name……, Server IP: 10.244.1.240 ~
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name……, Server IP: 10.244.2.82 ~
fault filter abort
Demoapp by iKubernetes! App Version: v2.1-canary, Client IP: 127.0.0.6, Server Name: ……, Server IP: 10.244.2.82 ~
 ……
```

