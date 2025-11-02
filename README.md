

<div align="center" style="background:#fafbff;padding:24px;border-radius:16px">

# 🍞 Sandwich — 개발자 포트폴리오 · 추천 · 챌린지

<p>
  <img alt="type" src="https://img.shields.io/badge/Monorepo-yes-ffd6a5">
  <img alt="backend" src="https://img.shields.io/badge/Backend-Spring%20Boot-ffd3b4">
  <img alt="frontend" src="https://img.shields.io/badge/Frontend-React%20%2B%20TS-bde0fe">
  <img alt="ai" src="https://img.shields.io/badge/AI-PyTorch-cdeac0">
  <img alt="db" src="https://img.shields.io/badge/DB-Postgres-e0bbff">
  <img alt="cache" src="https://img.shields.io/badge/Cache-Redis-f1c0e8">
</p>

> 실행되는 포트폴리오 · 개인화 추천 · 참여형 성장

</div>

---

## 🧀 핵심 가치

* **실행되는 포트폴리오**: 정적 문서가 아닌 실제 앱·웹을 호스팅
* **개인화 추천**: 활동 데이터와 기술 스택을 학습
* **참여형 성장**: 주간·월간 챌린지를 통한 지속 성장

---

## 🥬 저장소 구조

```
/
├─ Sandwich/                         # 백엔드: Spring Boot
│  ├─ src/main/java/com/sandwich/... # 도메인/컨트롤러/서비스/JPA
│  ├─ src/main/resources/            # 환경, 마이그레이션
│  └─ build.gradle / gradlew         # 빌드 스크립트
├─ front/                            # 프론트: React + TS + Tailwind
│  ├─ src/                           # 페이지/컴포넌트/hooks/store
│  └─ package.json                   # 빌드 설정
├─ ai/                               # AI 추천·생성·채점 파이프라인
│  ├─ model.py                       # FeatureDeepRec 모델
│  ├─ train.py                       # 학습 스크립트
│  ├─ src/pipeline/run_once.py       # 개인화 추천(일회성/주기)
│  ├─ src/aggregation/top_projects.py# 일/주간 인기 집계
│  ├─ ai_test/weekly_main.py         # 주간 코드 챌린지 생성
│  ├─ ai_test/monthly_main.py        # 월간 포트폴리오 챌린지 생성
│  └─ ai_test/grade_latest.py        # 제출물 자동 채점/검증
├─ public/                           # 공용 정적 자산
├─ package.json                      # 루트 유틸 스크립트
└─ README.md
```

### 모듈 역할

* `Sandwich/` — 인증, 프로젝트/댓글/랭킹 API (Spring Security, JPA)
* `front/` — 로그인, 프로필, 프로젝트 리스트/상세, 검색 UI
* `ai/` — 개인화 추천 학습·추론, 챌린지 생성/채점, 인기 집계

---

## 🍅 주요 기능

* **포트폴리오 호스팅**: `username.sandwich-dev.com` 서브도메인
* **개인화 추천**: 조회·좋아요·팔로우·기술 태그 기반
* **인기 트렌드**: 일간/주간 Top 프로젝트
* **로그인/보안**: 2FA(OTP), 신뢰 기기 쿠키
* **모니터링**: 보안·배치 메트릭 수집, 대시보드

---

## 🥖 빠른 시작

### 0) 사전 요구

Java 17+, Node 18+, Python 3.10+, Docker, Postgres, Redis

### 1) 환경 변수

루트 또는 모듈별 `.env` 사용.

### 2) 백엔드 실행

```bash
cd Sandwich
docker compose down
docker compose build --no-cache
docker compose up -d
```

### 3) 프론트 실행

```bash
cd front
npm install
npm run build
npm install firebase
npm start
```

### 4) AI 서비스

```bash
# 개인 추천 파이프라인
python src/pipeline/run_once.py

# 인기 집계(일간)
python src/aggregation/top_projects.py
```

다음 블록을 `README.md`에 붙여 넣어라.

---

## ⏱ 스케줄러 사용법

### 실행

프로젝트 루트에서 실행한다. 로그는 `./logs/scheduler.log`.

```bash
python src/scheduler/scheduler.py
```

* 기동 시 **startup gate**가 `train_once`를 1회 실행한다. 실패하면 스케줄러는 시작하지 않는다.

### 등록 잡과 주기

| ID                      | 작업                | 스케줄(로컬 TZ)  |
| ----------------------- | ----------------- | ----------- |
| `top_projects_day`      | 일간 인기 집계          | 매일 00:10    |
| `train_once_hourly`     | 개인화 추천 학습/실행(일회성) | 매시 정각       |
| `inference_every_10min` | 추론                | 10분마다       |
| `weekly_main_mon_0100`  | 주간 코드 챌린지 생성      | 매주 월 01:00  |
| `monthly_main_1st_0130` | 월간 포트폴리오 챌린지 생성   | 매월 1일 01:30 |
| `grade_latest_sun_1800` | 최근 제출 채점          | 매주 일 18:00  |

### 수동 점검

각 파이프라인을 단독 실행해 경로·의존성 확인.

```bash
# 루트에서
python src/aggregation/top_projects.py
python src/pipeline/train_once.py
python src/model/inference.py
python ai_test/weekly_main.py
python ai_test/monthly_main.py
python ai_test/grade_latest.py
```

### 로그 위치

* 파일: `logs/scheduler.log`
* 콘솔: 표준 출력

---


## 🏁 챌린지(Challenge) 모듈


### 1) 주간 코드 챌린지 — Weekly

* **생성 스크립트**: `ai_test/weekly_main.py`

  * 요구: 중복·과도 사양 주제 제외, 창의적 3문제 생성
  * 산출: 문제+예시 입출력+해설 코드 → `out/`에 저장
  * **검증**: 각 해설 코드를 예시 입력으로 실행해 정답 일치 시만 업서트
* **채점 스크립트**: `ai_test/grade_latest.py`

  * 입력: 유저 제출물, 선택된 문제 `selected_idx`
  * 방식: 동일 입출력 규약으로 실행·검증, 점수와 정답 여부 산출
* **API 예시**

  * 조회: `GET /api/reco/weekly`

* **스케줄**: 매주 **월요일 01:00** 생성, **일요일 18:00** 채점
### 2) 월간 포트폴리오 챌린지 — Monthly

* **생성 스크립트**: `ai_test/monthly_main.py`

  * 요구: 창의적 주제·가이드라인 생성, 중복 방지
  * 산출: 주제·평가 루브릭·예시 산출물 템플릿 → `out/`
* **검증/등록**: 규격 불일치(필수 필드 누락) 시 폐기 후 재생성
* **API 예시**

  * 조회: `GET /api/reco/monthly/`

### 3) 공통 규칙

* **히스토리 관리**: `out/_weekly_history.json` 등으로 중복·유사 주제 방지
* **선택 인덱스**: `selected_idx`를 문제·채점 양쪽에서 공통 사용
* **가드레일**: 실행 시간/메모리 한도, 금지 라이브러리, 파일 크기 제한



---
## 🧠 AI 모델 구성 요약

### 모델(`FeatureDeepRec`, `model.py`)

* 입력: `user_id`, `item_id`, `user_feat`, `item_feat`
* ID 임베딩: `Embedding(num_users, d)`, `Embedding(num_items, d)`
* 특징 인코더: `Linear→LeakyReLU→BatchNorm1d→Dropout→Linear`로 256→`d` 투영 후 L2 정규화
* 결합: 네 벡터 concat → `LayerNorm` → MLP
* 출력: 로짓 1차원. **학습**은 `BCEWithLogitsLoss`, **추론**은 `sigmoid`

### 학습(`train.py`)

* 데이터: `interactions.csv [u_idx,p_idx,label]`, `user_skills.npy`, `project_tools.npy`
* 로더: `(u_id, i_id, U[u_id], I[i_id], y)` 반환. 8:2 분할
* 안정화: AMP·GradScaler, Grad Clipping(1.0), OOM 스킵, 그래디언트 누적
* 최적화: Adam(lr=1e-3, wd=1e-5)
* 스케줄러: `ReduceLROnPlateau(factor=0.5, patience=3)`
* 체크포인트: `models/best_feature_deeprec.pth` 저장(모델/옵티마/스케일러/에폭/val_loss)
* 조기 종료: `patience ≥ 6`

> 사용 규칙: 로짓 필요 시 `return_logits=True`
> 텐서: `u_ids,i_ids → Long[B]`, `u_feat,i_feat → Float[B,d_user|d_item]`
> 임베딩 차원 `d`는 ID/특징 임베딩 모두 동일

---

## 🔐 보안·운영

* **MFA**: 낯선 기기 로그인 시 `MFA_REQUIRED` → OTP 메일 → 검증 후 토큰
* **신뢰 기기**: `tdid`, `tdt` 쿠키
* **레이트 리밋**: 인증·글쓰기·좋아요·OTP
* **모니터링**: OTP 성공/실패율, 배치 성공률, 큐 대기 시간
* **백업**: DB 스냅샷, 오브젝트 스토리지 버전닝

---

## 🚢 배포 개요

* **CI/CD**: GitHub Actions
* **백엔드**: Docker 이미지 → ECR/ECS 롤링 업데이트
* **프론트**: S3 정적 호스팅
* **도메인**: `sandwich-dev.com` 및 사용자 서브도메인
* **시크릿**: GitHub Secrets

---

<p align="center">
  <img alt="divider" src="https://img.shields.io/badge/Enjoy%20Building-%F0%9F%8D%94%20Sandwich-fde2e4">
</p>
