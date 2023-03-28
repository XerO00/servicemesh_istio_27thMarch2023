### lab connection 

<img src="lab.png">

### checking istioctl and kubectl working status 

```

[ashu@ip-172-31-32-172 ~]$ kubectl  get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
ip-192-168-17-96.ap-south-1.compute.internal    Ready    <none>   76m   v1.25.6-eks-48e63af
ip-192-168-39-228.ap-south-1.compute.internal   Ready    <none>   76m   v1.25.6-eks-48e63af
ip-192-168-87-0.ap-south-1.compute.internal     Ready    <none>   76m   v1.25.6-eks-48e63af
[ashu@ip-172-31-32-172 ~]$ 
[ashu@ip-172-31-32-172 ~]$ istioctl version 
client version: 1.17.1
control plane version: 1.17.1
data plane version: 1.17.1 (1 proxies)
```

### More info about envoy 

<img src="envoy.png">

### URL to read more about this 

[click_here](https://www.envoyproxy.io/)

## Lets create k8s namespace to deploy our sample app 

```
[ashu@ip-172-31-32-172 ~]$ kubectl  get  ns
NAME              STATUS   AGE
default           Active   102m
istio-system      Active   88m
kube-node-lease   Active   102m
kube-public       Active   102m
kube-system       Active   102m
[ashu@ip-172-31-32-172 ~]$ 
[ashu@ip-172-31-32-172 ~]$ kubectl  create  namespace  ashu-apps 
namespace/ashu-apps created
[ashu@ip-172-31-32-172 ~]$ kubectl  get  ns
NAME              STATUS   AGE
ashu-apps         Active   2s
default           Active   103m
istio-system      Active   89m
kube-node-lease   Active   103m
kube-public       Active   103m
kube-system       Active   103m
[ashu@ip-172-31-32-172 ~]$ kubectl   config set-context --current --namespace=ashu-apps
Context "iam-root-account@basic-cluster.ap-south-1.eksctl.io" modified.
[ashu@ip-172-31-32-172 ~]$ 
[ashu@ip-172-31-32-172 ~]$ kubectl   config get-contexts 
CURRENT   NAME                                                  CLUSTER                              AUTHINFO                                              NAMESPACE
*         iam-root-account@basic-cluster.ap-south-1.eksctl.io   basic-cluster.ap-south-1.eksctl.io   iam-root-account@basic-cluster.ap-south-1.eksctl.io   ashu-apps
[ashu@ip-172-31-32-172 ~]$ kubectl  get pod
No resources found in ashu-apps namespace.
[ashu@ip-172-31-32-172 ~]$ kubectl  get svc
No resources found in ashu-apps namespace.
```

## Testing Istio envoy 

### creating a sample deployment in my namespace

```
kubectl  create  deployment  ashu-webapp --image=nginx --port 80 --replicas=1 --dry-run=client -o yaml  >ashu-deploy.yaml 
===
[ashu@ip-172-31-32-172 ashu-application]$ ls
ashu-deploy.yaml
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  apply -f ashu-deploy.yaml 
deployment.apps/ashu-webapp created
[ashu@ip-172-31-32-172 ashu-application]$ kubectl   get  deploy 
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
ashu-webapp   0/1     1            0           5s
[ashu@ip-172-31-32-172 ashu-application]$ kubectl   get  deploy 
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
ashu-webapp   1/1     1            1           13s
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  rs
NAME                     DESIRED   CURRENT   READY   AGE
ashu-webapp-54d487cf97   1         1         1       18s
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get  po
NAME                           READY   STATUS    RESTARTS   AGE
ashu-webapp-54d487cf97-577fv   1/1     Running   0          21s
[ashu@ip-172-31-32-172 ashu-application]$ 


```

## istio will not be injecting any envoy proxy by default 

### label our target namespaces where we want istio to enject envoy proxy 

<img src="ns1.png">

### assinging label 

```
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  label namespaces ashu-apps  istio-injection=enabled 
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get ns  ashu-apps --show-labels
NAME        STATUS   AGE   LABELS
ashu-apps   Active   30m   istio-injection=enabled,kubernetes.io/metadata.name=ashu-apps
[ashu@ip-172-31-32-172 ashu-application]$ 
```

### redeploy it again 

```
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  delete  -f ashu-deploy.yaml 
deployment.apps "ashu-webapp" deleted
[ashu@ip-172-31-32-172 ashu-application]$ 
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  apply   -f ashu-deploy.yaml 
deployment.apps/ashu-webapp created
[ashu@ip-172-31-32-172 ashu-application]$ kubectl  get deploy 
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
ashu-webapp   1/1     1            1           8s
[ashu@ip-172-31-32-172 ashu-application]$ kubectl   get po
NAME                           READY   STATUS    RESTARTS   AGE
ashu-webapp-54d487cf97-bgjd5   2/2     Running   0          13s
[ashu@ip-172-31-32-172 ashu-application]$ 


```






