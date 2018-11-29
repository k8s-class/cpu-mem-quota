# cpu-mem-quota
### Example on how resource quotas work

```
kubectl create namespace quota-mem-cpu-example
kubectl create -f resource_quota.yaml --namespace=quota-mem-cpu-example
kubectl get resourcequota mem-cpu-demo --namespace=quota-mem-cpu-example --output=yaml
kubectl create -f quota-mem-cpu-pod.yaml --namespace=quota-mem-cpu-example
kubectl get resourcequota mem-cpu-demo --namespace=quota-mem-cpu-example --output=yaml
kubectl get pod quota-mem-cpu-demo --namespace=quota-mem-cpu-example
kubectl create -f quota-mem-cpu-pod-2.yaml --namespace=quota-mem-cpu-example
kubectl delete namespace quota-mem-cpu-example
```
```
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl create namespace quota-mem-cpu-example
namespace/quota-mem-cpu-example created
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl create -f resource_quota.yaml --namespace=quota-mem-cpu-example
resourcequota/mem-cpu-demo created
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ cat resource_quota.yaml 
---
apiVersion: v1
kind: ResourceQuota
metadata:
  name: mem-cpu-demo
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl get resourcequota mem-cpu-demo --namespace=quota-mem-cpu-example --output=yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  creationTimestamp: 2018-11-29T22:06:53Z
  name: mem-cpu-demo
  namespace: quota-mem-cpu-example
  resourceVersion: "28481"
  selfLink: /api/v1/namespaces/quota-mem-cpu-example/resourcequotas/mem-cpu-demo
  uid: 145680f7-f423-11e8-96f9-0a58ac1f08ec
spec:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
status:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
  used:
    limits.cpu: "0"
    limits.memory: "0"
    requests.cpu: "0"
    requests.memory: "0"
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl create -f quota-mem-cpu-pod.yaml --namespace=quota-mem-cpu-example
pod/quota-mem-cpu-demo created
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl get resourcequota mem-cpu-demo --namespace=quota-mem-cpu-example --output=yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  creationTimestamp: 2018-11-29T22:06:53Z
  name: mem-cpu-demo
  namespace: quota-mem-cpu-example
  resourceVersion: "28624"
  selfLink: /api/v1/namespaces/quota-mem-cpu-example/resourcequotas/mem-cpu-demo
  uid: 145680f7-f423-11e8-96f9-0a58ac1f08ec
spec:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
status:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
  used:
    limits.cpu: 800m
    limits.memory: 800Mi
    requests.cpu: 400m
    requests.memory: 600Mi
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl get pod quota-mem-cpu-demo --namespace=quota-mem-cpu-example
NAME                 READY   STATUS    RESTARTS   AGE
quota-mem-cpu-demo   1/1     Running   0          1m
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl create -f quota-mem-cpu-pod-2.yaml --namespace=quota-mem-cpu-example
Error from server (Forbidden): error when creating "quota-mem-cpu-pod-2.yaml": pods "quota-mem-cpu-demo-2" is forbidden: exceeded quota: mem-cpu-demo, requested: requests.memory=700Mi, used: requests.memory=600Mi, limited: requests.memory=1Gi
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ cat quota-mem-cpu-pod-2.yaml 
---
apiVersion: v1
kind: Pod
metadata:
  name: quota-mem-cpu-demo-2
spec:
  containers:
  - name: quota-mem-cpu-demo-2-ctr
    image: redis
    resources:
      limits:
        memory: "1Gi"
        cpu: "800m"
      requests:
        memory: "700Mi"
        cpu: "400m"


[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ vi quota-mem-cpu-pod-2.yaml # Changed it to 399 memory
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl create -f quota-mem-cpu-pod-2.yaml --namespace=quota-mem-cpu-example
pod/quota-mem-cpu-demo-2 created
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl get pod quota-mem-cpu-demo --namespace=quota-mem-cpu-example
NAME                 READY   STATUS    RESTARTS   AGE
quota-mem-cpu-demo   1/1     Running   0          3m
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ kubectl get resourcequota mem-cpu-demo --namespace=quota-mem-cpu-example --output=yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  creationTimestamp: 2018-11-29T22:06:53Z
  name: mem-cpu-demo
  namespace: quota-mem-cpu-example
  resourceVersion: "28998"
  selfLink: /api/v1/namespaces/quota-mem-cpu-example/resourcequotas/mem-cpu-demo
  uid: 145680f7-f423-11e8-96f9-0a58ac1f08ec
spec:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
status:
  hard:
    limits.cpu: "2"
    limits.memory: 2Gi
    requests.cpu: "1"
    requests.memory: 1Gi
  used:
    limits.cpu: 1600m
    limits.memory: 1824Mi
    requests.cpu: 800m
    requests.memory: 999Mi
[user@phatbox cpu-mem-quota (⎈ |Epick8s:default)]$ 
```
