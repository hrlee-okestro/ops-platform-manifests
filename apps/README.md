# apps/

App of Apps의 자식 Application 정의를 두는 디렉토리. root Application(`bootstrap/root-app.yaml`)이 이 디렉토리를 감시한다.

- 여기에 ArgoCD `Application` YAML을 추가하면 PR 머지 후 자동으로 생성/동기화된다.
- 모든 Application은 `project: ops-platform`을 사용한다 (기본 `default` 프로젝트 금지).
- 배포 대상 네임스페이스는 `argocd`, `ops-platform`, `monitoring`만 가능하다 (AppProject가 그 밖의 대상은 거부한다).
- ArgoCD는 `.yaml`/`.yml`/`.json`만 읽으므로 이 README는 배포 대상이 아니다.
