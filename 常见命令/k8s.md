# k8s常见命令

**缩写**

```
-n 表示命名空间/namespace
--all-namespaces 表示所有的命令空间
-c 表示容器的名称/container
-o 表示输出格式/output
-w 持续监控/watch
```



**重要配置**

```
1.配置k8s自动补全功能
echo 'source <(kubectl completion bash)' >>~/.bashrc

source .bashrc 
```



**查看命令**

```
1.查看kubectl中缩写
kubectl api-resources

2.查看pod(service,deployment,ds,sts)详细资源信息/这里一般用来定位问题查看pod为啥启动失败
kubeclt -n cmp describe pod cmp

3.查看容器执行日志(-n 表示命令空间/namespace)(-f 表示滚动输出最近日志)(-c 表示容器名称/container)
 kubectl -n cmp logs -f cmp-deployment-8fb4b7888-rqgdf -c cmp 
 
4.查看指定命名空间的pod列表(-o 表示输出格式/output)(目前格式有 wide/命令行格式很简单列出运行情况  json/json格式 yaml/yaml格式)
kubectl -n 命令空间 get pod(deployment/service/ds/sts) pod名称(deployment名称/service名称/ds名称/sts名称)  -o wide(yaml/json)
kubectl -n kube-system get pod mysql-standalone-8fb4b7888-rqgdf -o yaml
kubectl -n kube-system get deployment mysql-standalone  -o yaml
kubectl -n kube-system get service mysql-standalone -o yaml
kubectl -n kube-system get sts   mysql-standalone -o yaml
kubectl -n kube-system get ds  mysql-standalone -o yaml
 
5.查看当前k8s内部命令执行流程（-v=8）
kubeclt get pod -v=8


8.获取所有的deployment运行实例及其他信息
kubectl  get deployment --all-namespaces


9.获取所有pod(deployment,ds,sts)信息
kubectl get pod --all-namespaces


10查看所有pod上面标签
kubectl get pod --all-namespaces --show-labels

12列出指定标签下面的pod
kubectl get pod --all-namespaces -l test

13.查看pod（deployment,svc,ds,sts)的yaml文件编写规范
kubectl explain pod 

14.查看pod(deployment，svc,ds,sts)启动情况
kubectl -n cmp describe deployment cmp-deployment

15.持续观察pod（deployment,svc,ds,sts）状态
kubectl -n cmp get pod cmp -w 

16.查看node详细信息包括标签其污点(其中 Taints表示污点)
kubectl describe node intellif-0
```

**执行命令**

```
1.进入容器内部命令(kubectl -n 命令空间 exec -it pod名称 -c 容器名称 sh)
 kubectl -n cmp exec -it cmp-deployment-8fb4b7888-rqgdf -c cmp sh
 
 2.删除制定pod(kubectl delete pod -n 命令空间 pod名称)
 kubectl delete pod -n cmp cmp-deployment-c5765bfcb-62cr7
 
 3.编辑指定的configmap(kubectl -n 命名空间 edit configmap 名称)
 kubectl -n cmp edit configmap cmp
 
 4.修改指定的deployment（kubectl -n 命令空间 edit deployment名称）
  kubectl -n cmp edit deploy cmp-deployment
  
5.删pv挂卷
kubectl -n kube-system delete pv mysql-single

6.为某个pod(deployment)打标签
kubectl -n cmp label pod(deployment) cmp test=haha

7.修改已有pod（deployment）上面的标签
kubectl -n cmp label(deployment) pod cmp test=haha2 --overwrite

8.创建pod(deployment,svc,sts,ds)yaml文件生成对应实例
kubectl create -f /opt/data/pod.yaml

9.删除pod(deployment,svc,sts,ds)实例
kubectl delete -f /opt/data/pod.yaml

10.为deployment进行扩缩容
 kubectl -n cmp  scale deployment cmp --replicas=4
 
11.滚动更新
kubectl apply -f deployment名称文件


12.查看deployment升级历史记录
kubectl -n cmp rollout history deployment cmp-deployment

13.回滚到上一个版本
kubectl -n cmp rollout undo deployment cmp-deployment

14.在k8s和外面主机进行文件相互复制
kubectl cp /root/test.txt cmp/cmp-deployment-8fb4b7888-rqgdf:/root -c cmp (将外面主机的文件复制到k8s容器中)
kubectl cp cmp/cmp-deployment-8fb4b7888-rqgdf:/root/test.txt /root/test/log.txt -c cmp (将k8s里面的容器文件复制到外面)


15.通过外部文件创建configmap(其中 -n 后面第一个参数为namespace 第二个为 configmap名称  --from-file= 后面表示文件路径)

kubectl create configmap -n monitoring process-export  --from-file= ./test.yaml
```

**查看当前k8s配置harbor地址**

```
vim /etc/containerd/config.toml
```

