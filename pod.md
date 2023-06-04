# Pod

## Create pod using kubectl
```
kubectl run mypod --image=nginx
```

## Create pod using yaml file
1. create nginx-pod.yaml file
```
# To create nginx pod
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: nginx-pod
      image: nginx
```
2. Run below command
```
kubectl create -f nginx-pod.yaml
```

## Login to pod
```
kubectl exec -it nginx -- /bin/bash
```

## Delete pod
```
kubectl delete pod nginx
```
