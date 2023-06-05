# Deployment

## Create deployment
1. Create deployment.yaml to create deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: mypod
  template:
    metadata:
      name: mypod-demo
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-pod
        image: nginx
```

2. Create deployment and verify
```
root@kube-master-1:/home/saran# kubectl create -f deployment.yml
deployment.apps/nginx-deployment created
root@kube-master-1:/home/saran# kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           15s
root@kube-master-1:/home/saran# kubectl get po
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-94848b9d7-79qqn   1/1     Running   0          18s
nginx-deployment-94848b9d7-kl4vz   1/1     Running   0          18s
nginx-deployment-94848b9d7-wplzm   1/1     Running   0          18s
root@kube-master-1:/home/saran#
```