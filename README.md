# DailyFarmingGame — 데일리 농장

[ProjectDaily](https://github.com/nuclyee72/ProjectDaily) 허브 맨 왼쪽(홈 왼쪽) 카드로 들어가는 농장 게임.
출석과 데일리 퍼즐 결과로 NP를 모아 한 달 시즌 동안 탐험 → 농사/제작 → 요리 → 도감을 채운다.

- **플레이:** https://nuclyee72.github.io/ProjectDaily/#farm
- 이 저장소는 허브의 서브모듈(`ProjectDaily/DailyFarmingGame/`)이다. 혼자 도는 페이지는 없고, 허브 `index.html`이 스크립트를 불러 카드로 만든다.

## 구조

```
DailyFarmingGame/
├─ src/
│  ├─ data.js      ← 숫자와 표 전부 (기획 docs/farm-plan.md · docs/farm-GDD.xlsx와 같은 값)
│  ├─ engine.js    ← 규칙. DOM 없음, 난수 · 시각을 인자로 받음
│  ├─ sprites.js   ← 도트 251장 (16×16 픽셀맵 → 픽셀마다 음영 → 캔버스 → dataURL)
│  └─ farm.js      ← 화면. DailyFarm.build(ctx) → { slide, refresh, show, current }
├─ farm.css        ← 화면 스타일 (색은 --farm-* 토큰, 다크 모드는 허브의 data-theme을 따름)
├─ docs/           ← 기획 문서 (farm-plan.md · farm-GDD.xlsx · 입력 문서들, 배포 안 됨)
└─ tests/engine.test.cjs ← 규칙 · 도트 테스트 (Node만)
```

## 허브와 연결

- 허브 `index.html`이 `DailyFarmingGame/farm.css`와 `DailyFarmingGame/src/{data,engine,sprites,farm}.js`를 불러 `DailyFarm.build(ctx)`로 카드를 만든다.
  `ctx` = `{ GAMES, todayStr, untilNextReset, readJSON, el, button }` (NP는 `GAMES[].statsKey`의 이번 달 기록에서 계산)
- 허브 `scripts/assemble-site.mjs`가 배포 때 `farm.css` · `src`만 복사한다.
- 카드 · 버튼 · 탭 공통 모양과 색 변수(`--text-given`, `--surface-1` …)는 허브 `index.html` 스타일을 쓴다.
- 저장: `daily-farm:state`(이번 시즌, 달이 바뀌면 초기화) · `daily-farm:history`(지난 시즌 도감 기록, 허브 홈 프로필 배지)

## 테스트

```bash
npm test   # tests/engine.test.cjs — 규칙 · 확률 · 도트 (브라우저 없이)
```

화면 테스트(눌러서 되는지 · 9가지 화면 크기에서 카드 안에 들어가는지)는 허브 저장소의 `tests/farm.test.cjs` · `tests/layout.test.cjs` (`npm test`).

## 배포

허브(ProjectDaily)의 Pages 배포가 서브모듈을 이 저장소 `main` 최신으로 받아 조립한다.
`main`에 push하면 `.github/workflows/notify-hub.yml`이 허브 배포를 바로 실행한다 (시크릿 `HUB_DEPLOY_TOKEN` 필요, 없으면 허브의 6시간마다 배포에 맞춰 반영).
