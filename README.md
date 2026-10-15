# runai

Set variables:
```bash
NGC_API_KEY=nvapi-xxxxxxxxx
RUNAI_DOMAIN=runai.k8s.shubhamtatvamasi.com
RUNAI_GATEWAY_NAME=traefik-gateway
RUNAI_GATEWAY_NAMESPACE=traefik
RUNAI_ADMIN=admin@shubhamtatvamasi.com
STRONG_ADMIN_PASSWORD=$(openssl rand -base64 48 | tr -dc 'A-Za-z0-9' | head -c 32)
```

Add helm repo:
```bash
helm repo add runai https://helm.ngc.nvidia.com/nvidia/runai \
  --username='$oauthtoken' \
  --password="$NGC_API_KEY"
```

Install runai:
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

