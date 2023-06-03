# Install Kubernetes
This article guide you to install kubernetes in Ubuntu VM

## Docker Installation
Click [HERE](./docker%20install.md) to go for docker installation

## Disable swap
```
swapon --show
swapoff -a
swapon --show
```

## Update pkg index and install required packages
```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

## Download the Google Cloud public signing key
```
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
```

## Add the Kubernetes apt repository
```
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

## Install kubeadm, kubelet and kubectl
```
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## Configure cgroup driver
1. Change the cgroup driver to systemd using docker configuration file
vi /lib/systemd/system/docker.service <br />
Modify this line <br />
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock <br />
To<br />
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --exec-opt native.cgroupdriver=systemd<br />

2. Restart the Docker service by running the following command
```
docker info |grep -i cgroup
sudo systemctl status docker
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo systemctl status docker
sudo docker info |grep -i cgroup
```

3. create a kubeadm-config.yaml with below contents
```
kind: ClusterConfiguration
apiVersion: kubeadm.k8s.io/v1beta3
kubernetesVersion: v1.21.0
networking:
  podSubnet: 10.244.0.0/16
---
kind: KubeletConfiguration
apiVersion: kubelet.config.k8s.io/v1beta1
cgroupDriver: systemd
```
**Note:** you need to replace kubernetesVersion parameter with the same version of kubeadm tool<br />
Run below command to get kubeadm version
```
kubeadm version -o short
```

4. Remove config.toml file and restart the containerd
```
sudo rm /etc/containerd/config.toml
sudo systemctl restart containerd
```

## Install Kubernetes
1. install kubernetes
```
kubeadm init --config kubeadm-config.yaml
```

2. Configure kube config
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Install pod network – Flannel
Refer: https://github.com/flannel-io/flannel#flannel

```
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
kubectl get no
```

## Reference
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
