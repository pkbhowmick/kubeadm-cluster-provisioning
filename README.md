# kubeadm-cluster-provisioning

## Provision kubernetes cluster using kubeadm in local virtual machine(**Without cloud provider**)

#### Run virtual machine locally and ssh into it.
- Install oracle virtual box and run ubuntu server 22.04 LTS. [Installation guide.](https://hibbard.eu/install-ubuntu-virtual-box/)

- Recommended vm system requirements:
    - CPU >= 2 cores
    - Memory >= 2 GB
    - Storage >= 10GB

#### Provision k8s cluster (Single node)
- Permanently disable swap. 

```bash
swapoff -a; sed -i '/swap/d' /etc/fstab
```

- Install container runtime (containerd)
    - Install [containerd] (https://github.com/containerd/containerd/blob/main/docs/getting-started.md)
    - Install [runc](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#step-2-installing-runc)
    - Install [cni-plugins](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#step-3-installing-cni-plugins)
    - Generate config.toml file for containerd. [[Guide]](https://github.com/containerd/containerd/blob/main/docs/getting-started.md#customizing-containerd)
    - Configure systemd cgroup driver. [[Guide]](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#containerd-systemd)
- Install kubeadm, kubelet, kubectl. [[Guide]](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)

- Bootstarp cluster. [[Guide]](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
```bash
 kubeadm init --pod-network-cidr=192.168.0.0/16 --ignore-preflight-errors=all
```

- Install [Calico](https://projectcalico.docs.tigera.io/getting-started/kubernetes/quickstart) for Pod network add on.

- Untainted Node to run pods in Control plane. [[Ref]](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#control-plane-node-isolation)
```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane- node-role.kubernetes.io/master-
```

- Use cluster
```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Alternatively, if you are the root user, you can run:
```bash
export KUBECONFIG=/etc/kubernetes/admin.conf
```

- Check the pods.
```bash
kubectl get pods -A
```

### Acknowledgement
A big thanks to [Emruz Hossain](https://github.com/hossainemruz) bhai for the k8s provision workflow contents.
