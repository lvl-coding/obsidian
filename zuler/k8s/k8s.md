Kubernetes 是一个可移植、可扩展的开源平台，用于管理容器化的工作负载和服务，方便进行声明式配置和自动化。Kubernetes 拥有一个庞大且快速增长的生态系统，其服务、支持和工具的使用范围广泛。

```
    1  cd /etc/sudoers.d/
    2  ls
    3  vim kvirtadm
    4  df -h
    5  lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
    6  resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
    7  poweroff
    8  cat /proc/cpuinfo
    9  cd
   10  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.32.2
   11  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2
   12  ufw status
   13  crictl ps
   14  less /etc/kubernetes/manifests/kube-vip.yaml
   15  kubectl get nodes
   16  echo $VIP
   17  less /etc/kubernetes/manifests/kube-vip.yaml
   18  crictl ps
   19  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock ps -a | grep kube | grep -v pause
   20  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock logs a3af4b2e27f94
   21  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2 --v=5
   22  kubeadm --version
   23  kubeadm version
   24  kubeadm init --help
   25  kubeadm reset
   26  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2 --v=5
   27  kubectl get nodes
   28  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock ps -a | grep kube | grep -v pause
   29  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock logs dac1cbb941a6d
   30  kubeadm reset
   31  rm /etc/kubernetes/manifests/kube-vip.yaml
   32  less /etc/kubernetes/manifests/kube-vip.yaml
   33  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2
   34  ip a
   35  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock ps -a | grep kube | grep -v pausetes-version v1.33.2
   36  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock ps -a | grep kube | grep -v pause
   37  crictl --runtime-endpoint unix:///var/run/containerd/containerd.sock logs 5bb3d42c2be90
   38  crictl ps
   39  kubeadm reset -f
   40  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2 -v=10
   41  kubeadm reset -f
   42  vim /etc/hosts
   43  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2
   44  kubectl get nodes
   45  kubeadm join k8s-contarol-plane:6443 --apiserver-advertise-address 192.168.10.195 --token rekts1.im5wxtzz4qq1dwf3 \
   46  kubeadm join k8s-contarol-plane:6443 --apiserver-advertise-address 192.168.10.195 --token z9thni.bueijiez6e1nmld1 --discovery-token-ca-cert-hash sha256:eb7884e72043450d984652cf83491d84a3a964e977c64c8fd33a920a9cb0cb23 --control-plane --certificate-key e2cc9afd4b92d4a6148262179d51cf004356fab549f45e722f7deb419f07b492
   47  crictl ps
   48  crictl ps -a
   49  echo $HOME
   50  kubectl get nodes
   51  less /etc/hosts
   52  kubectl get nodes
   53  poweroff
   54  curl http://127.0.0.1:10248/healthz
   55  curl -v http://127.0.0.1:10248/healthz
   56  cd
   57  vim ~/.bashrc +3326
   58  kubectl taint nodes --all node-role.kubernetes.io/control-plane-
   59  wget https://get.helm.sh/helm-v3.17.1-linux-amd64.tar.gz
   60  tar -xvf helm-v3.17.1-linux-amd64.tar.gz
   61  install -m 755 linux-amd64/helm /usr/local/bin
   62  helm repo add projectcalico https://docs.tigera.io/calico/charts
   63  helm repo update
   64  helm search repo calico --versions
   65  kubectl create namespace tigera-operator
   66  helm show values projectcalico/tigera-operator --version v3.28.3 > calico.yaml
   67  helm install calico projectcalico/tigera-operator --version v3.28.3 --namespace tigera-operator --create-namespace
   68  helm repo add projectcalico https://docs.tigera.io/calico/charts
   69  helm pull projectcalico/tigera-operator --untar --version 3.28.3
   70  vim tigera-operator/values.yaml
   71  ./calico-install.sh
   72  vim tigera-operator/values.yaml
   73  kubectl create namespace tigera-operator
   74  helm install calico ./tigera-operator
   75  ls tigera-operator/
   76  helm package ./tigera-operator
   77  helm install  nginx-ingress ./tigera-operator-v3.28.3.tgz
   78  kubectl get pods -A
   79  crictl ps
   80  kubectl get nodes
   81  kubeadm help
   82  kubeadm reset --help
   83  kubeadm reset
   84  kubectl get nodes
   85  kubeadm init --apiserver-advertise-address 192.168.10.195 --pod-network-cidr=10.244.0.0/16 --service-cidr=10.96.0.0/12 --control-plane-endpoint "k8s-contarol-plane:6443" --upload-certs --kubernetes-version v1.33.2
   86  kubeadm join k8s-contarol-plane:6443 --apiserver-advertise-address 192.168.10.195 --token qzwd8d.tafmc8oyvgth35r7 	--discovery-token-ca-cert-hash sha256:c762031f0954836c71bec7996ea088d2becd5ed5749426acaec4e5292e19fddc
   87  kubectl get nodes
   88  kubectl taint nodes --all node-role.kubernetes.io/control-plane-
   89  helm repo add projectcalico https://docs.tigera.io/calico/charts
   90  helm repo update
   91  helm search repo calico --versions
   92  kubectl create namespace tigera-operator
   93  helm show values projectcalico/tigera-operator --version v3.28.3 > calico.yaml
   94  helm install calico projectcalico/tigera-operator --version v3.28.3 --namespace tigera-operator --create-namespace
   95  vim tigera-operator/values.yaml
   96  helm install calico ./tigera-operator
   97  vim tigera-operator/values.yaml
   98  vim ./tigera-operator/values.yaml
   99*
  100  helm install calico ./tigera-operator
  101  ls
  102  tar -zxvf tigera-operator-v3.28.3.tgz
  103  ls
  104  vim tigera-operator/values.yaml
  105  vim tigera-operator/Chart.yaml
  106  kubectl create namespace tigera-operator
  107  helm install calico ./tigera-operator
  108  helm install calico projectcalico/tigera-operator --version v3.28.3 --namespace tigera-operator --create-namespace
  109  kubectl get ns
  110  kubectl  get pods -A
  111  vi ~/.bashrc
  112  √source <(kubectl completion bash)
  113  source <(kubectl completion bash)
  114  kubectl --help
  115  kubectl completion
  116  kubectl completion --help
  117  source <(kubectl completion bash)
  118  ls
  119  ssh-rsa
  120  ssh-keygen
  121  less ~/.ssh/id_rsa.pub
  122  git clone git@gitlab.zuler.sh:virt-infra/cloudspace/middleplatform/bmrmp.git
  123  cd bmrmp/
  124  git status
  125  git fetch origin develop:develop
  126  git checkout develop
  127  wget https://github.com/git/git/blob/v2.34.1/contrib/completion/git-completion.bash
  128  ls
  129  vim ~/.bashrc
  130  ls /usr/share/bash-completion/completions/git
  131  source ~/.bashrc
  132  git branch
  133  ls
  134  make
  135  apt install make
  136  make
  137  go
  138  apt install go
  139  snap install go
  140  apt  install golang-go
  141  apt  install gogcc-go
  142  apt  install golanggcc-go
  143  apt install docker
  144  sudo apt install apt-transport-https ca-certificates curl software-properties-common
  145  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
  146  apt install docker-ce
  147  docker
  148  apt install doker
  149  docker
  150  apt-get install docker-ce docker-ce-cli containerd.io
  151  fio -bs=1M -ioengine=windowsaio -iodepth=1 -numjobs=1 -direct=1 -rw=rw -thread -refill_buffers -randrepeat=0 -group_reporting -name=fio-readwrite-throughput -verify=crc32 -nrfiles=5 -filesize=4M -directory="Z\:targetdir"
  152  apt install doker.io
  153  apt install docker.io
  154  docker ps
  155  systemctl status docker
  156  systemctl status docker.socket
  157  docker --version
  158  docker images
  159  docker run -d -p 17777:17777 --restart=always --name registry registry
  160  docker ps
  161  docker images
  162  docker image ls
  163  curl 127.0.0.1:17777/v2/_catalog
  164  curl 192.168.10.195:17777/v2/_catalog
  165  ss -antlp | grep 17777
  166  ls /docker/var/lib/registry
  167  vim /etc/docker/daemon.json
  168  curl 192.168.10.195:17777/v2/_catalog
  169  systemctl restart docker
  170  curl 192.168.10.195:17777/v2/_catalog
  171  docker exec -it registry bash
  172  docker exec -it registry sh
  173  docker ps
  174  docker history registry
  175  docker stop registry
  176  docker rm registry
  177  docker run -d -p 5000:5000 --restart=always --name registry registry
  178  docker ps
  179  vim /etc/docker/daemon.json
  180  curl 192.168.10.195:5000/v2/_catalog
  181  make docker-build docker-push IMG=192.168.10.195:5000/bmrmp:tag
  182  docker images
  183  curl 192.168.10.195:5000/v2/_catalog
  184  docker push bmrmp-scheduler:20250716142317
  185  docker login
  186  docker push 192.168.10.195:5000/bmrmp-scheduler:20250716142317
  187  docker ps
  188  docker images
  189  make docker-build docker-push IMG=192.168.10.195:5000/bmrmp:testlv
  190  docker images
  191  make docker-build docker-push IMG=<192.168.10.195:5000>/bmrmp:testlv
  192  make docker-build docker-push IMG=192.168.10.195:5000/bmrmp:testlv
  193  docker ps
  194  docker images
  195  docker tag registry:latest 192.168.10.195:5000/registry:latest
  196  docker images
  197  docker push 192.168.10.195:5000/registry:latest
  198  vim /etc/docker/daemon.json
  199  :q
  200  :
  201  vim /etc/docker/daemon.json
  202  ls
  203  systemctl restart docker
  204  docker push 192.168.10.195:5000/registry:latest
  205  make docker-build docker-push IMG=192.168.10.195:5000/bmrmp:testlv
  206  make docker-build IMG=192.168.10.195:5000/bmrmp:testlv
  207  docker ps
  208  docker images
  209  sh hack/docker_clean.sh
  210  bash -e hack/docker_clean.sh
  211  docker images
  212  docker image rm 5fc8a0a0bf2a
  213  docker image rm 8f7e07d2c252
  214  docker image rm 38cdb7d6144a
  215  docker images
  216  docker ps
  217  make docker-build IMG=192.168.10.195:5000/bmrmp:testlv
  218  docker images
  219  docker ps
  220  make install
  221  go --version
  222  go version
  223  docker images
  224  apt install go
  225  apt install golang
  226  go version
  227  apt update go
  228  apt update golang
  229  apt update
  230  apt list --upgradable
  231  go version
  232  apt remove golang
  233  go
  234  go version
  235  apt remove go
  236  apt remove golang
  237  history | grep go
  238  apt remove golang-go
  239  go
  240  which go
  241  go
  242  source ~/.bashrc
  243  go version
  244  go env -w GO111MODULE=on
  245  make install
  246  ls bin/
  247  make deploy IMG=192.168.10.195:5000/bmrmp:testlv
  248  docker images
  249  kubectl apply -k config/samples/
  250  ls config/samples/
  251  ls
  252  make; make uninstall; make install ; bash tools/samples.sh deploy
  253  ls
  254  git pull origin develop
  255  git status
  256  git checkout .
  257  git status
  258  git pull origin develop
  259  make; make uninstall; make install ; bash tools/samples.sh deploy
  260  kubectl get cred
  261  kubectl get crd
  262  bash tools/samples.sh deploy
  263  kubectl get crd
  264  kubectl -h
  265  kubeadm -h
  266  kubectl
  267  kubectl -h
  268  kubectl  get
  269  kubectl get -h
  270  vim config/manager/manager.yaml
  271  kubectl apply -f config/manager/manager.yaml
  272  kubectl cluster-info
  273  kubectl cluster-info dump
  274  kubectl get deployments.apps
  275  kubectl get deployments.apps  -n bmrmp-system
  276  kubectl describe deployments.apps -n bmrmp-system
  277  ls
  278  cd config/manager/
  279  ls
  280  vi manager.yaml
  281  kubectl apply -f manager.yaml
  282  kubectl describe deployments.apps -n bmrmp-system bmrmp-controller-manager
  283  kubectl delete -f manager.yaml
  284  kubectl apply -f manager.yaml
  285  kubectl describe deployments.apps -n bmrmp-system bmrmp-controller-manager
  286  vi manager.yaml
  287  kubectl apply -f manager.yaml
  288  kubectl describe deployments.apps -n bmrmp-system bmrmp-controller-manager
  289  kubectl get deployments.apps -n bmrmp-system
  290  kubectl delete deployments.apps -n bmrmp-system bmrmp-controller-manager
  291  kubectl apply -f manager.yaml
  292  kubectl get deployments.apps -n bmrmp-system
  293  kubectl get deployments.apps -A
  294  kubectl apply -f manager.yaml
  295  kubectl  get deployments.apps -A
  296  vi manager.yaml
  297  kubectl apply -f manager.yaml
  298  kubectl delete -n system deployments.apps controller-manager
  299  kubectl  get deployments.apps
  300  kubectl  get deployments.apps  -n bmrmp-system
  301  kubectl describe deployments.apps -n bmrmp-system
  302  kubectl  get pods -n bmrmp-system
  303  vi manager.yaml
  304  kubectl get deployments.apps -n bmrmp-system
  305* kubectl get  -n bmrmp-system
  306  kubectl get nodes
  307  history
```
