# 메모리 디렉터리 위치 규칙

> 이 파일은 **설명 문서**다. 메모리 자체를 여기에 두지 않는다. 이유는 마지막 절 참고.
> 짝 문서: [[auto-memory]] — 저장된 메모리가 **언제 어떻게 로드되는지**(200줄 규칙, 활성화 설정).

## 실제 위치

세션 시작 시 자동 로드되는 메모리는 저장소 안이 아니라 **홈 디렉터리** 아래에 있다.

```
~/.claude/projects/<인코딩된 프로젝트 경로>/memory/
```

이 프로젝트의 실제 경로:

```
/Users/psg/.claude/projects/-Users-psg-Documents-cloud-paiksunggum/memory/
├── MEMORY.md                    ← 인덱스. 세션 시작 시 이것만 자동 로드됨
├── project_overview.md
├── project_ec2_migration.md
├── feedback_local_only_ai_edits.md
└── ...                          ← 주제별 메모리 파일
```

## 프로젝트 경로 인코딩 규칙

디렉터리 이름은 **프로젝트 절대경로의 구분자를 전부 `-`로 바꾼 것**이다.
슬래시(`/`)뿐 아니라 **점(`.`)도 `-`로 바뀐다.**

```
/Users/psg/Documents/cloud.paiksunggum
        ↓  / → -,  . → -
-Users-psg-Documents-cloud-paiksunggum
```

같은 머신의 다른 프로젝트들도 동일 규칙을 따른다.

| 프로젝트 경로 | 메모리 디렉터리 |
|---|---|
| `/Users/psg/Documents/cloud.paiksunggum` | `-Users-psg-Documents-cloud-paiksunggum` |
| `/Users/psg/Documents/com.paiksunggum` | `-Users-psg-Documents-com-paiksunggum` |
| `/Users/psg/Documents/com.paiksg` | `-Users-psg-Documents-com-paiksg` |

**프로젝트별로 메모리가 분리된다.** `cloud.paiksunggum`에서 저장한 메모리는 `com.paiksunggum`
세션에서 보이지 않는다. 디렉터리가 다르기 때문이다.

## 파일 규약

**1 파일 = 1 사실.** 주제별로 뭉치지 않고 사실 단위로 쪼갠다.

```markdown
---
name: <kebab-case-슬러그>
description: <한 줄 요약 — 이 줄로 관련성을 판단하므로 구체적으로>
metadata:
  type: user | feedback | project | reference
---

<본문. feedback·project 타입은 **Why:** / **How to apply:** 를 이어 쓴다>
관련 메모리는 [[다른-메모리-이름]] 으로 연결한다.
```

| type | 용도 |
|------|------|
| `user` | 사용자가 누구인지 (역할·숙련도·선호) |
| `feedback` | 작업 방식에 대한 지시·교정. **왜 그런지 이유를 반드시 포함** |
| `project` | 코드나 git 이력에서 유추할 수 없는 진행 상황·목표·제약. 상대 날짜는 절대 날짜로 |
| `reference` | 외부 자원 포인터 (URL·대시보드·티켓) |

## MEMORY.md 인덱스가 핵심이다

세션 시작 시 컨텍스트에 자동으로 들어오는 것은 **`MEMORY.md` 한 줄 요약뿐**이다.
개별 파일은 필요할 때 열어서 읽는다.

```markdown
- [제목](파일명.md) — 한 줄 요약(hook)
```

**파일만 만들고 인덱스에 추가하지 않으면 그 메모리는 사실상 존재하지 않는다.**
세션 시작 시 로드되지 않아 영영 참조되지 않는다.
(실제로 `project_instructor_driven_experiments.md`가 이 상태로 5일간 방치돼 있었다 — 2026-07-29 수리 완료)

인덱스에는 **포인터만** 넣는다. 메모리 본문을 `MEMORY.md`에 쓰지 않는다.

## 저장하지 말아야 할 것

- 저장소가 이미 기록하는 것 — 코드 구조, 과거 수정 이력, git 로그, `CLAUDE.md` 내용
- 현재 대화에서만 의미 있는 것

저장 전에 **같은 내용을 다루는 기존 파일이 있는지 먼저 확인**한다. 중복 생성 대신 갱신한다.
틀린 것으로 판명된 메모리는 삭제한다.

## 왜 저장소 안에 복제하지 않는가

`.claude/rules/projects/` 아래에 메모리 파일을 복사해 두고 싶을 수 있으나, 하지 않는다.

1. **로드되지 않는다.** 세션 시작 시 읽히는 것은 `~/.claude/projects/.../memory/MEMORY.md`뿐이다.
   저장소 안의 사본은 아무도 읽지 않는 죽은 파일이 된다.
2. **원본이 둘이 된다.** 한쪽만 갱신되면서 갈라지고, 어느 쪽이 맞는지 알 수 없게 된다.
3. **커밋되면 곤란한 내용이 섞인다.** 메모리에는 서버 IP·운영 절차·개인 작업 습관이 들어 있다.
   공개 저장소(`github.com/paiksunggum/cloud.paiksunggum`)에 올라갈 위치가 아니다.

메모리를 다른 컴퓨터와 공유해야 한다면 저장소 커밋이 아니라 `~/.claude` 쪽을 직접 동기화한다.