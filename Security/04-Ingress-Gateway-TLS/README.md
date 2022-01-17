# Ingress Gateway TLS

## Grafana

### Certificate and Private Key 

Create CA Key and Certificate
```bash
mkdir certs/ && cd certs/
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -subj '/O=MageEdu Inc./CN=magedu.com' -keyout magedu.com.key -out magedu.com.crt
```

Create key and CSR
```bash
openssl req -out grafana.magedu.com.csr -newkey rsa:2048 -nodes -keyout grafana.magedu.com.key -subj "/CN=grafana.magedu.com/O=grafana organization"
openssl x509 -req -days 365 -CA magedu.com.crt -CAkey magedu.com.key -set_serial 0 -in grafana.magedu.com.csr -out grafana.magedu.com.crt
```

Create Secret
```bash
kubectl create -n istio-system secret tls grafana-credential --key=grafana.magedu.com.key --cert=grafana.magedu.com.crt
```

### Gateway
Gateway Resrouce

```
---
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: grafana-gateway
  namespace: istio-system
spec:
  selector:
    app: istio-ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "grafana.magedu.com"
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: grafana-credential
    hosts:
    - "grafana.magedu.com"
---
```


## Kiali

### Certificate and Private Key 

Create key and CSR
```bash
openssl req -out kiali.magedu.com.csr -newkey rsa:2048 -nodes -keyout kiali.magedu.com.key -subj "/CN=kiali.magedu.com/O=kiali organization"
openssl x509 -req -days 365 -CA magedu.com.crt -CAkey magedu.com.key -set_serial 0 -in kiali.magedu.com.csr -out kiali.magedu.com.crt
```

Create Secret
```bash
kubectl create -n istio-system secret tls kiali-credential --key=kiali.magedu.com.key --cert=kiali.magedu.com.crt
```

### Gateway
Gateway Resrouce

```
---
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: kiali-gateway
  namespace: istio-system
spec:
  selector:
    app: istio-ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "kiali.magedu.com"
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      credentialName: kiali-credential
    hosts:
    - "kiali.magedu.com"
---
```
