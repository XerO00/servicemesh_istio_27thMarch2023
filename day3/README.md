## Istio Revision 

## creating namespace and setting it as default 

```
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  create  namespace  ashu-webapp
namespace/ashu-webapp created
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get ns
kNAME              STATUS   AGE
ashu-webapp       Active   3s
default           Active   3h33m
istio-system      Active   3h17m
kube-node-lease   Active   3h33m
kube-public       Active   3h33m
kube-system       Active   3h33m
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  config set-context --current --namespace ashu-webapp
Context "iam-root-account@basic-cluster.ap-south-1.eksctl.io" modified.
[ashu@ip-172-31-32-172 ashu-application]$ 
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  po
No resources found in ashu-webapp namespace.
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  svc
No resources found in ashu-webapp namespace.
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  vs
No resources found in ashu-webapp namespace.
```

### taking sample micro service webapp with UI / backend / db 

<img src="micro.png">

### deploy book info micro service to your personal namespace 

```
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  apply -f micro-service/bookinfo.yaml 
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
```

### Verify and redeploy it 

```
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  label namespaces ashu-webapp  istio-injection=enabled
namespace/ashu-webapp labeled
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get ns ashu-webapp --show-labels 
NAME          STATUS   AGE   LABELS
ashu-webapp   Active   16m   istio-injection=enabled,kubernetes.io/metadata.name=ashu-webapp
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  apply -f micro-service/bookinfo.yaml 
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get po
NAME                             READY   STATUS            RESTARTS   AGE
details-v1-5ffd6b64f7-6qdbh      2/2     Running           0          8s
productpage-v1-979d4d9fc-dkvh9   0/2     PodInitializing   0          7s
ratings-v1-5f9699cfdf-kzlvn      2/2     Running           0          8s
reviews-v1-569db879f5-jzt4s      2/2     Running           0          8s
reviews-v2-65c4dc6fdc-8dn22      2/2     Running           0          7s
reviews-v3-c9c4fb987-gd74h       2/2     Running           0          7s
[ashu@ip-172-31-32-172 ashu-application]$ 
```

### Expose webapp outside k8s cluster 

### creating istio-gateway CRD 

```
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: ashu-micro-app-gw # name of my istio gateway to handle network filter
  namespace: ashu-webapp 
spec:
  selector: # for selecting the ingress controller out of nginx , istio , kong , ha proxy 
    istio: ingressgateway 
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - me.ashutoshh.in
```

### lets deploy it 

```
[ashu@ip-172-31-32-172 ashu-application]$ ls
ashu-deploy.yaml  ashu_istio_gateway.yaml  ashu_virtual_svc.yaml  micro-service  ui_svc.yaml
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  apply -f micro-service/ashu-istiogateway.yaml 
gateway.networking.istio.io/ashu-micro-app-gw created
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  gw 
NAME                AGE
ashu-micro-app-gw   3s
```

### Understanding traffic routing by virtual service -- redirect to home page micro service

<img src="vsu.png">


### creating virtual service only for product micro service component 

```
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ashu-vs-routing-rule # name of routing rule
  namespace: ashu-webapp  # namespace info 
spec:
  gateways: # name of gateway 
  - ashu-micro-app-gw
  hosts:
  - me.ashutoshh.in
  http:
  - match: # figure out redirection urls /login
    - uri:
        exact: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri: 
        prefix: /api/v1/products
    route:  # redirect to below service 
    - destination:
        host: productpage # service name of frontpage micro service 
        port:
          number: 9080
```

### depoy it 

```
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  apply -f micro-service/ashu-home-vs.yaml 
virtualservice.networking.istio.io/ashu-vs-routing-rule created
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get gw
NAME                AGE
ashu-micro-app-gw   76m
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  vs
NAME                   GATEWAYS                HOSTS                 AGE
ashu-vs-routing-rule   ["ashu-micro-app-gw"]   ["me.ashutoshh.in"]   7s
[ashu@ip-172-31-32-172 ashu-application]$ 

```


