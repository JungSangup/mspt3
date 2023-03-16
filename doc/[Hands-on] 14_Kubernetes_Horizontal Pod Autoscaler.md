
# [Hands-on] 14. Kubernetes Horizontal Pod Autoscaler

![](./img/hands_on.png)

<br>

# Contents

**[1. HPA를 사용해서 자동 스케일링 해보기](#1-hpa를-사용해서-자동-스케일링-해보기)**

💾실습파일 위치 : [hands_on_files/ch14](../hands_on_files/ch14)

미리 준비된 실습용 파일을 사용하려는 경우 아래 디렉토리 이동 명령어를 실행해주세요.
```bash
ubuntu@ip-172-31-23-60:~$ cd ~/mspt3/hands_on_files/ch14
ubuntu@ip-172-31-23-60:~/mspt3/hands_on_files/ch14$
```

> 💻 명령어
>```bash
>cd ~/mspt3/hands_on_files/ch14
>```

---

## 1. HPA를 사용해서 자동 스케일링 해보기

Horizontal Pod Autoscaler(HPA)를 이용하여 자동으로 Pod의 개수를 조절하는 실습입니다.

실습 내용은 [HorizontalPodAutoscaler Walkthrough](https://kubernetes.io/ko/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/) 를 기반으로 하였습니다.

먼저 자원 모니터링을 위한 metrics-server가 필요합니다.
우리는 앞서 Minikube의 Metrics-Server Addon을 Enable시켰기 때문에 따로 해줄일은 없습니다.

정상적으로 Addon이 Enable된 경우 아래와 같이 확인 가능합니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl top node
NAME              CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
ip-172-31-23-60   121m         6%     1921Mi          49%
```

> 💻 명령어
>```bash
>kubectl top node
>```

<br><br><br>

이제 준비가 됐으면, 다음 명령어를 실행하여 간단한 테스트용 Pod 를 준비합니다.
```yaml
ubuntu@ip-172-31-23-60:~$ kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
deployment.apps/php-apache created
service/php-apache created
```

> 💻 명령어
>```bash
>kubectl apply -f https://k8s.io/examples/application/php-apache.yaml
>```
> Deployment와 Service가 만들어집니다.

<br><br><br>

이제 **hpa**를 생성합니다.

명령어는 다음과 같습니다.  
CPU 사용량을 50%로 유지하기 위해서 Pod의 개수를 1 에서 10 사이로 조정하라는 의미입니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
horizontalpodautoscaler.autoscaling/php-apache autoscaled
```

> 💻 명령어
>```bash
>kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
>```

<br><br><br>

잘 만들어졌나 볼까요?
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get hpa
NAME         REFERENCE               TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   <unknown>/50%   1         10        1          27s
```

> 💻 명령어
>```bash
>kubectl get hpa
>```

<br><br><br>

다음은 먼저 생성한 Pod에 부하를 줄 도우미 친구 입니다.

간단한 sh 명령어를 실행할 pod(load-generator)를 만들어서 반복문을 실행합니다.  
앞에서 만든 Pod에 계속 요청(http request)을 보내서 CPU 사용율을 높이게 됩니다.

시스템에 사용자가 늘어난 상황을 비슷하게 만든거라고 보시면 됩니다.

```bash
ubuntu@ip-172-31-23-60:~$ kubectl run -it load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!OK!
```

> 💻 명령어
>```bash
>kubectl run -it load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
>```

<br><br><br>

이제 터미널을 더 열고 아래 명령어를 실행해서 어떤 변화가 있는지 알아봅니다.  
가능하면 두 개의 터미널을 더 열어두고 아래 두 가지를 같이 확인해보면 좋습니다.
```bash
Every 1.0s: kubectl get hpa                             ip-172-31-28-216: Mon Mar  6 13:15:05 2023

NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   71%/50%   1         10        6          4m13s
```

> 💻 명령어 (Terminal 2)
>```bash
>watch -n 1 kubectl get hpa
>```
> watch 명령어는 Ctrl + c로 중단할 수 있습니다.

<br>

```bash
Every 1.0s: kubectl top pod                             ip-172-31-28-216: Mon Mar  6 13:15:05 2023

NAME                          CPU(cores)   MEMORY(bytes)
load-generator                8m           0Mi
php-apache-7d665c4ddf-2b8rf   122m         11Mi
php-apache-7d665c4ddf-5r24h   121m         11Mi
php-apache-7d665c4ddf-cxbhb   172m         11Mi
php-apache-7d665c4ddf-l6lmv   158m         11Mi
```

> 💻 명령어 (Terminal 3)
>```bash
>watch -n 1 kubectl top pod
>```
> watch 명령어는 Ctrl + c로 중단할 수 있습니다.


<br><br><br>

어느정도 시간이 지나서, Pod가 늘어나는걸 보셨으면, 첫 번째 Terminal의 반복문을 중지해주세요.  
Ctrl + c로 중지하시면 됩니다.

부하를 중지하면 다시 Pod의 수가 줄어드는것도 확인할 수 있습니다.

다른 터미널의 watch명령어도 Ctrl + c로 중지해주세요.

<br><br><br>

마지막으로 사용된 HPA와 Pod를 삭제하고 마치겠습니다.
```bash
ubuntu@ip-172-31-23-60:~$ kubectl delete hpa php-apache
horizontalpodautoscaler.autoscaling "php-apache" deleted
ubuntu@ip-172-31-23-60:~$ kubectl delete -f https://k8s.io/examples/application/php-apache.yaml
deployment.apps "php-apache" deleted
service "php-apache" deleted
```

> 💻 명령어
>```bash
>kubectl delete hpa php-apache
>```
>```bash
>kubectl delete -f https://k8s.io/examples/application/php-apache.yaml
>```

<br>

정리 후 상태는 아래와 같습니다. (service/kubernetes 는 시스템에서 사용하는 리소스입니다.)
```bash
ubuntu@ip-172-31-23-60:~$ kubectl get hpa
No resources found in default namespace.
ubuntu@ip-172-31-23-60:~$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   125m
```

> 💻 명령어
>```bash
>kubectl get hpa
>```
>```bash
>kubectl get all
>```

<br>

끝~~~  ＿〆(。╹‿ ╹ 。)
