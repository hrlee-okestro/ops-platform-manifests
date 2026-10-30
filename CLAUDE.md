# ops-platform-manifests

ops-platform의 GitOps 매니페스트 저장소. ArgoCD가 이 저장소를 감시해 클러스터에 반영한다.
앱 코드와 전체 계획은 `../ops-platform` (CLAUDE.md, docs/PLAN.md)를 따른다. 아래는 이 저장소에서 특히 중요한 규칙이다.

## 안전 규칙 (최우선)

- **Claude는 클러스터에 읽기 작업만 한다.** apply, create, helm install/upgrade, ArgoCD 설치 등 쓰기/실행은 사용자가 직접 한다. Claude는 매니페스트를 작성하고 실행할 명령어와 이유를 전달만 한다. (자세한 기준은 `../ops-platform/CLAUDE.md` 참고)

- 이 저장소의 매니페스트는 **`argocd`, `ops-platform`, `monitoring` 네임스페이스에만** 리소스를 만든다.
- 기존 클러스터 리소스(boot-factory, contrabass, dataplatform, viola, ingress-nginx, istio-system, kube-system, observability-agent, default 등)를 수정/삭제하는 매니페스트를 **절대 작성하지 않는다.**
- ArgoCD Application/AppProject는 **대상 네임스페이스를 위 세 개로 제한**한다 (AppProject의 destinations 화이트리스트). 기본 `default` 프로젝트를 쓰지 않는다.
- 자동 prune/selfHeal을 켜기 전에는 prune 범위가 우리 리소스에 한정되는지 확인하고 사용자 승인을 받는다.
- CRD, ClusterRole 등 클러스터 범위 리소스가 포함된 변경은 내용을 보여주고 사용자 승인을 받는다.
- 모든 워크로드에 resources requests/limits를 둔다. Istio injection 라벨은 붙이지 않는다.

## 명령어 전달 형식

사용자에게 명령을 전달할 때는 코드 블록마다 실행 환경 라벨(맥·저장소 경로 / 맥·경로 무관 / deploy 서버)을 붙인다. 이 저장소의 YAML 적용은 맥의 `~/projects/ops-platform-manifests`에서 `ssh root@<deploy-ip> 'kubectl apply -f -' < 파일` 방식으로 한다. 자세한 규칙은 `../ops-platform/CLAUDE.md`를 따른다.

## 공개 저장소 규칙

- 내부 IP, 호스트명, 도메인, 비밀번호, 토큰, kubeconfig를 커밋하지 않는다. 플레이스홀더(`<INGRESS_HOST>` 등)를 쓴다.
- 시크릿 값은 매니페스트에 넣지 않는다 (Sealed Secrets/External Secrets 또는 수동 생성 후 참조).
- **커밋과 push는 사용자가 직접 한다. Claude는 하지 않는다.** 규칙은 `../ops-platform/docs/GIT_CONVENTIONS.md`를 따른다.
- `bootstrap/`은 ArgoCD가 관리하지 않는 수동 적용용 리소스다 (읽기 전용 RBAC 등). Application이 이 디렉토리를 가리키게 하지 않는다.

## 구조 (예정)

```
apps/        ArgoCD Application 정의 (App of Apps)
charts/      Helm 차트 또는 overlays/ (Kustomize)
env/         환경별 값 (민감 정보 제외)
```
