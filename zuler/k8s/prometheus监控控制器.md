要跨 namespace 添加 metric，先给 prometheus 添加权限
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  namespace: monitoring
  name: prometheus

```
创建服务账户，编写 sa-prometheus.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups: [""]
  resources:
  - nodes
  - nodes/metrics
  - services
  - endpoints
  - pods
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources:
  - configmaps
  verbs: ["get"]
- apiGroups:
  - networking.k8s.io
  resources:
  - ingresses
  verbs: ["get", "list", "watch"]
- nonResourceURLs: ["/metrics"]
  verbs: ["get"]

```
创建集群角色以及分配相应的权限，编写 clusterrole-prometheus.yaml
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: prometheus
  namespace: monitoring

```
把 clusterrole 与 serviceAccount 绑定，编写 clusterrolebinding-prometheus.yaml
```bash
$ kubectl apply -f <>.yaml
$ kubectl edit -n monitoring prometheus prometheus-kube-prometheus-prometheus
```
修改 prometheus(CRD) 的 [K8s](https://so.csdn.net/so/search?q=K8s&spm=1001.2101.3001.7020) 的 serviceAccount，把之前的 prometheus-k8s 修改为刚刚创建的服务账户 prometheus。
```yaml
......
  serviceAccountName: prometheus
  serviceMonitorNamespaceSelector: {}
  serviceMonitorSelector:
......
```
现在就可以跨 namespce 添加指标了。
# 添加集群内 target
添加集群内 target，需要用到 CRD  (Custom Resource Define) 自定义资源定义 ServiceMonitor。该 CRD 定义了如何监控一组动态的服务，使用标签选择来定义哪些 Service 被选择进行监控。需要注意的是，被监控的服务有暴露 metrics 端点，可以先用 curl 测试
```bash
$ curl 'svc_ip:port'/metrics
$ kubectl -n monitoring describe prometheus prometheus-kube-prometheus-prometheus
```
查看 peometheus 的配置。
```bash
$ kubectl -n monitoring describe prometheus k8s
......
  serviceAccountName: prometheus
  serviceMonitorNamespaceSelector: {}
  serviceMonitorSelector:
......
```
可以看到 Namespace Selector 和 Service Monitor Selector 选择器都为空，表示它可以读取集群内所有的 ServiceMonitor。编写 servicemonitor.yaml 文件：
```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: bmrmp-controller-metrics
  namespace: monitoring  #如果 prometheus 配置有指定 namespace，需要在该namespace下创建
  labels:                #如果 prometheus 配置有指定 labelSelector，需要添加对应的标签
    release: prometheus
spec:
  selector:              #添加标签选择器，筛选具有该标签的 svc
    matchLabels:
      app.kubernetes.io/name: bmrmp
      control-plane: controller-manager
  namespaceSelector:    #指定目标 svc 的 namespace，如果是 monitoring 可以省略
    matchNames:
      - bmrmp-system
  endpoints:
    - port: http        #这里的 port 只能填 string 类型
      path: /metrics
      interval: 30s
```
查看 web 页面的 target，