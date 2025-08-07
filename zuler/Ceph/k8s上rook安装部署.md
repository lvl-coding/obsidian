```bash
$ git clone --single-branch --branch v1.17.6 https://github.com/rook/rook.git

$ kubectl create -f deploy/examples/toolbox.yaml
$ kubectl -n rook-ceph rollout status deploy/rook-ceph-tools
$ kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- bash
$ kubectl -n rook-ceph delete deploy/rook-ceph-tools

$ kubectl create -f deploy/examples/toolbox-job.yaml
$ kubectl -n rook-ceph logs -l job-name=rook-ceph-toolbox-job


```

```sh
(
  set -x; cd "$(mktemp -d)" &&
  OS="$(uname | tr '[:upper:]' '[:lower:]')" &&
  ARCH="$(uname -m | sed -e 's/x86_64/amd64/' -e 's/\(arm\)\(64\)\?.*/\1\2/' -e 's/aarch64$/arm64/')" &&
  KREW="krew-${OS}_${ARCH}" &&
  curl -fsSLO "https://github.com/kubernetes-sigs/krew/releases/latest/download/${KREW}.tar.gz" &&
  tar zxvf "${KREW}.tar.gz" &&
  ./"${KREW}" install krew
)

```

```sh
export PATH="${KREW_ROOT:-$HOME/.krew}/bin:$PATH"
```

```sh
$ kubectl krew install rook-ceph
$ kubectl rook-ceph ceph status
```