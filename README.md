# runai

Set variables:
```bash
NGC_API_KEY=nvapi-xxxxxxxxx
RUNAI_DOMAIN=runai.k8s.shubhamtatvamasi.com
RUNAI_GATEWAY_NAME=traefik-gateway
RUNAI_GATEWAY_NAMESPACE=traefik
RUNAI_ADMIN=admin@shubhamtatvamasi.com
STRONG_ADMIN_PASSWORD=$(openssl rand -base64 48 | tr -dc 'A-Za-z0-9!@#$%^&*_-+=?' | head -c 20)
```

Add helm repo:
```bash
helm repo add runai https://helm.ngc.nvidia.com/nvidia/runai \
  --username='$oauthtoken' \
  --password="$NGC_API_KEY"
```

Install runai control-plane:
```bash
helm upgrade -i runai-backend runai/control-plane \
  --namespace runai-backend \
  --create-namespace \
  --set global.domain="$RUNAI_DOMAIN" \
  --set global.ingress.enabled=false \
  --set global.gatewayAPI.enabled=true \
  --set "global.gatewayAPI.parentRefs[0].name"="$RUNAI_GATEWAY_NAME" \
  --set "global.gatewayAPI.parentRefs[0].namespace"="$RUNAI_GATEWAY_NAMESPACE" \
  --set "global.gatewayAPI.parentRefs[0].sectionName"=https \
  --set tenantsManager.config.adminUsername="$RUNAI_ADMIN" \
  --set tenantsManager.config.adminPassword="$STRONG_ADMIN_PASSWORD"
```

---

Install runai cluster:
```bash
helm upgrade -i runai-cluster runai/runai-cluster \
  --namespace runai \
  --create-namespace \
  --set controlPlane.url=$RUNAI_DOMAIN \
  --set controlPlane.clientSecret=xxxxxxx \
  --set cluster.uid=xxxxxxx \
  --set cluster.url=https://$RUNAI_DOMAIN \
  --set clusterConfig.global.ingress.enabled=false \
  --set clusterConfig.global.gatewayAPI.enabled=true \
  --set clusterConfig.global.gatewayAPI.name="$RUNAI_GATEWAY_NAME" \
  --set clusterConfig.global.gatewayAPI.namespace="$RUNAI_GATEWAY_NAMESPACE" \
  --set global.fipsMode=off
```



