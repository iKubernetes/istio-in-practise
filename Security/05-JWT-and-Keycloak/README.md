# Istio and Keycloak

### Deploy oauth2-proxy

Deploy
```bash
kubectl apply -f 01-deploy-oauth2.yaml
```
Verify
```bash
kubectl get pods -n oauth2-proxy
```

### Set new provider

```bash
istioctl apply -f 02-istio-operator-update.yaml
```

### set Ingress Gateway ext authz

Access Kiali and Prometheus 

```bash
kubectl apply -f 03-ext-auth-ingress-gateway.yaml
```
