

# Docker & Kubernetes 노트 (MSP T2 8차)   (oﾟvﾟ)ノ

## Q&A

### Docker

- 회사에서 사용하는 VDI가 버츄얼 머신이고, sac같은것이 컨테이너일까요?
  - 네, VDI가 버츄얼머신 형태입니다.
- 리눅스에 docker 설치 시 windows에서와 같이 가상 OS(Linux kernel)가 별도로 있는 상태에서 container가 올라가나요?
  - 리눅스에서는 가상OS를 별도로 생성하지는 않고, 커널을 직접 사용합니다.
- 사내에서 개발환경으로 docker를 구성하려면 어떻게 해야하나요?
  - 1. WSL2나 Hyper-V, Virtualbox같은 가상 환경에 리눅스를 구성한 다음, [Install Docker Engine](https://docs.docker.com/engine/install/)의 방법을 적용하시면 됩니다.
  - 2. 또는 Docker desktop for windows를 비용을 지불하고 사용하는 것도 방법입니다. 

---

### Kubernetes
