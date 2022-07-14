# azure-vote-helm-chart
A demo helm chart

Read more: [Helm 3 - CI/CD with Azure DevOps using Azure Container Registry (ACR) and Azure Kubernetes Service (AKS)](https://www.visualstudiogeeks.com/devops/helm/deploying-helm-chart-with-azdo)

## ejercicio
para la aplicacion de este ejercicio se aplica un service y un ingresss
>
se instala el nginx-controller
>
```bash
NAMESPACE=ingress-basic

helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --create-namespace \
  --namespace $NAMESPACE \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz
```
>
## ejercicio 2
para usar el app gateway manejado por el aks 
se sigue el link
https://docs.microsoft.com/en-us/azure/aks/ingress-basic?tabs=azure-cli
ejemplo 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          service:
            name: aspnetapp
            port:
              number: 80
        pathType: Exact
```

el cambio real entre las dos opciones en el ingress classs

```

k get ingressclass

NAME                        CONTROLLER                  PARAMETERS   AGE
azure-application-gateway   azure/application-gateway   <none>       66m
nginx                       k8s.io/ingress-nginx        <none>       11m
```