## 组件


|  组件名称    | 是否必须   | 模块含义|
| -----    | ---------      | ----------- |
| istio-citadel	           | yes               |用于密钥和证书管理|
| istio-egressgateway	   |no                 |出口网关组件    |
| istio-galley		       | yes               |提供 istio 中的配置管理服务, 验证Istio的CRD 资源的合法性.|
| istio-ingressgateway     |yes                |入口网关组件|
| istio-nodeagent		   |no                 |istio开启SDS(Secret discovery service)时需要用到|
| istio-pilot	           | yes               | 对接平台适配层, 抽象服务注册信息、流量控制模型等, 封装统一的 API，供 Envoy 调用获取.|
| istio-policy		       |yes                | 策略控制组件  |
| istio-sidecar-injector   |yes                |启动一个http server, 接受kube api server 的Admission Webhook 请求, 对用户pod进行sidecar注入  |
| istio-telemetry          | yes               | 遥测信息收集组件    |
| grafana                  | no                |一个跨平台的度量分析和可视化工具|
| istio-tracing	           |no                 |链路追踪组件|
| kiali	                   | no                |具有服务网格配置功能的Istio的可观察性控制台|
| prometheus               | no                | 开源监控报警系统和时序数据库|

## 安装

由于本项目使用自己安装 prometheus 和 grafana，所以这两个组件的安装说明不在本文档的范畴。
### 核心组件安装

1.使用`kubectl apply` 安装所有Istio 自定义资源定义 （CRD），
并等待几秒钟，以便在Kubernetes API服务器中提交CRD：

```shell script
kubectl apply -f crds/
```
2.安装istio的核心组件

```shell script
kubectl apply -f istio-core.yaml
```
### 可选组件安装

##### kiali安装
```shell script
kubectl apply -f /kiali
```
##### tracing安装
```shell script
kubectl apply -f /tracing
```
##### egressgateway安装
```shell script
kubectl apply -f /egressgateway
```
##### nodeagent安装
```shell script
kubectl apply -f /nodeagent
```
## 验证安装

1. 确保部署了以下 Kubernetes 服务（部分可选）并验证它们是否具有适当IP

```shell script
kubectl get svc -n istio-system

NAME                     TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)                                                                                                                                      AGE
istio-citadel            ClusterIP      10.109.163.133   <none>        8060/TCP,15014/TCP                                                                                                                           3h45m
istio-egressgateway      ClusterIP      10.97.162.239    <none>        80/TCP,443/TCP,15443/TCP                                                                                                                     4m31s
istio-galley             ClusterIP      10.104.125.16    <none>        443/TCP,15014/TCP,9901/TCP                                                                                                                   3h45m
istio-ingressgateway     LoadBalancer   10.108.124.51    localhost     15020:30065/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31702/TCP,15030:31828/TCP,15031:32368/TCP,15032:31366/TCP,15443:31523/TCP   3h45m
istio-pilot              ClusterIP      10.107.155.1     <none>        15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       3h45m
istio-policy             ClusterIP      10.109.125.59    <none>        9091/TCP,15004/TCP,15014/TCP                                                                                                                 3h45m
istio-sidecar-injector   ClusterIP      10.100.18.156    <none>        443/TCP,15014/TCP                                                                                                                            3h45m
istio-telemetry          ClusterIP      10.106.77.179    <none>        9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       3h45m
jaeger-agent             ClusterIP      None             <none>        5775/UDP,6831/UDP,6832/UDP                                                                                                                   3m56s
jaeger-collector         ClusterIP      10.97.160.60     <none>        14267/TCP,14268/TCP                                                                                                                          3m56s
jaeger-query             ClusterIP      10.99.111.178    <none>        16686/TCP                                                                                                                                    3m56s
kiali                    ClusterIP      10.99.43.105     <none>        20001/TCP                                                                                                                                    3h42m
tracing                  ClusterIP      10.97.20.232     <none>        80/TCP                                                                                                                                       3m56s
zipkin                   ClusterIP      10.104.70.240    <none>        9411/TCP                                                                                                                                     3m56s

```

2. 确保部署了相应的 Kubernetes pod 并且 STATUS 为 Running

```shell script
kubectl get pods -n istio-system

NAME                                      READY   STATUS      RESTARTS   AGE
istio-citadel-679b7c9b5b-vcv6w            1/1     Running     2          3h43m
istio-cleanup-secrets-1.3.0-rf7xg         0/1     Completed   0          3h43m
istio-egressgateway-5db67796d5-bcssp      1/1     Running     0          2m27s
istio-galley-5b597b94bb-j6sjb             1/1     Running     0          4m56s
istio-ingressgateway-7965c97677-jvfmn     0/1     Running     1          3h43m
istio-nodeagent-n4rr9                     1/1     Running     0          58s
istio-pilot-6d985766bd-ptr46              2/2     Running     2          3h43m
istio-policy-7d7b5f454f-d5r6v             2/2     Running     6          3h43m
istio-security-post-install-1.3.0-t4rm5   0/1     Completed   0          3h43m
istio-sidecar-injector-68f4668959-4lh9j   1/1     Running     2          3h43m
istio-telemetry-675d747dcf-hrzwd          2/2     Running     6          3h43m
istio-tracing-669fd4b9f8-sb24s            1/1     Running     0          113s
kiali-94f8cbd99-kn4nf                     1/1     Running     1          3h40m
```

## 卸载

1. 使用 `kubectl delete`删除istio相关组件

```shell script
kubectl delete -f istio-core.yaml
kubectl delete -f egressgateway/
kubectl delete -f kiali/
kubectl delete -f nodeagent/
kubectl delete -f tracing/
```

2. 如果需要，删除 istio 的 CRD

```shell script
kubectl delete -f crds/
```