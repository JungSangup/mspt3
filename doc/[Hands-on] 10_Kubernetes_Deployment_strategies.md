---
marp: true
author: sangup.jung@gmail.com
size: 16:9
theme: mspt2
paginate: true
header: Docker & Kubernetes - [Hands-on] 10. Kubernetes Deployment strategies
footer: Samsung SDS
---

## [Hands-on] 10. Kubernetes Deployment strategies

이번 실습은 Deployment의 업데이트 방법 두 가지를 비교해보는 실습입니다.

먼저 실습실로 이동할게요.
cd /root/lab{{execute T1}}

그리고, 이번 실습은 Terminal이 하나 더 필요합니다.
Terminal 탭 옆의 +버튼을 클릭해서 하나 더 준비해주세요.

첫 번째는 Recreate 입니다.
말 그대로 다시 생성하는 방법입니다. 기존에 서비스되고 있던 Pod들을 모두 정지하고, 새로운 Pod를 실행하는거죠.

nginx Pod를 준비할게요.
kubectl apply -f nginx-recreate.yaml{{execute T1}}

kubectl get all{{execute T1}}

우리가 생성한 nginx 버젼을 알아볼까요?
kubectl describe deployment nginx-deployment | grep -i image{{execute T1}}

---

이제 버젼을 변경합니다.
앞에서 배웠으니 선언형(Declarative)으로 해볼게요.
yaml파일의 버젼부분을 수정합니다. (1.18 -> 1.19)
sed -i 's/image: nginx:1.18/image: nginx:1.19/g' nginx-recreate.yaml{{execute T1}}

그리고, Pod들이 어떻게 변하는지 살펴보기 위해서 다음 명령어를 실행해주세요.
Terminal2에서 실행되게 해 놓았습니다.
kubectl get pods --watch{{execute T2}}

그리고 변경된 yaml파일을 이용해서 업데이트를 합니다.
kubectl apply -f nginx-recreate.yaml{{execute T1}}

Terminal2에서 어떤 일이 일어나는지 유심히 보세요.

아마도, 있던 Pod들이 모두 삭제되고 새로운 Pod들이 생길거예요.

Deployment에 어떤 변화가 생겼나 볼까요?
kubectl describe deployment nginx-deployment | grep -i image{{execute T1}}

그리고, 새로 생성된 Pod도 한번 보구요. kubectl describe pod [POD-NAME] | grep -i image

어떤가요? 업데이트가 잘 이루어졌나요?

---

업데이트에 문제가 생기면 롤백도 할 수 있습니다.

History 도 목록형태로 볼 수 있구요.
kubectl rollout history deployment nginx-deployment{{execute T1}}

Revision을 콕 집어서 자세히 볼 수도 있습니다.
kubectl rollout history deployment nginx-deployment --revision=1{{execute T1}}

kubectl rollout history deployment nginx-deployment --revision=2{{execute T1}}

역시 Terminal2에 어떤 변화가 일어날지 모니터할 준비를 하고,
kubectl get pods --watch{{execute T2}}

revision1으로 롤백 합니다.
kubectl rollout undo deployment nginx-deployment --to-revision=1{{execute T1}}

이전 버젼으로 롤백이 잘 됐는지 아래 명령어로 확인해보세요.
kubectl describe pod [POD-NAME] | grep -i image

다 해보셨으면 다음 실습을 위해 삭제해주세요.
아시죠? 선언형(Declarative)...
kubectl delete -f nginx-recreate.yaml{{execute T1}}

---

---

혹시 이전 실습에서 경로를 왔다갔다 하셨으면 실습실로 다시 이동하시구요.
cd /root/lab{{execute T1}}

이번엔 RollingUpdate 입니다.

Terminal도 깨끗이 정리하고 하죠.
clear{{execute T1}}

clear{{execute T2}}

테스트를 위한 애플리케이션을 준비하구요.
kubectl apply -f nginx-rollingupdate.yaml{{execute T1}}

kubectl get all{{execute T1}}

생성된 Deployment의 정보를 보고 현재 실행된 이미지를 확인해봅니다.
kubectl describe deployment nginx-deployment | grep -i image{{execute T1}}

버젼을 변경하구요.
sed -i 's/image: nginx:1.18/image: nginx:1.19/g' nginx-rollingupdate.yaml{{execute T1}}

확인할 명령어를 실행한 후에
kubectl get pods --watch{{execute T2}}

업데이트를 합니다.
kubectl apply -f nginx-rollingupdate.yaml{{execute T1}}

---

deployment에 잘 반영이 됐나요?
kubectl describe deployment nginx-deployment | grep -i image{{execute T1}}

새로 생성된 Pod의 정보도 확인해봅니다.
kubectl describe pod [POD-NAME] | grep -i image

앞에서와 마찬가지로 롤백도 해보세요.. 자세한 설명은 생략합니다.

kubectl rollout history deployment nginx-deployment{{execute T1}}

kubectl get pods --watch{{execute T2}}

kubectl rollout undo deployment nginx-deployment --to-revision=1{{execute T1}}

kubectl describe pod [POD-NAME] | grep -i image

kubectl delete -f nginx-rollingupdate.yaml{{execute T1}}