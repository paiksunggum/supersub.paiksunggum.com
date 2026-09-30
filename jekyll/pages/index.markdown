---
layout: default
title: 표지
permalink: /
nav_exclude: true
---

<div class="home-hero" markdown="0">
  <div class="home-hero__text">
    <p class="home-hero__eyebrow">Super-Sub · 개발 제안서</p>
    <h1>생활체육 경기 영상에서 선수의 실력을 재고, 그 근거로 팀이 빈 자리에 맞는 용병을 찾는 플랫폼</h1>
    <p class="home-hero__sub">Measuring Player Skill from Amateur Sports Videos and Matching Substitutes on the Evidence</p>

    <div class="home-facts">
      <div><span class="home-facts__k">기간</span><span class="home-facts__v">2026년 8월 20일 ~ 10월 27일 (10주)</span></div>
      <div><span class="home-facts__k">팀</span><span class="home-facts__v">백성검 · 박민호 · 정상호 · 정어진 (4명)</span></div>
      <div><span class="home-facts__k">웹</span><span class="home-facts__v"><a href="https://supersub-ai.com">supersub-ai.com</a></span></div>
      <div><span class="home-facts__k">앱</span><span class="home-facts__v">Google Play 비공개 테스트 (1.0.2)</span></div>
      <div><span class="home-facts__k">코드</span><span class="home-facts__v"><a href="https://github.com/paiksunggum/super-sub.cloud">github.com/paiksunggum/super-sub.cloud</a></span></div>
    </div>

    <a class="home-cta" href="/내-역할/">내 역할 보기 (백성검) →</a>

    <p class="home-note">4인이 함께 쓴 제안서입니다. 제가 맡은 부분은 따로 정리해 두었습니다.</p>

    <nav class="home-browse" aria-label="둘러보기">
      <h2>둘러보기</h2>
      <dl>
        <dt>왜 만드나</dt>
        <dd><a href="/01-사업개요/">사업 개요</a> · <a href="/02-현황및문제정의/">현황 및 문제 정의</a> · <a href="/03-서비스제안/">서비스 제안</a></dd>
        <dt>무엇을 요구하나</dt>
        <dd><a href="/05-요구사항분석/">요구사항 분석</a></dd>
        <dt>어떻게 설계했나</dt>
        <dd><a href="/06-시스템설계/">시스템 설계</a> · <a href="/부록D-데이터베이스ERD/">데이터베이스 ERD</a></dd>
        <dt>어떻게 계획했나</dt>
        <dd><a href="/07-개발구현계획/">개발 구현 계획</a> · <a href="/08-테스트및검증계획/">테스트 및 검증 계획</a></dd>
        <dt>어디까지 왔나</dt>
        <dd><a href="/진행-현황/">진행 현황</a> · <a href="/관리-지표/">관리 지표</a> · <a href="/devlog/">개발 로그</a></dd>
      </dl>
    </nav>
  </div>

  <aside class="home-media" data-mode="app">
    <figure class="home-media__stage">
      <a class="home-media__link" href="/assets/home/app-home.jpg" title="크게 보기">
        <img class="home-media__shot" src="/assets/home/app-home.jpg" alt="Super-Sub 앱 홈 화면" width="600" height="1300">
      </a>
      <figcaption><span class="home-media__cap">앱 — 홈</span> <span class="home-media__zoom">(눌러서 크게)</span></figcaption>
    </figure>

    <div class="home-media__controls">
      <div class="home-media__tabs" role="tablist" aria-label="화면 종류">
        <button type="button" role="tab" data-mode="app" aria-selected="true">앱</button>
        <button type="button" role="tab" data-mode="web" aria-selected="false">웹</button>
      </div>

      <div class="home-media__thumbs" data-for="app" aria-label="앱 화면 고르기">
        <button type="button" class="is-on" data-shot="/assets/home/app-home.jpg" data-cap="앱 — 홈" data-w="600" data-h="1300">
          <img src="/assets/home/t-home.jpg" alt="홈" loading="lazy" width="157" height="340"><span>홈</span>
        </button>
        <button type="button" data-shot="/assets/home/app-squad.jpg" data-cap="앱 — 스쿼드 판" data-w="600" data-h="1300">
          <img src="/assets/home/t-squad.jpg" alt="스쿼드" loading="lazy" width="157" height="340"><span>스쿼드</span>
        </button>
        <button type="button" data-shot="/assets/home/app-analyze.jpg" data-cap="앱 — 영상 분석" data-w="600" data-h="1300">
          <img src="/assets/home/t-analyze.jpg" alt="영상 분석" loading="lazy" width="157" height="340"><span>분석</span>
        </button>
        <button type="button" data-shot="/assets/home/app-report.jpg" data-cap="앱 — 리포트 · 선수와 비교" data-w="600" data-h="1300">
          <img src="/assets/home/t-report.jpg" alt="리포트" loading="lazy" width="157" height="340"><span>리포트</span>
        </button>
        <button type="button" data-shot="/assets/home/app-profile.jpg" data-cap="앱 — 내 프로필" data-w="600" data-h="1300">
          <img src="/assets/home/t-profile.jpg" alt="프로필" loading="lazy" width="157" height="340"><span>프로필</span>
        </button>
      </div>

      <div class="home-media__thumbs is-wide" data-for="web" aria-label="웹 화면 고르기" hidden>
        <button type="button" class="is-on" data-shot="/assets/home/web-home.jpg" data-cap="웹 — 홈 · 스쿼드 판" data-w="1400" data-h="702">
          <img src="/assets/home/tw-home.jpg" alt="홈" loading="lazy" width="320" height="160"><span>홈</span>
        </button>
        <button type="button" data-shot="/assets/home/web-recommend.jpg" data-cap="웹 — AI 추천 · 지인 찾기" data-w="1400" data-h="841">
          <img src="/assets/home/tw-recommend.jpg" alt="AI 추천" loading="lazy" width="320" height="192"><span>추천</span>
        </button>
        <button type="button" data-shot="/assets/home/web-compare.jpg" data-cap="웹 — 프로 선수와 자세 비교" data-w="1400" data-h="660">
          <img src="/assets/home/tw-compare.jpg" alt="선수 비교" loading="lazy" width="320" height="151"><span>선수 비교</span>
        </button>
        <button type="button" data-shot="/assets/home/web-report.jpg" data-cap="웹 — 분석 리포트" data-w="1400" data-h="694">
          <img src="/assets/home/tw-report.jpg" alt="분석 리포트" loading="lazy" width="320" height="159"><span>리포트</span>
        </button>
        <button type="button" data-shot="/assets/home/web-profile.jpg" data-cap="웹 — 내 프로필" data-w="1400" data-h="754">
          <img src="/assets/home/tw-profile.jpg" alt="내 프로필" loading="lazy" width="320" height="172"><span>프로필</span>
        </button>
        <button type="button" data-shot="/assets/home/web-videos.jpg" data-cap="웹 — 영상 둘러보기" data-w="1400" data-h="693">
          <img src="/assets/home/tw-videos.jpg" alt="영상 둘러보기" loading="lazy" width="320" height="158"><span>영상</span>
        </button>
      </div>
    </div>
  </aside>
</div>

[목차 →](/toc/)
{: .home-toclink }
