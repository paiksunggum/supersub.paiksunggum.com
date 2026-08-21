---
name: deploy
description: |
  cloud.paiksunggum 백엔드를 EC2 프로덕션에 배포합니다.
  로컬에서 amd64 이미지를 빌드·푸시하고, EC2에서 pull·재기동·prune까지 수행합니다.
argument-hint: "[api|infra|check]"
disable-model-invocation: true   # 배포는 사용자가 명시적으로 시작한다. Claude 자동 실행 금지
user-invocable: true             # /deploy 로 호출
allowed-tools:
  - Bash
  - Read
---

# EC2 프로덕션 배포

**EC2(3.35.114.50)가 유일한 프로덕션 백엔드다.** 학원 데스크탑 스택은 2026-07-28부로 내려갔다.
프론트엔드는 Vercel이 `main` 푸시를 감지해 자동 배포하므로 이 스킬의 대상이 아니다.

접속은 `ssh aws` (ec2-user). EC2는 AI가 SSH로 직접 작업해도 되는 대상이다
(학원 데스크탑 원격 편집 금지 규칙의 예외).

## 인자

| 인자 | 대상 |
|------|------|
| `api` (기본) | `api/` 코드 변경 → 이미지 빌드·푸시 → EC2 재기동 |
| `infra` | `docker-compose.yaml` · `nginx/` 변경 → EC2에서 git pull 후 반영 |
| `check` | 배포하지 않고 현재 프로덕션 상태만 점검 |

인자가 없으면 `git status`로 변경 파일을 보고 어느 쪽인지 판단한 뒤, **사용자에게 확인받고** 진행한다.

---

## 1. 사전 점검 (배포 전 필수)

```bash
cd api
ruff check . --fix
ruff format .
pytest -m "not ollama"
```

- 린트 에러나 테스트 실패가 있으면 **배포하지 않는다.** 먼저 고치고 사용자에게 보고한다.
- `ollama` 마커 테스트는 모델 서버가 필요하므로 항상 제외한다.

이어서 배포 대상이 맞는지 확인한다.

```bash
git branch --show-current    # main 이어야 한다
git status --porcelain       # 커밋 안 된 변경이 이미지에 안 들어갈 수 있음
```

`main`이 아니거나 미커밋 변경이 있으면 그대로 진행하지 말고 사용자에게 알린다.

## 2. 배포 — `api`

코드는 이미지에 baked 되므로 **EC2에서 git pull 할 필요가 없다.**

```bash
# ① 로컬에서 amd64 이미지 빌드 + 푸시 (맥은 arm64라 --platform 필수)
docker buildx build \
  --platform linux/amd64 \
  --provenance=false \
  -t paiksunggum/paik-api:latest \
  --push \
  api/

# ② EC2에서 pull → 재기동 → 이미지 정리
ssh aws 'cd ~/cloud.paiksunggum && \
  docker compose pull api && \
  docker compose up -d api auth && \
  docker image prune -f'
```

**주의할 점**

- `--platform linux/amd64` 를 빠뜨리면 EC2에서 `exec format error`로 죽는다.
- `--provenance=false` 가 없으면 Docker Hub에 manifest list가 올라가 pull이 꼬인다.
- `auth`도 **같은 이미지**(`paik-api`)를 entrypoint만 바꿔 쓴다. api만 재기동하면 auth는 옛 코드로 남는다.
- `docker image prune -f` 는 매 배포마다 실행한다. 디스크 30GB에서 dangling 이미지가 빠르게 쌓인다.
- 빌드·푸시는 수 분 걸린다. 침묵하지 말고 진행 상황을 사용자에게 알린다.

## 3. 배포 — `infra`

`docker-compose.yaml`이나 `nginx/conf.d/`를 고쳤을 때만 해당한다. 이때는 EC2가 파일을 직접 읽으므로 git pull이 필요하다.

```bash
# 로컬에서 커밋·푸시가 끝난 뒤
ssh aws 'cd ~/cloud.paiksunggum && git pull'

# compose 변경 시
ssh aws 'cd ~/cloud.paiksunggum && docker compose up -d'

# nginx 변경 시 — 문법 검사 후 reload
ssh aws 'sudo nginx -t && sudo systemctl reload nginx'
```

`nginx -t` 가 실패하면 **reload 하지 않는다.** 잘못된 설정으로 reload 하면 전체 도메인이 죽는다.

## 4. 검증 (배포 후 필수)

```bash
# 컨테이너 상태 — api·auth 모두 healthy 여야 한다
ssh aws 'cd ~/cloud.paiksunggum && docker compose ps'

# 외부에서 실제 응답 확인
curl -s -o /dev/null -w "api   %{http_code}\n" https://api.paiksunggum.com/
curl -s -o /dev/null -w "auth  %{http_code}\n" https://auth.paiksunggum.com/healthz
curl -s -o /dev/null -w "front %{http_code}\n" https://paiksunggum.com/

# 최근 로그에 예외가 없는지
ssh aws 'cd ~/cloud.paiksunggum && docker compose logs --tail=50 api auth'
```

`ec2.paiksunggum.com`은 `api.paiksunggum.com`과 동일한 백엔드의 별칭이다(롤백 보험용).

## 5. 롤백

| 상황 | 조치 |
|------|------|
| 새 이미지가 문제 | 직전 이미지 태그로 `docker compose up -d api auth` |
| 프론트가 백엔드를 못 찾음 | Vercel `BACKEND_URL`을 이전 값으로 되돌리고 Redeploy |
| nginx 설정 문제 | EC2에서 `git checkout` 후 `sudo nginx -t && sudo systemctl reload nginx` |

`:latest` 하나만 쓰고 있어 이미지 롤백은 로컬에서 이전 커밋을 다시 빌드·푸시하는 방식이 된다.
빠른 롤백이 필요하면 배포 전 태그를 하나 더 붙여두는 것을 검토한다.

---

## 하지 않는 것

- **사용자 요청 없이 커밋·푸시하지 않는다.** 배포는 이미 푸시된 코드를 대상으로 한다.
- 사전 점검이 실패한 상태로 배포를 강행하지 않는다.
- `.env` · `.env.auth` 를 읽거나 화면에 출력하지 않는다. EC2에만 있어야 하는 값이다.
- Qwen 의존 기능(`her`의 RAG, `star_craft` 한국어 번역)이 안 된다고 고치려 들지 않는다. 의도된 비활성 상태다.