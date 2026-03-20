# TPM — Theater Production Manager

> 연극·뮤지컬 공연 기획 제안서 자동화 플러그인

---

## 개요

TPM은 공연 기획사 내부 팀이 연극·뮤지컬 기획 제안서를 작성할 때, 작품 리서치부터 컨셉 이미지가 포함된 최종 제안서·프레젠테이션까지 **전 과정을 자동화**하는 Claude Code 플러그인입니다.

`/tpm:propose` 한 번으로 5개의 전문 AI 에이전트가 협업하여 12단계 워크플로우를 순차 실행하고, 내부용 기획 제안서와 고객용 Word 파일까지 자동 생성합니다.

**주요 기능:**

- 작품·국내외 공연 자료 리서치 및 유사 공연 레퍼런스 수집
- 시장 동향·타겟 관객층·경쟁 공연 분석 및 마케팅 전략 수립
- 5WHY 기반 문제 가설 설정 및 킹핀 문제·공연 방향성 정의
- SCAMPER·Steal & Synthesize 기법으로 공연 컨셉 후보 도출 및 선정
- 공연 규모·조건에 맞는 제작비·마케팅비 항목별 예산 계획 (BEP 분석 포함)
- 분석 결과 기반 내부 기획 제안서 자동 작성
- Gemini 기반 기획 컨셉 이미지 및 공연 핵심 장면 이미지 생성
- 경영진 승인용 프레젠테이션 개요 자동 구성
- 고객(관람객·구매자)용 외부 제안서 및 PPT 개요 자동 작성 (Word 파일 출력)

---

## 설치

### 사전 요구사항

- [설치하기](./install.md) 참고하여 설치
- Python 3.8+ (이미지 생성·Word 변환 기능 사용 시)
- Google Gemini API Key (이미지 생성 기능 사용 시)

### 플러그인 설치

```bash
# 0. 플러그인 다운로드
cd ~
git clone https://github.com/unicorn-plugins/tpm.git

# 1. 로컬 경로를 마켓플레이스로 등록
claude plugin marketplace add ./tpm

# 2. 플러그인 설치
claude plugin install tpm@tpm

# 3. 설치 확인
claude plugin list
```

> **처음 GitHub을 사용하시나요?**
>
> - [GitHub 계정 생성 가이드](https://github.com/unicorn-plugins/dmap/blob/main/resources/guides/github/github-account-setup.md)
> - [Personal Access Token 생성 가이드](https://github.com/unicorn-plugins/dmap/blob/main/resources/guides/github/github-token-guide.md)
> - [GitHub Organization 생성 가이드](https://github.com/unicorn-plugins/dmap/blob/main/resources/guides/github/github-organization-guide.md)

### 초기 설정

설치 후 반드시 `/tpm:setup`을 실행하세요.

```
/tpm:setup
```

- `gateway/install.yaml`을 읽어 필수 Python 패키지 자동 설치
- 이미지 생성 도구(`generate_image`) 의존성 설치 안내
- 환경 변수 설정 안내 (`GEMINI_API_KEY`)
- 플러그인 활성화 확인

환경 변수는 `gateway/tools/.env` 파일에 저장합니다 (`.env.example` 참조):

```bash
GEMINI_API_KEY=your_api_key_here
```

## 사용법

| 명령 | 설명 |
|------|------|
| `/tpm:setup` | 플러그인 초기 설정 (의존성 설치, 환경 변수, 도구 검증) |
| `/tpm:propose` | 공연 기획 제안서 자동 생성 (12단계 전체 워크플로우) |
| `/tpm:help` | 사용법 및 명령어 안내 |


## 기획 제안서 작성 워크플로우

`/tpm:propose` 실행 시 아래 12단계가 순차 실행됩니다.

| Phase | 단계 | 담당 에이전트 | 산출물 |
|-------|------|--------------|--------|
| 1 | 작품 리서치 | researcher | `01-리서치.md` |
| 2 | 시장·관객 분석 | market-analyst | `02-시장분석.md` |
| 3 | 문제 가설 설정 | market-analyst | `03-문제가설.md` |
| 4 | 킹핀 & 방향성 정의 | market-analyst | `04-방향성.md` |
| 5 | 공연 컨셉 후보 도출 | market-analyst + proposal-writer (병렬) | `05-컨셉후보.md` |
| 6 | 핵심 컨셉 선정 | market-analyst + proposal-writer (병렬) | `06-핵심컨셉.md` + `06-컨셉매트릭스.svg` |
| 7 | 예산 계획 | budget-planner | `07-예산계획.md` |
| 8 | 기획 제안서 작성 | proposal-writer | `08-기획제안서-{작품명}.md` |
| 9 | 컨셉 이미지 생성 | visual-creator | `images/` |
| 10 | 프레젠테이션 구성 | proposal-writer | `10-프레젠테이션-{작품명}.md` |
| 11 | 고객용 제안서 작성 | proposal-writer | `11-고객제안서-{작품명}.md` + `고객제안서-{작품명}.docx` |
| 12 | 고객용 프레젠테이션 | proposal-writer | `12-고객프레젠테이션-{작품명}.md` |

### 단계별 상세

**Phase 1 — 작품 리서치**
국내외 유사 공연 레퍼런스 3건 이상 수집, 원작 정보 및 공연화 사례 조사.

**Phase 2 — 시장·관객 분석**
타겟 관객 프로파일, 경쟁 공연 2건 이상 비교 분석, 마케팅 채널·전략안 도출.

**Phase 3 — 문제 가설 설정**
관객·시장이 겪는 핵심 문제 3개를 5WHY 분석으로 근본원인까지 도출. 해소 시 얻는 비즈니스 가치 정의.

**Phase 4 — 킹핀 & 방향성 정의**
영향력·빈도·심각도·근본성·해결가능성 5기준 평가로 킹핀 문제 선정. Needs Statement 형식으로 공연 방향성 정의.

**Phase 5 — 공연 컨셉 후보 도출**
SCAMPER·Steal & Synthesize 기법으로 Big Idea 3개 + Little Win 2개 + Crazy Idea 1개 도출(병렬). 유사도 평가(컨셉 70% / 형식 30%)로 3~5개 후보 수렴.

**Phase 6 — 핵심 컨셉 선정**
관객 매력도(A) × 실현 가능성(F) 2×2 매트릭스 투표로 핵심 컨셉 3개 이하 선정. SVG 매트릭스 시각화 포함.

**Phase 7 — 예산 계획**
제작비·출연료·운영비·마케팅비·예비비 항목별 산정 (총 2,000만원 이내). BEP 관객 수 및 시나리오별 수익 분석.

**Phase 8 — 기획 제안서 작성**
리서치·시장분석·방향성·핵심 컨셉·예산을 통합한 7섹션 + 부록 구조의 내부 기획 제안서.

**Phase 9 — 컨셉 이미지 생성**
Gemini 기반으로 컨셉 포스터 1장, 작품개요·기획의도 인포그래픽 1장, 주요 장면 이미지 최소 3장 생성.

**Phase 10 — 프레젠테이션 구성**
경영진 승인용 10슬라이드 PPT 개요. 주요 장면 이미지 삽입 위치 포함.

**Phase 11 — 고객용 제안서 작성**
관람객·구매자 대상 감성적·설득적 언어의 외부용 제안서. 마크다운 초안 저장 후 이미지가 삽입된 MS Word 파일로 자동 변환.

**Phase 12 — 고객용 프레젠테이션**
고객 대상 7슬라이드 PPT 개요. 장면 이미지와 감성적 메시지 중심 구성.

---

## 산출물

모든 파일은 `output/{작품명}/` 디렉토리에 저장됩니다.

### 내부용 (경영진)

| 파일 | Phase | 설명 |
|------|:-----:|------|
| `01-리서치.md` | 1 | 작품 리서치 결과 (유사 공연 레퍼런스 포함) |
| `02-시장분석.md` | 2 | 시장·관객 분석 결과 |
| `03-문제가설.md` | 3 | 문제 가설 · 5WHY 분석 · 비즈니스 가치 |
| `04-방향성.md` | 4 | 킹핀 문제 선정 · Needs Statement |
| `05-컨셉후보.md` | 5 | 공연 컨셉 후보 3~5개 |
| `06-핵심컨셉.md` | 6 | 핵심 공연 컨셉 (3개 이하) |
| `06-컨셉매트릭스.svg` | 6 | 컨셉 우선순위 매트릭스 시각화 |
| `07-예산계획.md` | 7 | 항목별 예산 상세 · BEP 분석 |
| `08-기획제안서-{작품명}.md` | 8 | 내부 기획 제안서 (7섹션 + 부록) |
| `images/` | 9 | 컨셉 포스터·장면 이미지 (PNG) |
| `10-프레젠테이션-{작품명}.md` | 10 | 내부 프레젠테이션 개요 (10슬라이드) |

### 고객용 (외부)

| 파일 | Phase | 설명 |
|------|:-----:|------|
| `11-고객제안서-{작품명}.md` | 11 | 고객용 제안서 마크다운 초안 |
| `고객제안서-{작품명}.docx` | 11 | 고객용 제안서 Word 파일 (이미지 삽입 최종본) |
| `12-고객프레젠테이션-{작품명}.md` | 12 | 고객용 프레젠테이션 개요 (7슬라이드) |

### 이미지 파일 (Phase 9)

| 파일 | 설명 |
|------|------|
| `images/concept-poster.png` | 공연 컨셉 포스터·무드보드 |
| `images/overview-concept.png` | 작품개요·기획의도 통합 인포그래픽 |
| `images/scene-01.png` ~ `scene-{n}.png` | 주요 장면 이미지 (최소 3장) |

---

## 에이전트 구성

| 에이전트 | 모델 티어 | 담당 Phase | 역할 |
|----------|:--------:|:---------:|------|
| researcher | MEDIUM | 1 | 연극·뮤지컬 후보 작품 발굴, 국내외 공연 자료 수집, 레퍼런스 리서치 |
| market-analyst | HIGH | 2·3·4·5·6 | 시장 동향, 타겟 관객층, 경쟁 공연 분석, 마케팅 전략 수립 |
| budget-planner | MEDIUM | 7 | 제작비·마케팅비 항목별 예산 계획 수립 (공연 도메인 특화) |
| proposal-writer | MEDIUM | 5·6·8·10·11·12 | 기획 제안서, 프레젠테이션 개요, 고객용 제안서 작성 |
| visual-creator | LOW | 9 | Gemini 기반 기획 컨셉 이미지 및 공연 핵심 장면 이미지 생성 |

> 모델 티어는 `gateway/runtime-mapping.yaml`에서 관리됩니다.
> HIGH → claude-opus-4-6 / MEDIUM → claude-sonnet-4-6 / LOW → claude-haiku-4-5

---

## 라이선스

MIT License
