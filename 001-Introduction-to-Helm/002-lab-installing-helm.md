
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

- Erro no crictl
solução:
<https://github.com/kubernetes-sigs/cri-tools/issues/153>
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






- Pods


root@debian10x64:/home/fernando# crictl pods
POD ID              CREATED             STATE               NAME                       NAMESPACE           ATTEMPT             RUNTIME
e41bd4d079860       2 months ago        NotReady            coredns-5dd5756b68-sr9rq   kube-system         0                   (default)
ad45028c9f6a5       2 months ago        NotReady            coredns-5dd5756b68-629mj   kube-system         0                   (default)
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# crictl pods -h
NAME:
   crictl pods - List pods

USAGE:
   crictl pods [command options] [arguments...]

OPTIONS:
   --id value                       filter by pod id
   --label value [ --label value ]  filter by key=value label
   --last value, -n value           Show last n recently created pods. Set 0 for unlimited (default: 0)
   --latest, -l                     Show the most recently created pod (default: false)
   --name value                     filter by pod name regular expression pattern
   --namespace value                filter by pod namespace regular expression pattern
   --no-trunc                       Show output without truncating the ID (default: false)
   --output value, -o value         Output format, One of: json|yaml|table (default: "table")
   --quiet, -q                      list only pod IDs (default: false)
   --state value, -s value          filter by pod state
   --verbose, -v                    show verbose info for pods (default: false)
   --help, -h                       show help (default: false)
root@debian10x64:/home/fernando# crictl rmp -h
NAME:
   crictl rmp - Remove one or more pods

USAGE:
   crictl rmp [command options] POD-ID [POD-ID...]

OPTIONS:
   --all, -a    Remove all pods (default: false)
   --force, -f  Force removal of the pod sandbox, disregarding if running (default: false)
   --help, -h   show help (default: false)
root@debian10x64:/home/fernando# crictl rmp -a
E0225 12:28:59.719497   34556 remote_runtime.go:224] "RemovePodSandbox from runtime service failed" err="rpc error: code = DeadlineExceeded desc = context deadline exceeded" podSandboxID="e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0"
E0225 12:28:59.719805   34556 remote_runtime.go:224] "RemovePodSandbox from runtime service failed" err="rpc error: code = DeadlineExceeded desc = context deadline exceeded" podSandboxID="ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b"
removing the pod sandbox "e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0": rpc error: code = DeadlineExceeded desc = context deadline exceeded
removing the pod sandbox "ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b": rpc error: code = DeadlineExceeded desc = context deadline exceeded
root@debian10x64:/home/fernando#

root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# crictl rmp -a -f
E0225 12:29:22.441130   34695 remote_runtime.go:224] "RemovePodSandbox from runtime service failed" err="rpc error: code = DeadlineExceeded desc = context deadline exceeded" podSandboxID="ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b"
E0225 12:29:22.441192   34695 remote_runtime.go:224] "RemovePodSandbox from runtime service failed" err="rpc error: code = DeadlineExceeded desc = context deadline exceeded" podSandboxID="e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0"
removing the pod sandbox "ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b": rpc error: code = DeadlineExceeded desc = context deadline exceeded
removing the pod sandbox "e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0": rpc error: code = DeadlineExceeded desc = context deadline exceeded
root@debian10x64:/home/fernando# crictl pods
POD ID              CREATED             STATE               NAME                       NAMESPACE           ATTEMPT             RUNTIME
e41bd4d079860       2 months ago        NotReady            coredns-5dd5756b68-sr9rq   kube-system         0                   (default)
ad45028c9f6a5       2 months ago        NotReady            coredns-5dd5756b68-629mj   kube-system         0                   (default)
root@debian10x64:/home/fernando#


root@debian10x64:/home/fernando# docker ps -a --filter name=k8s_  -q
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# sudo crictl -r /var/run/containerd/containerd.sock pods --namespace 'kube-system' -q
I0225 12:30:42.871038   34956 util_unix.go:103] "Using this endpoint is deprecated, please consider using full URL format" endpoint="/var/run/containerd/containerd.sock" URL="unix:///var/run/containerd/containerd.sock"
e41bd4d07986070f612e8fc07b0f83a0376f8d415af1bd465a4ef0e0e72a2ec0
ad45028c9f6a5804006defcf7e6c3ab33ff490c25905f080a3926f9df2debe1b
root@debian10x64:/home/fernando#

root@debian10x64:/home/fernando# sudo crictl -r /var/run/containerd/containerd.sock pods
I0225 12:31:30.207618   35112 util_unix.go:103] "Using this endpoint is deprecated, please consider using full URL format" endpoint="/var/run/containerd/containerd.sock" URL="unix:///var/run/containerd/containerd.sock"
POD ID              CREATED             STATE               NAME                       NAMESPACE           ATTEMPT             RUNTIME
e41bd4d079860       2 months ago        NotReady            coredns-5dd5756b68-sr9rq   kube-system         0                   (default)
ad45028c9f6a5       2 months ago        NotReady            coredns-5dd5756b68-629mj   kube-system         0                   (default)
root@debian10x64:/home/fernando#




ver erro
crictl RemovePodSandbox from runtime service failed err rpc error  code  DeadlineExceeded desc   context deadline exceeded  podSandboxID



sudo apt remove containerd



                                    This APT has Super Cow Powers.
root@debian10x64:/home/fernando# sudo apt-key list
/etc/apt/trusted.gpg
--------------------
pub   rsa4096 2020-03-02 [SC] [expired: 2022-03-02]
      F640 3F65 44A3 8863 DAA0  B6E0 3F01 618A 5131 2F3F
uid           [ expired] GitLab B.V. (package repository signing key) <packages@gitlab.com>

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]

pub   dsa1024 2003-02-03 [SCA] [expired: 2022-02-16]
      A4A9 4068 76FC BD3C 4567  70C8 8C71 8D3B 5072 E1F5
uid           [ expired] MySQL Release Engineering <mysql-build@oss.oracle.com>

pub   rsa4096 2020-05-07 [SC]
      E8A0 32E0 94D8 EB4E A189  D270 DA41 8C88 A321 9F7B
uid           [ unknown] HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>
sub   rsa4096 2020-05-07 [E]

pub   rsa2048 2021-05-04 [SC]
      35BA A0B3 3E9E B396 F59C  A838 C0BA 5CE6 DC63 15A3
uid           [ unknown] Artifact Registry Repository Signer <artifact-registry-repository-signer@google.com>

pub   rsa2048 2022-05-21 [SC]
      A362 B822 F6DE DC65 2817  EA46 B53D C80D 13ED EF05
uid           [ unknown] Rapture Automatic Signing Key (cloud-rapture-signing-key-2022-03-07-08_01_01.pub)
sub   rsa2048 2022-05-21 [E]

pub   rsa3072 2019-03-18 [SC] [expired: 2024-02-16]
      1505 8500 A023 5D97 F5D1  0063 B188 E2B6 95BD 4743
uid           [ expired] DEB.SURY.ORG Automatic Signing Key <deb@sury.org>

pub   rsa4096 2016-10-26 [SCEA]
      A758 B3FB CD43 BE8D 123A  3476 BB29 EE03 8ECC E87C
uid           [ unknown] infrastructure-eng <infrastructure-eng@newrelic.com>




sudo apt-key del 'F640 3F65 44A3 8863 DAA0  B6E0 3F01 618A 5131 2F3F'




Reading package lists... Done
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: http://repo.mysql.com/apt/debian buster InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY B7B3B788A8D3785C
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://packages.sury.org/php buster InRelease: The following signatures were invalid: EXPKEYSIG B188E2B695BD4743 DEB.SURY.ORG Automatic Signing Key <deb@sury.org>
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://apt.releases.hashicorp.com buster InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY AA16FCBCA621E701
W: Target Packages (stable/binary-amd64/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Packages (stable/binary-all/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en_US) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-amd64.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-all.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons-small (stable/dep11/icons-48x48.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons (stable/dep11/icons-64x64.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
E: The repository 'https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/  Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o://  Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://packages.gitlab.com/gitlab/gitlab-ce/debian buster InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 3F01618A51312F3F
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: https://packages.gitlab.com/runner/gitlab-runner/debian buster InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 3F01618A51312F3F
W: Target Packages (stable/binary-amd64/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Packages (stable/binary-all/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en_US) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-amd64.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-all.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons-small (stable/dep11/icons-48x48.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons (stable/dep11/icons-64x64.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
root@debian10x64:/home/fernando#





https://askubuntu.com/questions/13065/how-do-i-fix-the-gpg-error-no-pubkey

<https://askubuntu.com/questions/13065/how-do-i-fix-the-gpg-error-no-pubkey>


sudo apt-key del 'A4A9 4068 76FC BD3C 4567  70C8 8C71 8D3B 5072 E1F5'

sudo apt-key del '1505 8500 A023 5D97 F5D1  0063 B188 E2B6 95BD 4743'

sudo apt-key del 'E8A0 32E0 94D8 EB4E A189  D270 DA41 8C88 A321 9F7B'


root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# sudo apt-key del 'A4A9 4068 76FC BD3C 4567  70C8 8C71 8D3B 5072 E1F5'
OK
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# sudo apt-key del '1505 8500 A023 5D97 F5D1  0063 B188 E2B6 95BD 4743'
OK
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# sudo apt-key del 'E8A0 32E0 94D8 EB4E A189  D270 DA41 8C88 A321 9F7B'
OK
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#



gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv B7B3B788A8D3785C
gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv B188E2B695BD4743
gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv AA16FCBCA621E701

gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv 3F01618A51312F3F
gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv 3F01618A51312F3F


root@debian10x64:/home/fernando# gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv B7B3B788A8D3785C
gpg: directory '/root/.gnupg' created
gpg: keybox '/root/.gnupg/pubring.kbx' created
gpg: /root/.gnupg/trustdb.gpg: trustdb created
gpg: key B7B3B788A8D3785C: public key "MySQL Release Engineering <mysql-build@oss.oracle.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv B188E2B695BD4743
gpg: key B188E2B695BD4743: public key "DEB.SURY.ORG Automatic Signing Key <deb@sury.org>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@debian10x64:/home/fernando# gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv AA16FCBCA621E701
gpg: key AA16FCBCA621E701: public key "HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv 3F01618A51312F3F
gpg: key 3F01618A51312F3F: public key "GitLab B.V. (package repository signing key) <packages@gitlab.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@debian10x64:/home/fernando# gpg --keyserver hkp://keyserver.ubuntu.com:80 --recv 3F01618A51312F3F
datgpg: key 3F01618A51312F3F: "GitLab B.V. (package repository signing key) <packages@gitlab.com>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
root@debian10x64:/home/fernando# date
Sun 25 Feb 2024 12:50:57 PM -03
root@debian10x64:/home/fernando#



gpg --export --armor B7B3B788A8D3785C | sudo apt-key add -
gpg --export --armor B188E2B695BD4743 | sudo apt-key add -
gpg --export --armor AA16FCBCA621E701 | sudo apt-key add -
gpg --export --armor 3F01618A51312F3F | sudo apt-key add -
gpg --export --armor 3F01618A51312F3F | sudo apt-key add -


root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando# gpg --export --armor B7B3B788A8D3785C | sudo apt-key add -
gpg --export --armor B188E2B695BD4743 | sudo apt-key add -
gpg --export --armor AA16FCBCA621E701 | sudo apt-key add -
gpg --export --armor 3F01618A51312F3F | sudo apt-key add -
gpg --export --armor 3F01618A51312F3F | sudo apt-key add -OK
root@debian10x64:/home/fernando# gpg --export --armor B188E2B695BD4743 | sudo apt-key add -
OK
root@debian10x64:/home/fernando# gpg --export --armor AA16FCBCA621E701 | sudo apt-key add -
OK
root@debian10x64:/home/fernando# gpg --export --armor 3F01618A51312F3F | sudo apt-key add -
OK
root@debian10x64:/home/fernando# gpg --export --armor 3F01618A51312F3F | sudo apt-key add -


OK
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#
root@debian10x64:/home/fernando#





<https://insujang.github.io/2019-11-21/installing-kubernetes-and-crio-in-debian/>

root@debian10x64:/etc# history | tail
  585  modprobe overlay
  586  modprobe br_netfilter
  587  cat /etc/sysctl.d/99-kubernetes-cri.conf
  588  sysctl --system
  589  add-apt-repository ppa:projectatomic/ppa -y && apt update
  590  vi /etc/apt/sources.list.d/projectatomics.list
  591  apt update
  592  apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8BECF1637AD8C79D
  593  apt update
  594  history | tail
root@debian10x64:/etc#





root@debian10x64:/etc# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8BECF1637AD8C79D
Executing: /tmp/apt-key-gpghome.aFPJWAQTXg/gpg.1.sh --keyserver keyserver.ubuntu.com --recv-keys 8BECF1637AD8C79D
gpg: key 8BECF1637AD8C79D: public key "Launchpad PPA for Project Atomic" imported
gpg: Total number processed: 1
gpg:               imported: 1
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc# apt update
Hit:1 http://repo.mysql.com/apt/debian buster InRelease
Get:2 https://packages.sury.org/php buster InRelease [7,559 B]
Hit:3 http://deb.debian.org/debian buster InRelease
Hit:4 http://security.debian.org/debian-security buster/updates InRelease
Hit:5 http://deb.debian.org/debian buster-updates InRelease
Hit:6 http://deb.debian.org/debian buster-backports InRelease
Hit:7 https://download.newrelic.com/infrastructure_agent/linux/apt buster InRelease
Hit:8 https://deb.nodesource.com/node_16.x buster InRelease
Get:9 https://download.docker.com/linux/debian buster InRelease [53.9 kB]
Ign:13 http://ppa.launchpad.net/projectatomic/ppa/ubuntu noble InRelease
Hit:10 https://packages.cloud.google.com/apt kubernetes-xenial InRelease
Hit:14 https://apt.releases.hashicorp.com buster InRelease
Ign:15 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/  InRelease
Ign:16 http://ppa.launchpad.net/webupd8team/y-ppa-manager/ubuntu noble InRelease
Ign:17 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o://  InRelease
Err:18 http://ppa.launchpad.net/projectatomic/ppa/ubuntu noble Release
  404  Not Found [IP: 185.125.190.80 80]
Err:19 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/  Release
  404  Not Found [IP: 195.135.223.226 443]
Get:20 http://ppa.launchpad.net/projectatomic/ppa/ubuntu bionic InRelease [21.3 kB]
Err:21 https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o://  Release
  404  Not Found [IP: 195.135.223.226 443]
Hit:11 https://packages.gitlab.com/gitlab/gitlab-ce/debian buster InRelease
Err:22 http://ppa.launchpad.net/webupd8team/y-ppa-manager/ubuntu noble Release
  404  Not Found [IP: 185.125.190.80 80]
Get:23 http://ppa.launchpad.net/projectatomic/ppa/ubuntu bionic/main Sources [3,916 B]
Get:24 http://ppa.launchpad.net/projectatomic/ppa/ubuntu bionic/main amd64 Packages [2,936 B]
Get:25 http://ppa.launchpad.net/projectatomic/ppa/ubuntu bionic/main Translation-en [1,496 B]
Hit:12 https://packages.gitlab.com/runner/gitlab-runner/debian buster InRelease
Reading package lists... Done
W: Target Packages (stable/binary-amd64/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Packages (stable/binary-all/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en_US) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-amd64.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-all.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons-small (stable/dep11/icons-48x48.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons (stable/dep11/icons-64x64.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
E: The repository 'http://ppa.launchpad.net/projectatomic/ppa/ubuntu noble Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/  Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o://  Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'http://ppa.launchpad.net/webupd8team/y-ppa-manager/ubuntu noble Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
W: Target Packages (stable/binary-amd64/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Packages (stable/binary-all/Packages) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en_US) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target Translations (stable/i18n/Translation-en) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-amd64.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11 (stable/dep11/Components-all.yml) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons-small (stable/dep11/icons-48x48.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1
W: Target DEP-11-icons (stable/dep11/icons-64x64.tar) is configured multiple times in /etc/apt/sources.list:22 and /etc/apt/sources.list.d/docker.list:1






E: The repository 'http://ppa.launchpad.net/projectatomic/ppa/ubuntu noble Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/  Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o://  Release' does not have a Release file.
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
E: The repository 'http://ppa.launchpad.net/webupd8team/y-ppa-manager/ubuntu noble Release' does not have a Release file.





<https://linuxsimply.com/linux-basics/package-management/update-packages/apt-get-update-not-working/>

root@debian10x64:/etc# rm /etc/apt/sources.list.d/*
root@debian10x64:/etc# sudo apt-get clean
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc# apt update
Get:1 https://download.docker.com/linux/debian buster InRelease [53.9 kB]
Hit:2 http://security.debian.org/debian-security buster/updates InRelease
Hit:3 http://deb.debian.org/debian buster InRelease
Hit:4 http://deb.debian.org/debian buster-updates InRelease
Fetched 53.9 kB in 1s (52.2 kB/s)
Reading package lists... Done
Building dependency tree
Reading state information... Done
77 packages can be upgraded. Run 'apt list --upgradable' to see them.
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc# apt remove containerd
Reading package lists... Done
Building dependency tree
Reading state information... Done
Note, selecting 'containerd.io' instead of 'containerd'
The following packages were automatically installed and are no longer required:
  docker-ce-rootless-extras docker-scan-plugin libevent-core-2.1-6 libevent-pthreads-2.1-6 libopts25 sntp xdg-desktop-portal xdg-desktop-portal-gtk
Use 'sudo apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 77 not upgraded.
root@debian10x64:/etc#




disable-pull-on-run: false
root@debian10x64:/etc# ls /var/run/
avahi-daemon  chronyd.pid    containers  crond.reboot  dhclient.ens33.pid  docker  gdm3.pid   ipcns  lvm           mysqld   NetworkManager  pidns            reboot-required.pkgs  snapd-snap.socket  sshd      systemd     udisks2  utmp
blkid         console-setup  crio        cups          dmeventd-client     fsck    initctl    lock   motd.dynamic  netns    newrelic-infra  plymouth         sendsigs.omit.d       snapd.socket       sshd.pid  tmpfiles.d  user     utsns
chrony        containerd     crond.pid   dbus          dmeventd-server     gdm3    initramfs  log    mount         network  nginx.pid       reboot-required  shm                   speech-dispatcher  sudo      udev        userns
root@debian10x64:/etc# ls /var/run/crio/
crio.sock  exec-pid-dir  exits  version
root@debian10x64:/etc# ls /var/run/crio/
crio.sock     exec-pid-dir/ exits/        version
root@debian10x64:/etc# ls /var/run/crio/crio.sock
/var/run/crio/crio.sock
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc# ls /var/run/crio/crio.sock ^C
root@debian10x64:/etc# vi /etc/crictl.yaml
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc#
root@debian10x64:/etc# sudo crictl info
{
  "status": {
    "conditions": [
      {
        "type": "RuntimeReady",
        "status": true,
        "reason": "",
        "message": ""
      },
      {
        "type": "NetworkReady",
        "status": true,
        "reason": "",
        "message": ""
      }
    ]
  }
}
root@debian10x64:/etc# systemctl status crio
● crio.service - Container Runtime Interface for OCI (CRI-O)
   Loaded: loaded (/lib/systemd/system/crio.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2024-02-25 13:18:10 -03; 1min 51s ago
     Docs: https://github.com/cri-o/cri-o
 Main PID: 85624 (crio)
    Tasks: 12
   Memory: 27.3M
   CGroup: /system.slice/crio.service
           └─85624 /usr/bin/crio

Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.713547985-03:00" level=info msg="No seccomp profile specified, using the internal default"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.713555415-03:00" level=info msg="Installing default AppArmor profile: crio-default"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.728831071-03:00" level=info msg="No blockio config file specified, blockio not configured"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.728859502-03:00" level=info msg="RDT not available in the host system"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.745333126-03:00" level=info msg="Found CNI network cilium (type=cilium-cni) at /etc/cni/net.d/05-cilium.conflist"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.759979165-03:00" level=info msg="Found CNI network crio (type=bridge) at /etc/cni/net.d/100-crio-bridge.conf"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.761795540-03:00" level=info msg="Found CNI network 200-loopback.conf (type=loopback) at /etc/cni/net.d/200-loopback.conf"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.761821190-03:00" level=info msg="Updated default CNI network name to cilium"
Feb 25 13:18:10 debian10x64 crio[85624]: time="2024-02-25 13:18:10.766731028-03:00" level=warning msg="Error encountered when checking whether cri-o should wipe images: version file /var/lib/crio/version not found: open /var/lib/crio/version: no such file or directory"
Feb 25 13:18:10 debian10x64 systemd[1]: Started Container Runtime Interface for OCI (CRI-O).
root@debian10x64:/etc#




root@debian10x64:/etc# systemctl status crio
● crio.service - Container Runtime Interface for OCI (CRI-O)
   Loaded: loaded (/lib/systemd/system/crio.service; enabled; vendor preset: enabled)
   Active: active (running) since Sun 2024-02-25 13:20:14 -03; 11s ago
     Docs: https://github.com/cri-o/cri-o
 Main PID: 86033 (crio)
    Tasks: 11
   Memory: 22.1M
   CGroup: /system.slice/crio.service
           └─86033 /usr/bin/crio

Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.847970763-03:00" level=info msg="Conmon does support the --log-global-size-max option"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.847997653-03:00" level=info msg="No seccomp profile specified, using the internal default"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.848002513-03:00" level=info msg="Installing default AppArmor profile: crio-default"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.861193887-03:00" level=info msg="No blockio config file specified, blockio not configured"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.861205357-03:00" level=info msg="RDT not available in the host system"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.878428887-03:00" level=info msg="Found CNI network cilium (type=cilium-cni) at /etc/cni/net.d/05-cilium.conflist"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.881280486-03:00" level=info msg="Found CNI network crio (type=bridge) at /etc/cni/net.d/100-crio-bridge.conf"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.882659541-03:00" level=info msg="Found CNI network 200-loopback.conf (type=loopback) at /etc/cni/net.d/200-loopback.conf"
Feb 25 13:20:14 debian10x64 crio[86033]: time="2024-02-25 13:20:14.882681531-03:00" level=info msg="Updated default CNI network name to cilium"
Feb 25 13:20:14 debian10x64 systemd[1]: Started Container Runtime Interface for OCI (CRI-O).
root@debian10x64:/etc# date
Sun 25 Feb 2024 01:20:27 PM -03
root@debian10x64:/etc#




root@debian10x64:/etc#
root@debian10x64:/etc# crictl ps
CONTAINER           IMAGE               CREATED             STATE               NAME                ATTEMPT             POD ID              POD
root@debian10x64:/etc# crictl pods
POD ID              CREATED             STATE               NAME                NAMESPACE           ATTEMPT             RUNTIME
root@debian10x64:/etc# date
Sun 25 Feb 2024 01:20:39 PM -03
root@debian10x64:/etc#





root@debian10x64:/etc# kubeadm reset
W0225 13:20:49.526227   86205 preflight.go:56] [reset] WARNING: Changes made to this host by 'kubeadm init' or 'kubeadm join' will be reverted.
[reset] Are you sure you want to proceed? [y/N]: y
[preflight] Running pre-flight checks
W0225 13:20:51.031395   86205 removeetcdmember.go:106] [reset] No kubeadm config, using etcd pod spec to get data directory
[reset] Stopping the kubelet service
[reset] Unmounting mounted directories in "/var/lib/kubelet"
[reset] Deleting contents of directories: [/etc/kubernetes/manifests /var/lib/kubelet /etc/kubernetes/pki]
[reset] Deleting files: [/etc/kubernetes/admin.conf /etc/kubernetes/kubelet.conf /etc/kubernetes/bootstrap-kubelet.conf /etc/kubernetes/controller-manager.conf /etc/kubernetes/scheduler.conf]

The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
root@debian10x64:/etc#




- OK kubeadm init agora

~~~~bash

root@debian10x64:/etc# kubeadm init
I0225 13:21:11.035436   86253 version.go:256] remote version is much newer: v1.29.2; falling back to: stable-1.28
[init] Using Kubernetes version: v1.28.7
[preflight] Running pre-flight checks
        [WARNING SystemVerification]: missing optional cgroups: hugetlb
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [debian10x64 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.136.128]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [debian10x64 localhost] and IPs [192.168.136.128 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [debian10x64 localhost] and IPs [192.168.136.128 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 13.002174 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node debian10x64 as control-plane by adding the labels: [node-role.kubernetes.io/control-plane node.kubernetes.io/exclude-from-external-load-balancers]
[mark-control-plane] Marking the node debian10x64 as control-plane by adding the taints [node-role.kubernetes.io/control-plane:NoSchedule]
[bootstrap-token] Using token: jdfg8g.sirk72qkctgrbn06
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] Configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] Configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] Configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.136.128:6443 --token jdfg8g.sirk72qkctgrbn06 \
        --discovery-token-ca-cert-hash  
root@debian10x64:/etc#

~~~~




## SOLUÇÃO

1. Desinstalar o containerd, parar os Pods que estavam up no containerd.

2. Instalar o cri-o no Debian:

<https://computingforgeeks.com/install-cri-o-container-runtime-on-debian-linux/>

  636  sudo apt update && sudo apt -y upgrade
  637  OS=Debian_10
  638  CRIO_VERSION=1.23
  639  echo "deb https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/ /"|sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable.list
  640  echo "deb http://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/$CRIO_VERSION/$OS/ /"|sudo tee /etc/apt/sources.list.d/devel:kubic:libcontainers:stable:cri-o:$CRIO_VERSION.list
  641  curl -L https://download.opensuse.org/repositories/devel:kubic:libcontainers:stable:cri-o:$CRIO_VERSION/$OS/Release.key | sudo apt-key add -
  642  curl -L https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable/$OS/Release.key | sudo apt-key add -
  643  sudo apt update
  644  sudo apt upgrade
  645  sudo apt install cri-o cri-o-runc
  646  cat /etc/crictl.yaml
  647  sudo apt install cri-o cri-o-runc
  648  sudo apt remove cri-o cri-o-runc
  649  sudo apt install cri-o cri-o-runc
  650  sudo systemctl start crio.service
  651  sudo systemctl enable crio.service
  652  systemctl status crio
  653  sudo apt install cri-tools
  654  sudo crictl info
  655  cat /etc/crictl.yaml
  656  ls /var/run/
  657  ls /var/run/crio/
  658  ls /var/run/crio/crio.sock
  659  vi /etc/crictl.yaml
  660  sudo crictl info
  661  systemctl status crio
  662  systemctl restart crio
  663  systemctl status crio
  664  systemctl status crio
  665  date
  666  crictl ps
  667  crictl pods
  668  date
  669  kubeadm reset
  670  kubeadm init










root@debian10x64:/etc#
root@debian10x64:/etc# kubectl get nodes
NAME          STATUS   ROLES           AGE    VERSION
debian10x64   Ready    control-plane   103s   v1.28.1
root@debian10x64:/etc# helm ls -a
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
root@debian10x64:/etc# helm ls -A
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
root@debian10x64:/etc#




# ###################################################################################################################### 
# ###################################################################################################################### 
## PENDENTE

- Seguir lab de Helm, instalação e validação.






- Seguir lab de Helm, instalação e validação.

<https://kodekloud.com/topic/lab-installing-helm/>


You must have Kubernetes installed for a successful and properly secured use of Helm.

Refer the documentation to check the prerequisites. The Documentation tab is available at the top right panel.



<https://helm.sh/docs/intro/install/>

$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh




Install the helm package on the controlplane node.

If unsure how to install the helm tool, feel free to refer to the documentation. The Documentation tab is available at the top right panel.



Note: Make sure to complete the installation process before moving to the next questions.

Helm installed Successfully?



controlplane ~ ➜  helm version
version.BuildInfo{Version:"v3.14.2", GitCommit:"c309b6f0ff63856811846ce18f3bdc93d2b4d54b", GitTreeState:"clean", GoVersion:"go1.21.7"}

controlplane ~ ➜  






Which environment variable can be used to indicate whether or not Helm is running in Debug mode?

Use the help mode of the helm command to look for this information.



controlplane ~ ➜  helm -h | grep -i debug
| $HELM_DEBUG                        | indicate whether or not Helm is running in Debug mode                                                      |
      --debug                           enable verbose output

controlplane ~ ➜  





What is a command line flag that can be used to enable verbose output?



Which of the following is not a valid subcommand to use with helm get command?


controlplane ~ ➜  helm get -h

This command consists of multiple subcommands which can be used to
get extended information about the release, including:

- The values used to generate the release
- The generated manifest file
- The notes provided by the chart of the release
- The hooks associated with the release
- The metadata of the release

Usage:
  helm get [command]

Available Commands:
  all         download all information for a named release
  hooks       download all hooks for a named release
  manifest    download the manifest for a named release
  metadata    This command fetches metadata for a given release
  notes       download the notes for a named release
  values      download the values file for a named release

Flags:
  -h, --help   help for get

Global Flags:
      --burst-limit int                 client-side default throttling limit (default 100)
      --debug                           enable verbose output
      --kube-apiserver string           the address and the port for the Kubernetes API server
      --kube-as-group stringArray       group to impersonate for the operation, this flag can be repeated to specify multiple groups.
      --kube-as-user string             username to impersonate for the operation
      --kube-ca-file string             the certificate authority file for the Kubernetes API server connection
      --kube-context string             name of the kubeconfig context to use
      --kube-insecure-skip-tls-verify   if true, the Kubernetes API server's certificate will not be checked for validity. This will make your HTTPS connections insecure
      --kube-tls-server-name string     server name to use for Kubernetes API server certificate validation. If it is not provided, the hostname used to contact the server is used
      --kube-token string               bearer token used for authentication
      --kubeconfig string               path to the kubeconfig file
  -n, --namespace string                namespace scope for this request
      --qps float32                     queries per second used when communicating with the Kubernetes API, not including bursting
      --registry-config string          path to the registry config file (default "/root/.config/helm/registry/config.json")
      --repository-cache string         path to the file containing cached repository indexes (default "/root/.cache/helm/repository")
      --repository-config string        path to the file containing repository names and URLs (default "/root/.config/helm/repositories.yaml")

Use "helm get [command] --help" for more information about a command.

controlplane ~ ➜  




That's all for now. That was a quick introduction to the helm command line utility. Feel free to explore the helm command line utility further. We will learn more about these commands throughout the remainder of this course.