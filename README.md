# Super-Sub — 개발 제안서 사이트

생활체육 경기 영상에서 선수의 실력을 재고, 그 근거로 팀이 빈 자리에 맞는 용병을
찾는 플랫폼입니다. 4인 팀으로 10주간(2026.08.20 ~ 2026.10.27) 만들었고, 저는
**Flutter 앱 전체와 Next.js 웹 대부분**을 맡았습니다.

| | |
|---|---|
| Flutter 앱 | 159개 파일 · 33,432줄 · 시험 994개 · 커밋 111/114 |
| Next.js 웹 | 308개 파일 · 54,411줄 · 커밋 392/482 |
| 전체 커밋 | 628개 (팀 전체 약 1,840개 중 최다) |

무엇을 어떻게 풀었는지는 **[「내 역할」 페이지](https://supersub.paiksunggum.com/내-역할/)**
에 정리했습니다.

## 링크

- 문서 사이트 — <https://supersub.paiksunggum.com>
- 제품 웹 — <https://supersub-ai.com>
- 안드로이드 앱 — Google Play 비공개 테스트 중 (1.0.2)
- 코드 전체 — [paiksunggum/super-sub.cloud](https://github.com/paiksunggum/super-sub.cloud) (제 작업 브랜치는 `paik`)
- 팀 저장소 — [pmhllll12/super-sub.cloud](https://github.com/pmhllll12/super-sub.cloud)

## 이 저장소

팀 제안서 사이트를 제 도메인으로 옮긴 것입니다. 본문은 네 사람이 함께 쓴 팀 문서
그대로이고, 제가 맡은 부분을 정리한 페이지를 하나 두었습니다.

Jekyll + [Just the Docs](https://just-the-docs.com/) 로 만들었습니다.

```bash
bundle install
bundle exec jekyll serve
```
