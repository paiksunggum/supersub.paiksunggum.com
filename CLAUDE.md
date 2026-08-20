# CLAUDE.md

이 저장소는 **제안요청서 기반 개발 결과 보고서**를 Jekyll 정적 사이트로 문서화하는 프로젝트다.
블로그가 아니라 **문서(보고서)** 사이트다. 작업 전 이 문서를 먼저 읽는다.

---

## 1. 프로젝트 정보

| 항목 | 값 |
|---|---|
| 사업명 | **미정** — 확정 전까지 `(사업명 미정)` 유지 |
| 발주처 | 한국소프트웨어진흥원 |
| 원문 | 영상자료디지털화사업 개발물 DB 영문화작업 제안요청서 |
| 개발 기간 | 2026년 8월 20일 ~ 2026년 10월 27일 (10주) |
| 개발 인원 | 백성검, 박민호, 정상호, 정어진 (4명) |

공개 주소: <https://paiksunggum.github.io/demo.paiksunggum.com/>
저장소: <https://github.com/paiksunggum/demo.paiksunggum.com>

---

## 2. 환경

- Ruby **3.3.12** (rbenv, `rbenv global`로 지정됨)
- Jekyll **4.4.1** / Bundler 4.0.19 / 테마 `minima` 2.5.2
- 셸은 zsh. `~/.zshrc`에 `eval "$(rbenv init - zsh)"` 등록됨

### 로컬 서버

```bash
cd ~/project/demo-paiksunggum
bundle exec jekyll serve --host 0.0.0.0 --port 4000 --baseurl ""
```

**`--baseurl ""`는 반드시 붙인다.** `_config.yml`의 `baseurl`은 GitHub Pages
하위 경로(`/demo.paiksunggum.com`)용이라, 이 옵션 없이 로컬에서 띄우면
CSS와 링크가 전부 404가 난다.

`_config.yml`을 수정했을 때만 서버 재시작이 필요하다. 나머지 파일은 저장 시 자동 반영된다.

---

## 3. 배포

`main` 브랜치에 push하면 GitHub Actions(`.github/workflows/jekyll.yml`)가
자동으로 빌드·배포한다. 1~2분 소요. **저장만으로는 배포되지 않는다.**

```bash
git add -A && git commit -m "메시지" && git push
```

- Pages 빌드 방식은 `workflow`. Settings→Pages를 건드리지 않는다.
- 워크플로가 `--baseurl`을 자동으로 넘기므로 `_config.yml`의 `baseurl`을 배포용으로 바꿀 필요 없다.
- 배포 확인: `gh run list --workflow=jekyll.yml --limit 1`

**커밋 전 반드시 로컬에서 확인한다.** push하면 즉시 공개된다.

---

## 4. 파일 구조

```
_config.yml           사이트 설정 + 보고서 메타정보(report 변수)
index.markdown        1페이지 — 표지
toc.markdown          2페이지 — 목차 (/toc/)
assets/main.scss      표지·목차 스타일 (상단 빈 프론트매터 필수)
_posts/               블로그용. 이 프로젝트에선 사용하지 않음
about.markdown        Jekyll 기본값 그대로 — 미정리
404.html
```

---

## 5. 작성 규칙

### 5.1 보고서 메타정보는 하드코딩하지 않는다

사업명·기간·인원은 `_config.yml`의 `report` 변수에만 둔다.
페이지에서는 반드시 변수로 참조한다.

```liquid
{{ site.report.project_name }}
{{ site.report.period_start }} ~ {{ site.report.period_end }}
{% for m in site.report.members %}{{ m }}{% unless forloop.last %} · {% endunless %}{% endfor %}
```

사업명이 확정되면 `_config.yml`의 `project_name` 한 줄만 고친다.
페이지 본문에 사업명을 직접 써 넣으면 나중에 전부 찾아 고쳐야 하므로 금지한다.

### 5.2 내부 링크는 `relative_url`을 통과시킨다

```liquid
<a href="{{ '/toc/' | relative_url }}">목차</a>   <!-- O -->
<a href="/toc/">목차</a>                          <!-- X: 배포 시 404 -->
```

`baseurl` 때문에 로컬과 배포 경로가 다르다. 이미지·CSS 등 모든 자산에 동일하게 적용한다.

### 5.3 새 장(章) 페이지 작성 형식

목차의 각 장은 `_posts/`가 아니라 **루트의 독립 페이지**로 만든다.
파일명은 `ch01-사업개요.markdown` 형식, permalink는 `/ch01/` 형식으로 통일한다.

```markdown
---
layout: page
title: 1. 영상자료 디지털화 사업개요
permalink: /ch01/
chapter: 1
---
```

- `title`은 목차(`toc.markdown`)의 문구와 **정확히 일치**시킨다.
- 장을 추가하면 `toc.markdown`의 해당 항목에 링크를 건다. 목차와 실제 페이지가 어긋나지 않게 한다.
- 본문 제목 레벨은 `##`(절), `###`(항)을 쓴다. `#`은 페이지 제목이 이미 차지하므로 쓰지 않는다.

### 5.4 목차 구조

`toc.markdown`이 문서 구조의 기준이다. 임의로 장 번호를 바꾸지 않는다.

- **I 부. 제안요청 내용** (1~4장) — 제안요청서 원문 구조. 항목을 임의로 추가·삭제하지 않는다.
- **II 부. 개발 수행 내용** (5~9장) — 실제 개발 내용. 필요 시 조정 가능하되 사용자에게 먼저 확인한다.

### 5.5 스타일

`assets/main.scss`에 추가한다. 파일 맨 위 빈 프론트매터(`---` 두 줄)와
`@import "minima";`를 지우면 CSS가 통째로 깨지므로 건드리지 않는다.

한글 제목은 `word-break: keep-all;`을 적용해 단어 중간에서 줄바꿈되지 않게 한다.

### 5.6 문체

- 본문은 **평서형 종결(~한다, ~이다)**. 보고서 문체를 유지한다.
- 표는 마크다운 표를 쓴다.
- 존댓말·구어체·이모지는 본문에 쓰지 않는다.

---

## 6. 진행 상황

### 완료

- [x] rbenv + Ruby 3.3.12 + Jekyll 4.4.1 설치 (macOS)
- [x] Jekyll 사이트 생성
- [x] GitHub Pages 배포 (Actions 워크플로)
- [x] 1페이지 표지
- [x] 2페이지 목차 (I부 4장 + II부 5장)

### 미완료

- [ ] **사업명 확정** — `_config.yml`의 `project_name`
- [ ] 1~4장 본문 (제안요청 내용)
- [ ] 5~9장 본문 (개발 수행 내용)
- [ ] `about.markdown` — Jekyll 기본 문구 그대로. 보고서 사이트에 안 맞음
- [ ] `_posts/2026-08-20-welcome-to-jekyll.markdown` — Jekyll 샘플 글. 삭제 대상
- [ ] `README.md` — 빈 파일
- [ ] 커스텀 도메인 `demo.paiksunggum.com` 연결 (미정) — 연결 시 `baseurl`을 비워야 함

---

## 7. 주의사항

- **`_site/`는 빌드 산출물이다.** 직접 수정하지 않는다. `.gitignore`에 포함되어 있다.
- **push = 공개 배포다.** 작성 중인 초안은 로컬에서만 확인한다.
- `bundle exec` 없이 `jekyll` 명령을 직접 쓰지 않는다. Gemfile에 고정된 버전과 어긋난다.
- Sass의 `@import` 관련 deprecation 경고는 minima 테마가 구식 문법을 쓰는 탓이며 동작에는 영향이 없다. 무시한다.
