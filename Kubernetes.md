---
title: Kubernetes Commands
category: Devops
layout: 2017/sheet
intro: |
  A quick reference of Kubernetes Commands with Input / Output examples, the ecosystem surround it and beyond.
---

### General Information

```console
𝜆 minikube start
Starting local Kubernetes v1.10.0 cluster...
Starting VM...
Getting VM IP address...
Moving files into cluster...
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Starting cluster components...
Kubectl is now configured to use the cluster.
Loading cached images from config file.

𝜆 kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443
KubeDNS is running at https://192.168.99.100:8443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

𝜆 kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     master    13h       v1.10.0
```

### Dashboard

```console
𝜆 minikube dashboard
Opening kubernetes dashboard in default browser...

𝜆 minikube addons list
- addon-manager: enabled
- coredns: disabled
- dashboard: enabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- heapster: disabled
- ingress: disabled
- kube-dns: enabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
```

## kubectl

### Configuration

```console
𝜆 kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /Users/terrence/.minikube/ca.crt
    server: https://192.168.99.100:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /Users/terrence/.minikube/client.crt
    client-key: /Users/terrence/.minikube/client.key

𝜆 kubectl describe secret
Name:         default-token-wktcc
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name=default
              kubernetes.io/service-account.uid=6614cf82-9afb-11e8-a330-080027bd97be

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4td2t0Y2MiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjY2MTRjZjgyLTlhZmItMTFlOC1hMzMwLTA4MDAyN2JkOTdiZSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.YO31k193K_92H7kdVPjieIGncBhnhOrnC0iZP8QyofejLgtzJ0dJFdtaDKEpGL4tXUC-ol7wSqhUCvBIGRHQT7FmfUXo5rKSV38eDIfeJKfZqC9gSzzY_8uoPNgPrPgo5iEQDAX3_7JAZlxujBF6eRz417T3402irLkK0T3IC0UhqqKDNdul-uZvLOHmgtkxWJlgcP_0zHcnG9YqZMIC3aucf7Us5E8l74JRm7L9u_GmQFd6i8mR0eHDn3MAtn8ZuKjQffqSc4uHmMetHGhic5dFre_7KOaCcurhKuZaYxF6E-X6CR2-gS0b9JhaTDLjpNJCPVV4SOeiJuYzp5kxLw
ca.crt:     1066 bytes
namespace:  7 bytes
```

### Proxy

```console
𝜆 kubectl proxy --port=8080 &
Starting to serve on 127.0.0.1:8080

𝜆 curl http://localhost:8080/api/
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "192.168.99.100:8443"
    }
  ]
}
```

NOTE: can **ONLY** bind localhost IP (127.0.0.1)

### Port Forwarding

```console
𝜆 kubectl port-forward svc/elasticsearch 9200:9200
Forwarding from 127.0.0.1:9200 -> 9200
Forwarding from [::1]:9200 -> 9200
```

NOTE: can **ONLY** bind localhost IP (127.0.0.1)

### Expose Service

```console
𝜆 kubectl expose deployment elasticsearch --type=LoadBalancer
service/elasticsearch exposed
```

### Deployment

```console
𝜆 kubectl create -f kibana-deployment.yaml
deployment.apps/kibana created
```

### Stateful Sets

```console
𝜆 kubectl create -f elasticsearch-stateful-sets.yaml
statefulset.apps/elasticsearch created

𝜆 kubectl get statefulset elasticsearch
NAME                 DESIRED   CURRENT   AGE
elasticsearch        1         1         52s
```

### Services

```console
𝜆 kubectl create -f elasticsearch-service.yaml
service/elasticsearch created

𝜆 kubectl get service elasticsearch
NAME                 TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
elasticsearch        NodePort   10.104.152.51   10.0.0.100    9200:30920/TCP,9300:30930/TCP   18m

𝜆 kubectl get service --all-namespaces
NAMESPACE     NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
default       elasticsearch          LoadBalancer   10.104.91.150   10.0.0.100    9200:31383/TCP,9300:30063/TCP   7m
default       kubernetes             ClusterIP      10.96.0.1       <none>        443/TCP                         3d
kube-system   kube-dns               ClusterIP      10.96.0.10      <none>        53/UDP,53/TCP                   3d
kube-system   kubernetes-dashboard   NodePort       10.108.138.47   <none>        80:30000/TCP                    3d
```

### Quick Start example

Take ElasticSearch into Kubernetes action:

```console
𝜆 kubectl run elasticsearch --image=docker.elastic.co/elasticsearch/elasticsearch:6.3.2 --env="discovery.type=single-node" --port=9200
deployment.apps/elasticsearch created

𝜆 kubectl get deployments
NAME            DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
elasticsearch   1         1         1            1           10m

𝜆 kubectl get pods
NAME                             READY     STATUS    RESTARTS   AGE
elasticsearch-5d484fdbbd-6nq2n   1/1       Running   0          12m

𝜆 kubectl logs elasticsearch-5d484fdbbd-6nq2n
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 ...
[2018-08-08T23:30:14,398][INFO ][o.e.n.Node               ] [] initializing ...

...

[2018-08-08T23:30:34,986][INFO ][o.e.l.LicenseService     ] [KnDA6iW] license [XXX] mode [basic] - valid
```

## minikube

### Host

```console
𝜆 minikube ssh
                         _             _
            _         _ ( )           ( )
  ___ ___  (_)  ___  (_)| |/')  _   _ | |_      __
/' _ ` _ `\| |/' _ `\| || , <  ( ) ( )| '_`\  /'__`\
| ( ) ( ) || || ( ) || || |\`\ | (_) || |_) )(  ___/
(_) (_) (_)(_)(_) (_)(_)(_) (_)`\___/'(_,__/'`\____)
```

### Services

```console
𝜆 minikube service list
|-------------|----------------------|--------------------------------|
|  NAMESPACE  |         NAME         |              URL               |
|-------------|----------------------|--------------------------------|
| default     | elasticsearch        | http://192.168.99.100:30920    |
|             |                      | http://192.168.99.100:30930    |
| default     | kibana               | http://192.168.99.100:30560    |
| default     | kubernetes           | No node port                   |
| kube-system | kube-dns             | No node port                   |
| kube-system | kubernetes-dashboard | http://192.168.99.100:30000    |
|-------------|----------------------|--------------------------------| 

𝜆 minikube service elasticsearch
Opening kubernetes service default/elasticsearch in default browser...
```

Default browser open URL e.g. _http://192.168.99.100:30920/_ with response:

```javascript
{
  "name" : "KnDA6iW",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "Mk_t_ATpSE-DxLTgD6b-4g",
  "version" : {
    "number" : "6.3.2",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "053779d",
    "build_date" : "2018-07-20T05:20:23.451332Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

### Kubernetes in Action

![ElasticSearch in Kubernetes](https://raw.githubusercontent.com/TerrenceMiao/ReactJS/master/auto-suggest/auto-suggest%20in%20Kubernetes.png "ElasticSearch in Kubernetes")


## References

* Kubernetes Commands, <https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands>
