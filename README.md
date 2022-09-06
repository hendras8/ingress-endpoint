# ingress-endpoint

## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_kustomize"></a> [kustomize](#requirement\_kustomize | ~> v4.5.4 |


## Provision Instructions

```
 kustomize build .                          #--> check manifest to make sure the manifest is correct
 

apiVersion: v1
kind: Namespace
metadata:
  name: ingress-pool
---
apiVersion: v1
kind: Endpoints
metadata:
  name: sonar-svc
  namespace: ingress-pool
subsets:
- addresses:
  - ip: 192.168.1.2
  ports:
  - port: 8080
    protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: sonar-svc
  namespace: ingress-pool
spec:
  ports:
  - port: 443
    protocol: TCP
    targetPort: 8080
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    ingress.kubernetes.io/ssl-redirect: "true"
    kubernetes.io/ingress.class: nginx-external
    nginx.ingress.kubernetes.io/backend-protocol: HTTP
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    nginx.ingress.kubernetes.io/whitelist-source-range: 192.0.0.0/16 
  name: sonar-ing
  namespace: ingress-pool
spec:
  rules:
  - host: sonar.dodol.com
    http:
      paths:
      - backend:
          service:
            name: sonar-svc
            port:
              number: 443
        path: /
        pathType: ImplementationSpecific


 kustomize build . | kubectl apply -f -     #--> for apply manifest
 
```
