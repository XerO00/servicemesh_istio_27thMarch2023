### good bye to monolit with no of problems we had 

<img src="prob.png">

### introduction to microservices

<img src="micro.png">

## problems with container based deployment using microservices models 

### how we deploy 3 tier micro services in k8s api resources 

<img src="deploy.png">

### Istio will be injecting an init-container as sidecar pattern in our k8s app deployment pod 

<img src="istio.png">

## Architecture and working of Istio -- 

<img src="ist1.png">

### Istio setup in k8s cluster 

<img src="setup.png">

### to setup istio using any given method -- we need to have k8s client machine configured 

```
[ashu@ip-172-31-32-172 ~]$ kubectl  version --client  -o yaml 
clientVersion:
  buildDate: "2023-03-15T13:39:54Z"
  compiler: gc
  gitCommit: 0ce7342c984110dfc93657d64df5dc3b2c0d1fe9
  gitTreeState: clean
  gitVersion: v1.25.8
  goVersion: go1.19.7
  major: "1"
  minor: "25"
  platform: linux/amd64
kustomizeVersion: v4.5.7

[ashu@ip-172-31-32-172 ~]$ 
[ashu@ip-172-31-32-172 ~]$ kubectl  get  nodes
The connection to the server localhost:8080 was refused - did you specify the right host or port?
[ashu@ip-172-31-32-172 ~]$ 

```

### From k8s client machine -- installing istio in k8s cluster -- using istioCTL 

### Download istio package 

```
[root@ip-172-31-32-172 opt]# curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.17.1 TARGET_ARCH=x86_64 sh -
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   102  100   102    0     0    344      0 --:--:-- --:--:-- --:--:--   345
100  4856  100  4856    0     0   8651      0 --:--:-- --:--:-- --:--:--  8651

Downloading istio-1.17.1 from https://github.com/istio/istio/releases/download/1.17.1/istio-1.17.1-linux-amd64.tar.gz ...

Istio 1.17.1 Download Complete!

Istio has been successfully downloaded into the istio-1.17.1 folder on your system.

Next Steps:
See https://istio.io/latest/docs/setup/install/ to add Istio to your Kubernetes cluster.

To configure the istioctl client tool for your workstation,
add the /opt/istio-1.17.1/bin directory to your environment path variable with:
	 export PATH="$PATH:/opt/istio-1.17.1/bin"

Begin the Istio pre-installation check by running:
	 istioctl x precheck 

Need more information? Visit https://istio.io/latest/docs/setup/install/ 
[root@ip-172-31-32-172 opt]# ls
aws  istio-1.17.1

```

### setting linux path variable to get istioctl 

```
[root@ip-172-31-32-172 opt]# ls
aws  istio-1.17.1
[root@ip-172-31-32-172 opt]# 
[root@ip-172-31-32-172 opt]# cd istio-1.17.1/
[root@ip-172-31-32-172 istio-1.17.1]# ls
LICENSE  README.md  bin  manifest.yaml  manifests  samples  tools
[root@ip-172-31-32-172 istio-1.17.1]# pwd
/opt/istio-1.17.1
[root@ip-172-31-32-172 istio-1.17.1]# vim ~/.bashrc 
[root@ip-172-31-32-172 istio-1.17.1]# source ~/.bashrc 
[root@ip-172-31-32-172 istio-1.17.1]# 
[root@ip-172-31-32-172 istio-1.17.1]# istioctl  version 
no running Istio pods in "istio-system"
1.17.1

```

### Install 

```
[root@ip-172-31-32-172 ~]# istioctl  install 
This will install the Istio 1.17.1 default profile with ["Istio core" "Istiod" "Ingress gateways"] components into the cluster. Proceed? (y/N) y
✔ Istio core installed                                                                                                                    
✔ Istiod installed                                                                                                                        
✔ Ingress gateways installed                                                                                                              
✔ Installation complete                                                                                                                   Making this installation the default for injection and validation.

Thank you for installing Istio 1.17.  Please take a few minutes to tell us about your install/upgrade experience!  https://forms.gle/hMHGiwZHPU7UQRWe9

```





