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

ubuntu@ip-10-0-1-161:~$ kubectl get pods --show-labels
NAME                                READY   STATUS    RESTARTS   AGE   LABELS
nginx-deployment-56cb9cc9db-6zv82   1/1     Running   0          10s   app=my-nginx,pod-template-hash=56cb9cc9db
nginx-deployment-56cb9cc9db-7vgpl   1/1     Running   0          10s   app=my-nginx,pod-template-hash=56cb9cc9db
```

이제 **Service**를 생성해보겠습니다.

먼저 한 번 볼까요?
cat nginx-clusterip-service.yaml{{execute}}

그리고, 생성
kubectl apply -f nginx-clusterip-service.yaml{{execute}}

생성된걸 볼때는
kubectl get services{{execute}}

생성된 Service의 Cluster IP가 보이시나요?
이 아이피로 Pod까지 접근할 수도 있습니다.

간단한 Pod를 하나 만들어 보겠습니다.
kubectl run curlpod --image=radial/busyboxplus:curl --generator=run-pod/v1 --command -- /bin/sh -c "while true; do echo hi; sleep 10; done"{{execute}}

그리고, 그 Pod에서 curl을 실행하는데, 앞서 만든 Service의 Name을 이용합니다.
kubectl exec -it curlpod -- curl nginx-clusterip-service{{execute}}

잘 되네요...
이제 Service를 만들면 클러스터 내에서는 이름으로도 접근이 가능합니다.
당연히 Cluster IP로도 가능하구요.

볼까요?
Cluster IP를 알아내서
export CLUSTER_IP=$(kubectl get services/nginx-clusterip-service -o go-template='{{(index .spec.clusterIP)}}'){{execute}}

확인해보고
echo CLUSTER_IP=$CLUSTER_IP{{execute}}

그 IP로 접근합니다.
curl http://$CLUSTER_IP:80{{execute}}

잘 되죠?

이번엔 NodePort입니다.

자세한 설명은 생략할게요.
한 번 아래 명령어들을 실행하면서 차이를 보세요.

cat nginx-nodeport-service.yaml{{execute}}

kubectl apply -f nginx-nodeport-service.yaml{{execute}}

kubectl get services{{execute}}

ClusterIP와 NodePort 유형의 Service간 차이가 보이시나요?

NodePort는 아래와 같은 방법으로 접근 가능합니다.
kubectl get nodes -o wide{{execute}}

curl http://[NODE_IP]:30007

같은 클러스터 내에 있는 다른 Node의 Port로도 해보세요. 될까요?