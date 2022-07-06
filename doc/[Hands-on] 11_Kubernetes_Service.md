---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
paginate: true
header: Docker & Kubernetes - [Hands-on] 11. Kubernetes Service
footer: Samsung SDS
---

## [Hands-on] 11. Kubernetes Service

이번 실습에서는 **Service**를 이용하는 방법을 알아보겠습니다.

먼저 **Deployment**를 이용해서 Pod를 몇 개 생성해 보겠습니다.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: my-nginx
    tier: frontend
spec:
  replicas: 2
  strategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app: my-nginx
  template:
    metadata:
      labels:
        app: my-nginx
      name: my-nginx
    spec:
      containers:
      - image: nginx:1.19.3
        name: my-nginx
        ports:
        - containerPort: 80
```
> 파일명은 nginx-deployment.yaml로 합니다.

---

그리고, 아래와 같이 생성한 다음, 생성된 Pod을 조회합니다.
```bash
ubuntu@ip-10-0-1-161:~$ kubectl apply -f nginx-deployment.yaml
deployment.apps/nginx-deployment created

ubuntu@ip-10-0-1-161:~$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP           NODE       NOMINATED NODE   READINESS GATES
nginx-deployment-56cb9cc9db-bh4q6   1/1     Running   0          53s   172.17.0.4   minikube   <none>           <none>
nginx-deployment-56cb9cc9db-hgp6h   1/1     Running   0          53s   172.17.0.3   minikube   <none>           <none>
```

첫 번째 Pod에서 두 번째 Pod의 서비스를 사용하는 상황을 가정해 보겠습니다.

```bash
ubuntu@ip-10-0-1-161:~$ kubectl exec -it nginx-deployment-56cb9cc9db-bh4q6 -- curl http://172.17.0.4
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
> **명령어** : `kubectl exec -it [POD1_NAME] -- curl http://[POD2_IP]`
> [POD1_NAME] 은 첫 번째 Pod의 NAME, [POD2_IP] 는 두 번째 Pod의 IP

잘 동작하네요.

하지만, 이렇게는 쓰기 어렵습니다.
Pod의 IP가 어떻게 주어질지 우리는 알 수가 없고, Scaling되는 환경이라면 개별 Pod에 대한 Loadbalancing 문제도 있습니다.


이제 **Service**를 생성해서 위의 문제들을 해결해보겠습니다.

먼저 ClusterIP 타입의 서비스를 하나 만들어 보겠습니다.
아래와 같은 파일을 준비해주세요.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-clusterip-service
spec:
  type: ClusterIP
  selector:
    app: my-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
> 파일명은 nginx-clusterip-service.yaml로 합니다.

그리고, 생성합니다.
```bash
ubuntu@ip-10-0-1-161:~$ kubectl apply -f nginx-clusterip-service.yaml
service/nginx-clusterip-service created
```
> **명령어** : `kubectl apply -f nginx-clusterip-service.yaml`


생성된걸 조회할 대는 아래와 같이 합니다.
```
ubuntu@ip-10-0-1-161:~$ kubectl get services
NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP   40h
nginx-clusterip-service   ClusterIP   10.107.31.242   <none>        80/TCP    34s
```
> **명령어** : `kubectl get services`

생성된 Service의 **CLUSTER-IP**가 보이시나요?
이 아이피로 Pod까지 접근할 수도 있습니다.

```bash
ubuntu@ip-10-0-1-161:~$  kubectl exec -it nginx-deployment-56cb9cc9db-bh4q6 -- curl http://10.107.31.242
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
> **명령어** : `kubectl exec -it [POD1_NAME] -- curl http://[SVC_CLUSTER_IP]`
> [POD1_NAME] 은 첫 번째 Pod의 NAME, [SVC_CLUSTER_IP] 는 Service의 CLUSTER-IP

IP가 아닌 Name으로도 가능합니다.
이렇게요.
```bash
ubuntu@ip-10-0-1-161:~/mspt2/hands_on_files$ kubectl exec -it  nginx-deployment-56cb9cc9db-bh4q6 -- curl http://nginx-clusterip-service
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
> **명령어** : `kubectl exec -it [POD1_NAME] -- curl http://[SVC_NAME]`
> [POD1_NAME] 은 첫 번째 Pod의 NAME, [SVC_NAME] 는 Service의 NAME

잘 되네요...
이제 Service를 만들면 클러스터 내에서는 이름으로도 접근이 가능합니다.

이번엔 NodePort입니다.

Node의 특정 Port를 이용하는 방식입니다.

먼저 NodePort 타입의 서비스를 하나 만들어 보겠습니다.
아래와 같은 파일을 준비해주세요.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport-service
spec:
  type: NodePort
  selector:
    app: my-nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30007
```
> 파일명은 nginx-nodeport-service.yaml로 합니다.

그리고, 생성하고 조회까지 해볼게요.
```bash
ubuntu@ip-10-0-1-161:~$ kubectl apply -f nginx-nodeport-service.yaml
service/nginx-nodeport-service created

ubuntu@ip-10-0-1-161:~$ kubectl get services
NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP        41h
nginx-clusterip-service   ClusterIP   10.107.31.242   <none>        80/TCP         20m
nginx-nodeport-service    NodePort    10.104.230.63   <none>        80:30007/TCP   60s
```
> **명령어** : `kubectl apply -f nginx-nodeport-service.yaml` , `kubectl get services`


ClusterIP와 NodePort 유형의 Service간 차이가 보이시나요?

NodePort는 아래와 같은 방법으로 접근 가능합니다.

```bash
ubuntu@ip-10-0-1-161:~$ kubectl get nodes -o wide
NAME       STATUS   ROLES           AGE   VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION    CONTAINER-RUNTIME
minikube   Ready    control-plane   41h   v1.24.1   192.168.49.2   <none>        Ubuntu 20.04.4 LTS   5.15.0-1013-aws   docker://20.10.17
```
> **명령어** : `kubectl get nodes -o wide`

```bash
ubuntu@ip-10-0-1-161:~$ curl http://192.168.49.2:30007
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
> **명령어** : `curl http://[NODE_IP]:30007`
> [NODE_IP] 는 Node의 IP


같은 클러스터 내에 있는 다른 Node의 Port로도 해보세요. 될까요?
