# Установка ingress-nginx с разными параметрами

## Main ingress with static ip.
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.service.loadBalancerIP=<YOUR_STATIC_IP> \
--set controller.service.externalTrafficPolicy="Cluster" \
--set controller.replicaCount=1 \
--set controller.service.sessionAffinity="None" 
```

## Main ingress without static ip.
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.service.externalTrafficPolicy="Cluster" \
--set controller.replicaCount=1 \
--set controller.service.sessionAffinity="None"

helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.service.externalTrafficPolicy="Local" \
--set controller.replicaCount=1

helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.service.externalTrafficPolicy="Cluster" \
--set controller.replicaCount=1
```

## Enable proxy protocol.
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.publishService.enabled=true \
--set-string controller.config.use-forward-headers=true,controller.config.compute-full-forward-for=true,controller.config.use-proxy-protocol=true \
 --set controller.service.annotations."service\.beta\.kubernetes\.io/do-loadbalancer-enable-proxy-protocol=true"
```

## Simple ingress controller with static address.
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.service.loadBalancerIP=<YOUR_STATIC_IP> \
--set controller.ingressClass="nginx"
```
---------------------------------------------------------------------------------------------------------------------------------------------------------

## Ingress controller with internal LB.
Файл values.yaml.
```
controller:
  service:
    external:
      enabled: false
    internal:
      enabled: true
      annotations:
        yandex.cloud/load-balancer-type: internal
        yandex.cloud/subnet-id: <subnet_id>
        yandex.cloud/subnet-id: <subnet_id>
```

### Установка.
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.service.externalTrafficPolicy="Cluster" \
--set controller.replicaCount=1 \
-f values.yaml
```
---------------------------------------------------------------------------------------------------------------------------------------------------------

## Ingress controller with internal LB and Pod Topolgy Spread Constraint.
```
controller:
  service:
    external:
      enabled: false
    internal:
      enabled: true
      annotations:
        yandex.cloud/load-balancer-type: internal
        yandex.cloud/subnet-id: <subnet_id>
        yandex.cloud/subnet-id: <subnet_id>
  topologySpreadConstraints:
    - maxSkew: 1
      topologyKey: topology.kubernetes.io/zone
      whenUnsatisfiable: ScheduleAnyway
    - maxSkew: 1
      topologyKey: kubernetes.io/hostname
      whenUnsatisfiable: ScheduleAnyway
```

### Установка.
```
helm upgrade --install ingress-nginx ingress-nginx \
--repo https://kubernetes.github.io/ingress-nginx \
--namespace ingress-nginx --create-namespace \
--debug \
--set controller.ingressClass="nginx" \
--set controller.ingressClassResource.name="nginx" \
--set controller.ingressClassResource.enabled=true \
--set controller.ingressClassByName=true \
--set controller.publishService.enabled=true \
--set controller.admissionWebhooks.enabled=false \
--set controller.service.externalTrafficPolicy="Cluster" \
--set controller.replicaCount=3 \
-f values.yaml
```
