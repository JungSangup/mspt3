# Docker & Kubernetes 노트 (MSP T2 10차)   (oﾟvﾟ)ノ
## Q&A

### Docker

- Docker build를 했는데 교재/강사화면과 다르네요. 왜 그런가요? ( FROM docker.io/library/node:10-alpine ... )
  - 빌드하는 시점/환경에 따라 다를 수 있습니다.
  - 뒤에 배우겠지만, 레이어에 해당하는 것이 호스트 머신에 있을 때와 없을 때 차이가 있을 수 있습니다.
