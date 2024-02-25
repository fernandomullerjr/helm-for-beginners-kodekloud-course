
# ###################################################################################################################### 
# ###################################################################################################################### 
#  push

git status
git add .
git commit -m " lab-installing-helm."
git push
git status



# ###################################################################################################################### 
# ###################################################################################################################### 
## lab-installing-helm

You must have Kubernetes installed for a successful and properly secured use of Helm.

Refer the documentation to check the prerequisites. The Documentation tab is available at the top right panel.



- Erro ao tentar subir k8s

~~~~bash
fernando@debian10x64:~$ sudo kubeadm init
[sudo] password for fernando:
I0225 11:49:00.124421   22449 version.go:256] remote version is much newer: v1.29.2; falling back to: stable-1.28
[init] Using Kubernetes version: v1.28.7
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: missing optional cgroups: hugetlb
error execution phase preflight: [preflight] Some fatal errors occurred:
        [ERROR Port-10259]: Port 10259 is in use
        [ERROR Port-10257]: Port 10257 is in use
        [ERROR FileAvailable--etc-kubernetes-manifests-kube-apiserver.yaml]: /etc/kubernetes/manifests/kube-apiserver.yaml already exists
        [ERROR FileAvailable--etc-kubernetes-manifests-kube-controller-manager.yaml]: /etc/kubernetes/manifests/kube-controller-manager.yaml already exists
        [ERROR FileAvailable--etc-kubernetes-manifests-kube-scheduler.yaml]: /etc/kubernetes/manifests/kube-scheduler.yaml already exists
        [ERROR FileAvailable--etc-kubernetes-manifests-etcd.yaml]: /etc/kubernetes/manifests/etcd.yaml already exists
        [ERROR Port-10250]: Port 10250 is in use
        [ERROR DirAvailable--var-lib-etcd]: /var/lib/etcd is not empty
[preflight] If you know what you are doing, you can make a check non-fatal with `--ignore-preflight-errors=...`
To see the stack trace of this error execute with --v=5 or higher
fernando@debian10x64:~$
fernando@debian10x64:~$


fernando@debian10x64:~$ crictl ps
WARN[0000] runtime connect using default endpoints: [unix:///var/run/dockershim.sock unix:///run/containerd/containerd.sock unix:///run/crio/crio.sock unix:///var/run/cri-dockerd.sock]. As the default settings are now deprecated, you should set the endpoint instead.
WARN[0000] image connect using default endpoints: [unix:///var/run/dockershim.sock unix:///run/containerd/containerd.sock unix:///run/crio/crio.sock unix:///var/run/cri-dockerd.sock]. As the default settings are now deprecated, you should set the endpoint instead.
E0225 12:09:57.510390   29700 remote_runtime.go:390] "ListContainers with filter from runtime service failed" err="rpc error: code = Unavailable desc = connection error: desc = \"transport: Error while dialing dial unix /var/run/dockershim.sock: connect: no such file or directory\"" filter="&ContainerFilter{Id:,State:&ContainerStateValue{State:CONTAINER_RUNNING,},PodSandboxId:,LabelSelector:map[string]string{},}"
FATA[0000] listing containers: rpc error: code = Unavailable desc = connection error: desc = "transport: Error while dialing dial unix /var/run/dockershim.sock: connect: no such file or directory"
fernando@debian10x64:~$
~~~~


## PENDENTE
- Revisar instalação do k8s local
- Validar instalação do Helm



- Tentar remover Containers via crictl manualmente

ajustando o crictl
crictl config runtime-endpoint unix:///var/run/containerd/containerd.sock

~~~~bash
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# crictl config runtime-endpoint unix:///var/run/containerd/containerd.sock
root@debian10x64:/home/fernando# crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID              POD
root@debian10x64:/home/fernando#
~~~~


- Erro no kubeadm reset, mesmo sem ter container rodando no containerd:

~~~~bash

root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# crictl config runtime-endpoint unix:///var/run/containerd/containerd.sock
root@debian10x64:/home/fernando# crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID              POD
root@debian10x64:/home/fernando# kubeadm reset
W0225 12:21:37.403754   31837 preflight.go:56] [reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0225 12:21:40.484870   31837 removeetcdmember.go:106] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
W0225 12:22:00.703506   31837 cleanupnode.go:99] [reset] Failed to remove containers: [failed to stop running pod e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0: output: E0225 12:21:50.601362   31996 remote_runtime.go:205] "StopPodSandbox from runtime service failed" err="rpc error: code = DeadlineExceeded desc = context deadline exceeded" podSandboxID="e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0"
time="2024-02-25T12:21:50-03:00" level=fatal msg="stopping the pod sandbox \"e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0\": rpc error: code = DeadlineExceeded desc = context deadline exceeded"
: exit status 1, failed to stop running pod ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b: output: E0225 12:22:00.702598   32131 remote_runtime.go:205] "StopPodSandbox from runtime service failed" err="rpc error: code = DeadlineExceeded desc = context deadline exceeded" podSandboxID="ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b"
time="2024-02-25T12:22:00-03:00" level=fatal msg="stopping the pod sandbox \"ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b\": rpc error: code = DeadlineExceeded desc = context deadline exceeded"
: exit status 1]
[reset] Deleting contents of directories: [/etc/kubernetes/manifests /var/lib/kubelet /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID              POD
root@debian10x64:/home/fernando# crictl ps -a
CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID              POD

~~~~