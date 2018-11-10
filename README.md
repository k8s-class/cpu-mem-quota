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

