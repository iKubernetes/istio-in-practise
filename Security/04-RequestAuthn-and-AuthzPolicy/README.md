# Istio安全策略示例

Istio同安全相关的CRD主要有PeerAuthentication、RequestAuthentication和AuthoricationPolicy三个。

### PeerAuthentication

> PeerAuthentication设置的策略，要同DestinationRule上的tls策略保持一致，否则可能导致认证失败。

PeerAuthentication认证成功后，会自动提取“身份”，并能够用于随后的鉴权策略（AuthorizationPolicy CRD）中，其相关的身份信息为“SPIFFE ID”，这通常是Pod上的Service Account，且表示格式为“Domain.LTD/namespace/NAMESPACE/sa/SERVICE_ACCOUNT”，例如“cluster.local/namespace/default/sa/default”。

### RequestAuthentication

RequestAuthentication通常会依赖于一个可用的Token Issuer，例如Keycloak等。
