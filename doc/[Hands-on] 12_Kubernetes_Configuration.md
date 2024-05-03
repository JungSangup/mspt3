
# [Hands-on] 12. Kubernetes Configuration

![](./img/hands_on.png)

<br>

# Contents

**[1. ConfigMap, Secret 생성하고 사용하기](#1-configmap-secret-생성하고-사용하기)**

💾실습파일 위치 : [hands_on_files/ch12](../hands_on_files/ch12)

미리 준비된 실습용 파일을 사용하려는 경우 아래 디렉토리 이동 명령어를 실행해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ cd ~/mspt3/hands_on_files/ch12
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files/ch12$
```

> 💻 명령어
>```bash
>cd ~/mspt3/hands_on_files/ch12
>```

---

## 1. ConfigMap, Secret 생성하고 사용하기

**ToDo App**을 또 다른 방법으로 실행해보려고 합니다.  
**Docker Network** 실습에서 했던 방법과 동일하게

- MySQL
- Todo App

을 각각 실행해서 연동하려고 합니다.  
이렇게요.

![](./img/multi-app-architecture.png)

기본적인 구조와 방법은 **Docker Network** 실습과 같고, Kubernetes환경에 맞게 리소스들을 생성해서 해볼게요.

<br>

먼저 MySQL을 실행합니다.
Docker에서는 아래와 같이 했습니다.
```bash
ubuntu@ip-10-0-1-14:~$ docker run -d \
    --network todo-app --network-alias mysql \
    --volume todo-mysql-data:/var/lib/mysql \
    --env MYSQL_ROOT_PASSWORD=secret \
    --env MYSQL_DATABASE=todos \
    --env LANG=C.UTF-8 \
    --name my-mysql \
    mysql:5.7 \
    --character-set-server=utf8mb4 \
    --collation-server=utf8mb4_unicode_ci
c9d83cbd2ac8941da32d8d64103223fe1c6937c9c28507c6e19ed91fca740c98
```
> 환경변수를 이용해서 MySQL 설정을 하고, user-defined bridge 네트워크를 이용했습니다.

쿠버네티스에서는 설정에 필요한 환경변수를 **ConfigMap**과 **Secret**으로 만들어서 사용해볼게요.

- **ConfigMap**으로 만들 환경변수
  - MYSQL_DATABASE
  - LANG
- **Secret**으로 만들 환경변수
  - MYSQL_ROOT_PASSWORD

<br><br><br>

ConfigMap과 Secret은 각각 아래와 같이 준비합니다.
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-config
data:
  database: "todos"
  lang: "C.UTF-8"
```
> 파일명은 **mysql-configmap.yaml**로 합니다.

<br>

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  password: c2VjcmV0
```
> 파일명은 **mysql-secret.yaml**로 합니다.

<br><br><br>

password("c2VjcmV0")는 "secret"을 base64 encoding한 것입니다.  
base64 encoding 은 아래처럼 하면 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ echo -n 'secret' | base64
c2VjcmV0
```

<br><br><br>

ConfigMap과 Secret을 위한 파일들이 준비됐으면, 다음은 pvc를 위한 파일도 하나 준비해주세요.
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```
> 파일명은 **mysql-pvc.yaml**로 합니다.

<br><br><br>

그리고, 이제 다들 아시겠지만 Service도 만들어야하니 준비해 주시구요.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: todo-mysql-svc
spec:
  type: ClusterIP
  selector:
    app: todo-mysql
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306
```
> 파일명은 **mysql-clusterip-service.yaml**로 합니다.

<br><br><br>

마지막으로 Deployment를 준비합니다.
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-mysql-deployment
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: todo-mysql
  template:
    metadata:
      labels:
        app: todo-mysql
    spec:
      containers:
      - name: todo-mysql-pod
        image: mysql:5.7
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: password
        - name: MYSQL_DATABASE
          valueFrom:
            configMapKeyRef:
              name: mysql-config
              key: database
        - name: LANG
          valueFrom:
            configMapKeyRef:
              name: mysql-config
              key: lang
        ports:
        - containerPort: 3306
        args: ["--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci"]
        volumeMounts:
          - mountPath: "/var/lib/mysql"
            name: mysql-storage
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc
```
> 파일명은 **mysql-deployment.yaml**로 합니다.

<br><br><br>

이제 하나씩 생성해줍니다.  
모두 다섯 개의 리소스를 생성합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f mysql-configmap.yaml
configmap/mysql-config created
ubuntu@ip-172-31-23-60:~$ kubectl apply -f mysql-secret.yaml
secret/mysql-secret created
ubuntu@ip-172-31-23-60:~$ kubectl apply -f mysql-pvc.yaml
persistentvolumeclaim/mysql-pvc created
ubuntu@ip-172-31-23-60:~$ kubectl apply -f mysql-clusterip-service.yaml
service/todo-mysql-svc created
ubuntu@ip-172-31-23-60:~$ kubectl apply -f mysql-deployment.yaml
deployment.apps/todo-mysql-deployment created
```
> 💻 명령어
>```bash
>kubectl apply -f mysql-configmap.yaml
>kubectl apply -f mysql-secret.yaml
>kubectl apply -f mysql-pvc.yaml
>kubectl apply -f mysql-clusterip-service.yaml
>kubectl apply -f mysql-deployment.yaml
>
>```

<br><br><br>

ConfigMap이 잘 생성됐나 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get configmaps
NAME               DATA   AGE
kube-root-ca.crt   1      4d12h
mysql-config       2      44s
ubuntu@ip-172-31-23-60:~$ kubectl describe configmaps mysql-config
Name:         mysql-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
database:
----
todos
lang:
----
C.UTF-8

BinaryData
====

Events:  <none>
```

> 💻 명령어
>```bash
>kubectl get configmaps
>```
>```bash
>kubectl describe configmaps mysql-config
>```

lang(C.UTF-8)과 database(todos)두 개의 데이터가 보입니다.

<br><br><br>

Secret도 볼게요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-dskrr   kubernetes.io/service-account-token   3      4d12h
mysql-secret          Opaque                                1      83s
ubuntu@ip-172-31-23-60:~$ kubectl describe secrets mysql-secret
Name:         mysql-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  6 bytes
```
> 💻 명령어
>```bash
>kubectl get secrets
>```
>```bash
>kubectl describe secrets mysql-secret
>```

Secret의 Data는 값이 보이지는 않네요.
Secret이니까요. -_-

<br><br><br>

Pod도 확인해보세요. (Environment, Mounts, Volumes 부분을 잘 보세요.)
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get pod
NAME                                     READY   STATUS    RESTARTS   AGE
todo-mysql-deployment-78dd847547-2b7ns   1/1     Running   0          3m2s
ubuntu@ip-172-31-23-60:~$ kubectl describe pod todo-mysql-deployment-78dd847547-2b7ns
Name:         todo-mysql-deployment-78dd847547-2b7ns
Namespace:    default
Priority:     0
Node:         ip-172-31-23-60/172.31.23.60
Start Time:   Thu, 16 Feb 2023 03:03:23 +0000
Labels:       app=todo-mysql
              pod-template-hash=78dd847547
Annotations:  <none>
Status:       Running
IP:           172.17.0.2
IPs:
  IP:           172.17.0.2
Controlled By:  ReplicaSet/todo-mysql-deployment-78dd847547
Containers:
  todo-mysql-pod:
    Container ID:  docker://cd07bdadf530178209895eba843b036360393eaa2efe70e93bd8840f626448d7
    Image:         mysql:5.7
    Image ID:      docker-pullable://mysql@sha256:8cf035b14977b26f4a47d98e85949a7dd35e641f88fc24aa4b466b36beecf9d6
    Port:          3306/TCP
    Host Port:     0/TCP
    Args:
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_unicode_ci
    State:          Running
      Started:      Thu, 16 Feb 2023 03:03:23 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      MYSQL_ROOT_PASSWORD:  <set to the key 'password' in secret 'mysql-secret'>      Optional: false
      MYSQL_DATABASE:       <set to the key 'database' of config map 'mysql-config'>  Optional: false
      LANG:                 <set to the key 'lang' of config map 'mysql-config'>      Optional: false
    Mounts:
      /var/lib/mysql from mysql-storage (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rhgg8 (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  mysql-storage:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  mysql-pvc
    ReadOnly:   false
  kube-api-access-rhgg8:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m15s  default-scheduler  Successfully assigned default/todo-mysql-deployment-78dd847547-2b7ns to ip-172-31-23-60
  Normal  Pulled     3m15s  kubelet            Container image "mysql:5.7" already present on machine
  Normal  Created    3m15s  kubelet            Created container todo-mysql-pod
  Normal  Started    3m15s  kubelet            Started container todo-mysql-pod
```

> 💻 명령어
>```bash
>kubectl get pod
>```
>```bash
>kubectl describe pod [POD-NAME]
>```
- [POD-NAME] 에는 MySQL POD의 이름을 넣어주세요.

<br><br><br>

이제 두 번재 워크로드인 **ToDo App**을 실행해볼까요?  
MySQL과 마찬가지로 ConfigMap과 Secret을 사용하고, 외부에서 접속을 해야하니 Ingress까지 만들어볼게요.

이번엔 몽땅 하나의 yaml파일로 만들어 보겠습니다.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: todo-secret
type: Opaque
data:
  mysql-user: cm9vdA==
  mysql-password: c2VjcmV0
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: todo-config
data:
  mysql-host: "todo-mysql-svc.default.svc.cluster.local"
  mysql-db: "todos"
---
apiVersion: v1
kind: Service
metadata:
  name: todo-app-svc
spec:
  type: NodePort
  selector:
    app: todo-app
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30007
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-app-deployment
  labels:
    app: todo-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app: todo-app
  template:
    metadata:
      labels:
        app: todo-app
    spec:
      containers:
      - name: todo-app
        image: [USER-NAME]/todo-app:1.0.0
        env:
        - name: MYSQL_HOST
          valueFrom:
            configMapKeyRef:
              name: todo-config
              key: mysql-host
        - name: MYSQL_DB
          valueFrom:
            configMapKeyRef:
              name: todo-config
              key: mysql-db
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: todo-secret
              key: mysql-user
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: todo-secret
              key: mysql-password
        ports:
        - containerPort: 3000
      imagePullSecrets:
      - name: regcred
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: todo-app-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: todo-app.info
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: todo-app-svc
                port:
                  number: 3000
```
> 파일명은 **todo-all.yaml**로 합니다.  
> [USER-NAME] 에는 여러분의 정보로 채워넣어 주세요. (52번째 라인 -> Deployment의 .spec.containers[0].image 입니다.)  
> - 만약 [USER-NAME]/todo-app:1.0.0가 준비가 안되어있다면, **rogallo/101-todo-app:1.0.0**을 사용해주세요.

<br>

파일이 좀 길죠?  
교재 **hands_on_files**디렉토리에 [todo-all.yaml](https://github.com/JungSangup/mspt3/blob/main/hands_on_files/ch12/todo-all.yaml)이라는 이름으로 미리 만들어 놓았으니, 그걸 사용하셔도 됩니다.

> 하나의 yaml파일 안에 여러개의 K8s [Manifest](https://kubernetes.io/docs/reference/glossary/?fundamental=true#term-manifest)를 정의할때는, `---`를 구분자로 해서 여러개를 담으면 됩니다.

<br><br><br>

생성하기 전에 한 가지 더 해줘야할 일이 있습니다.
이 과정은 Public repository의 이미지인 **rogallo/101-todo-app:1.0.0**를 사용하는 경우는 생략해도 됩니다.

컨테이너 이미지([USER-NAME]/todo-app:1.0.0)를 여러분의 private repository에서 pull해야하기 때문에, **자격증명**을 해야합니다.  
여러가지 방법이 있지만, 간단하게 [커맨드 라인에서 자격 증명을 통하여 시크릿 생성하기](https://kubernetes.io/ko/docs/tasks/configure-pod-container/pull-image-private-registry/#%EC%BB%A4%EB%A7%A8%EB%93%9C-%EB%9D%BC%EC%9D%B8%EC%97%90%EC%84%9C-%EC%9E%90%EA%B2%A9-%EC%A6%9D%EB%AA%85%EC%9D%84-%ED%86%B5%ED%95%98%EC%97%AC-%EC%8B%9C%ED%81%AC%EB%A6%BF-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)방법으로 해볼게요.

```bash
ubuntu@ip-172-31-23-60:~$ kubectl create secret docker-registry regcred --docker-server='https://index.docker.io/v1/' --docker-username='rogallo' --docker-password='XXXXXX'
secret/regcred created
```

> 💻 명령어
>```bash
>kubectl create secret docker-registry regcred --docker-server='https://index.docker.io/v1/' --docker-username='[USER-NAME]' --docker-password='[PASSWORD]'
>```
> [USER-NAME]과 [PASSWORD]는 여러분의 정보로 채워넣어 주세요.

이것도 많이 쓰이는 Secret의 용도 중 하나입니다.  
조회도 한 번 해보세요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl describe secrets regcred
Name:         regcred
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  kubernetes.io/dockerconfigjson

Data
====
.dockerconfigjson:  114 bytes
```
> 💻 명령어
>```bash
>kubectl describe secrets regcred
>```

<br><br><br>

이제 ToDo App을 생성해볼게요.  
생성은 아래처럼 한 번에 됩니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl apply -f todo-all.yaml
secret/todo-secret created
configmap/todo-config created
service/todo-app-svc created
deployment.apps/todo-app-deployment created
ingress.networking.k8s.io/todo-app-ingress created
```

> 💻 명령어
>```bash
>kubectl apply -f todo-all.yaml
>```

<br><br><br>

앞서 MySQL에서 한 것과 비슷하게 ConfigMap, Secret, Pod도 확인해보세요.  
명령어만 알려드릴게요.
> 💻 명령어
>```bash
>kubectl describe configmaps todo-config
>```
>```bash
>kubectl describe secrets todo-secret
>```
>```bash
>kubectl get pods
>```
>```bash
>kubectl describe pod [POD-NAME]
>```
> [POD-NAME] 에는 ToDo App POD중 하나의 이름을 넣어주세요.

<br><br><br>

POD의 환경변수를 확인하려면 아래처럼도 가능합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl exec -it todo-app-deployment-66764fc999-2b4zp -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=todo-app-deployment-66764fc999-2b4zp
TERM=xterm
MYSQL_USER=root
MYSQL_PASSWORD=secret
MYSQL_HOST=todo-mysql-svc.default.svc.cluster.local
MYSQL_DB=todos
TODO_APP_SVC_PORT_3000_TCP_ADDR=10.99.52.118
KUBERNETES_SERVICE_PORT_HTTPS=443
TODO_MYSQL_SVC_SERVICE_HOST=10.108.50.108
TODO_MYSQL_SVC_SERVICE_PORT=3306
TODO_MYSQL_SVC_PORT_3306_TCP_ADDR=10.108.50.108
TODO_APP_SVC_PORT_3000_TCP=tcp://10.99.52.118:3000
TODO_MYSQL_SVC_PORT_3306_TCP_PORT=3306
TODO_APP_SVC_SERVICE_PORT=3000
TODO_APP_SVC_PORT_3000_TCP_PORT=3000
KUBERNETES_PORT_443_TCP_PORT=443
TODO_APP_SVC_PORT=tcp://10.99.52.118:3000
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
TODO_MYSQL_SVC_PORT=tcp://10.108.50.108:3306
TODO_MYSQL_SVC_PORT_3306_TCP=tcp://10.108.50.108:3306
TODO_MYSQL_SVC_PORT_3306_TCP_PROTO=tcp
TODO_APP_SVC_SERVICE_HOST=10.99.52.118
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
TODO_APP_SVC_PORT_3000_TCP_PROTO=tcp
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
NODE_VERSION=10.24.1
YARN_VERSION=1.22.5
HOME=/root
```

> 💻 명령어
>```bash
>kubectl exec -it [POD-NAME] -- env
>```
> [POD-NAME] 에는 ToDo App POD중 하나의 이름을 넣어주세요.

- [kubectl exec](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec) 명령어의 사용방법은 [동작중인 컨테이너의 셸에 접근하기](https://kubernetes.io/ko/docs/tasks/debug/debug-application/get-shell-running-container/)를 참고하세요.

<br><br><br>

브라우저에서 http://todo-app.info/ 로 접속해서 테스트도 해보세요.    
ToDo App 접속을 위해서 **hosts**파일에 다음과 같이 ***todo-app.info*** 가 있어야 합니다. 없으면 추가해주세요.  
- Windows라면 **C:\Windows\System32\drivers\etc\hosts** 파일에,
- Linux계열은 **/etc/hosts** 파일에 추가하면 됩니다.
```bash
#mspt3
11.22.33.44  my-nginx.io todo-app.info
```
> 11.22.33.44 대신 여러분 AWS EC2 Instance의 **Public IPv4 address** 또는 SCP Virtual Server의 **NAT IP**를 써주세요.

![h:400](./img/k8s_todo_ingress.png)

<br><br><br>

MySQL DB의 테이블에 잘 저장됐는지도 확인해보세요.  
화면에서 입력한 내용이 **todo_items** 테이블에 저장되어 있습니다.  
⚠️ DB 테이블에 데이터가 저장된 걸 확인하기 위해서는, 위의 화면처럼 Item을 몇 개 입력해주세요.

```bash
ubuntu@ip-172-31-23-60:~$ kubectl exec -it todo-mysql-deployment-78dd847547-2b7ns -- mysql -p todos
Enter password:
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 5.7.41 MySQL Community Server (GPL)

Copyright (c) 2000, 2023, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> select * from todo_items;
+--------------------------------------+-----------------------+-----------+
| id                                   | name                  | completed |
+--------------------------------------+-----------------------+-----------+
| 4f7c3980-640d-4bec-a04a-467a55fa1de5 | 엠에스피티쓰리            |         0 |
| 2f6da7dd-859d-4a47-8991-908e239cc1b8 | mspt3                 |         0 |
+--------------------------------------+-----------------------+-----------+
2 rows in set (0.01 sec)

mysql> exit;
Bye
```

> 💻 명령어
>```bash
>kubectl exec -it [POD-NAME] -- mysql -p todos
>```
> [POD-NAME] 에는 MySQL POD의 이름을 넣어주세요.  
> mysql의 root password 는 'secret' 입니다.

<br><br><br>

이것저것 확인해보시고, 마지막은 생성된 리소스들을 정리해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete -f todo-all.yaml
secret "todo-secret" deleted
configmap "todo-config" deleted
service "todo-app-svc" deleted
deployment.apps "todo-app-deployment" deleted
ingress.networking.k8s.io "todo-app-ingress" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete secret regcred
secret "regcred" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete -f mysql-deployment.yaml
deployment.apps "todo-mysql-deployment" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete -f mysql-clusterip-service.yaml
service "todo-mysql-svc" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete -f mysql-pvc.yaml
persistentvolumeclaim "mysql-pvc" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete -f mysql-secret.yaml
secret "mysql-secret" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete -f mysql-configmap.yaml
configmap "mysql-config" deleted
```
> 💻 명령어
>```bash
>kubectl delete -f todo-all.yaml
>kubectl delete secret regcred
>kubectl delete -f mysql-deployment.yaml
>kubectl delete -f mysql-clusterip-service.yaml
>kubectl delete -f mysql-pvc.yaml
>kubectl delete -f mysql-secret.yaml
>kubectl delete -f mysql-configmap.yaml
>
>```

<br>

정리 후 상태는 아래와 같습니다. (service/kubernetes, kube-root-ca.crt, default-token-OOOOO 는 시스템에서 사용하는 리소스입니다.)
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get ingress
No resources found in default namespace.
ubuntu@ip-172-31-23-60:~$ kubectl get pvc
No resources found in default namespace.
ubuntu@ip-172-31-23-60:~$ kubectl get pv
No resources found
ubuntu@ip-172-31-23-60:~$ kubectl get configmaps
NAME               DATA   AGE
kube-root-ca.crt   1      12m
ubuntu@ip-172-31-23-60:~$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-bx5vw   kubernetes.io/service-account-token   3      12m
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   7m35s
```

> 💻 명령어
>```bash
>kubectl get ingress
>```
>```bash
>kubectl get pvc
>```
>```bash
>kubectl get pv
>```
>```bash
>kubectl get configmaps
>```
>```bash
>kubectl get secrets
>```
>```bash
>kubectl get all
>```

<br>

이번 실습은 여기까지 입니다.   ＿〆(。╹‿ ╹ 。)
