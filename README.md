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

- [Claude Code](https://claude.com/claude-code) CLI 설치
- Python 3.8+ (이미지 생성·Word 변환 기능 사용 시)
- Google Gemini API Key (이미지 생성 기능 사용 시)

### 플러그인 설치

**방법 1: 마켓플레이스 — GitHub (권장)**

```bash
# 1. GitHub 저장소를 마켓플레이스로 등록
claude plugin marketplace add unicorn-plugins/tpm

# 2. 플러그인 설치
claude plugin install tpm@tpm

# 3. 설치 확인
claude plugin list
```

**방법 2: 마켓플레이스 — 로컬**

```bash
# 1. 로컬 경로를 마켓플레이스로 등록
claude plugin marketplace add ./tpm

# 2. 플러그인 설치
claude plugin install tpm@tpm

# 3. 설치 확인
claude plugin list
```

> **처음 GitHub을 사용하시나요?**
>
> - [GitHub 계정 생성 가이드](https://github.com/unicorn-plugins/gen-ma-plugin/blob/main/resources/guides/github/github-account-setup.md)
> - [Personal Access Token 생성 가이드](https://github.com/unicorn-plugins/gen-ma-plugin/blob/main/resources/guides/github/github-token-guide.md)
> - [GitHub Organization 생성 가이드](https://github.com/unicorn-plugins/gen-ma-plugin/blob/main/resources/guides/github/github-organization-guide.md)

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

---

## 업그레이드

### Git Repository 마켓플레이스

```bash
# 마켓플레이스 업데이트
claude plugin marketplace update tpm

# 플러그인 재설치
claude plugin install tpm@tpm

# 설치 확인
claude plugin list
```

> **갱신이 반영되지 않는 경우:**
> ```bash
> claude plugin remove tpm@tpm
> claude plugin marketplace update tpm
> claude plugin install tpm@tpm
> ```

### 로컬 마켓플레이스

```bash
# 1. 로컬 플러그인 소스 갱신
cd ./tpm && git pull origin main

# 2. 마켓플레이스 업데이트
claude plugin marketplace update tpm

# 3. 플러그인 재설치
claude plugin install tpm@tpm
```

> 업그레이드 후 새 도구가 추가된 경우 `/tpm:setup`을 재실행하세요.

---

## 사용법

TPM은 **Claude Code CLI**와 **DMAP Web** 두 가지 방식으로 사용할 수 있습니다.

---

### 방법 1: Claude Code CLI

#### 슬래시 명령

| 명령 | 설명 |
|------|------|
| `/tpm:setup` | 플러그인 초기 설정 (의존성 설치, 환경 변수, 도구 검증) |
| `/tpm:propose` | 공연 기획 제안서 자동 생성 (12단계 전체 워크플로우) |
| `/tpm:help` | 사용법 및 명령어 안내 |
| `/tpm:add-ext-skill` | 외부호출 스킬 추가 |
| `/tpm:remove-ext-skill` | 외부호출 스킬 제거 |

#### 기본 사용 흐름

```
1. /tpm:setup       ← 최초 1회 실행
2. /tpm:propose     ← 작품명 입력 후 전 과정 자동 실행
```

작품명을 입력하면 TPM이 Phase 1부터 12까지 자동으로 실행하며, 각 단계 완료 시 `output/{작품명}/` 디렉토리에 산출물을 저장합니다.

**중단 및 재개:**

- 중단: "취소" 또는 "중단" 입력
- 재개: `/tpm:propose` 재실행 후 작품명 입력 → 마지막 완료된 Phase부터 자동 재개

---

### 방법 2: DMAP Web

[DMAP Web](https://github.com/unicorn-plugins/dmap)은 브라우저에서 TPM 스킬을 실행할 수 있는 웹 인터페이스입니다. 채팅 형태로 실시간 실행 결과를 확인하고, 승인 게이트를 통해 워크플로우를 제어할 수 있습니다.

#### 사전 요구사항

- Node.js 18 이상
- Git

#### 1단계: DMAP Web 클론 및 실행

```bash
# DMAP Web 저장소 클론
git clone https://github.com/unicorn-plugins/dmap.git
cd dmap

# 의존성 설치 및 실행
npm install
npm run dev
```

- 프론트엔드: http://localhost:5173
- 백엔드 API: http://localhost:3001

#### 2단계: 공연제안 플러그인 추가

DMAP Web이 실행 중인 상태에서 브라우저 UI로 플러그인을 추가합니다.

**방법 1: DMAP Web UI를 통해 추가 (권장)**

1. 브라우저에서 http://localhost:5173 접속
2. 사이드바 상단의 **"플러그인 추가"** 버튼 클릭
3. 추가 방식 선택:
   - **GitHub에서 가져오기** — GitHub 저장소 URL 입력 (예: `unicorn-plugins/tpm`)
   - **로컬에서 추가** — 로컬 경로 입력 (예: `/Users/dreamondal/workspace/tpm`)
4. 경로 입력 후 확인 → 플러그인 디렉토리 구조(agents, commands, gateway 등) 미리보기 확인
5. **등록** 버튼 클릭 → 사이드바에 "공연제안" 플러그인 자동 추가

**방법 2: `plugins.json` 직접 편집**

DMAP Web의 `plugins.json`에 TPM 프로젝트 경로를 수동으로 추가합니다:

```json
{
  "projectDir": "/path/to/tpm",
  "displayNames": {
    "ko": "공연제안",
    "en": "공연제안"
  }
}
```

#### 3단계: 웹 UI 사용

```
1. 브라우저에서 http://localhost:5173 접속
2. 사이드바에서 "공연제안" 플러그인 선택
3. Core → "공연 기획 제안서 자동 생성" 스킬 선택
4. 채팅창에 작품명 입력 → 전 과정 자동 실행
5. 승인 게이트 발생 시 옵션 선택 또는 직접 입력으로 진행 제어
```

#### 사용 가능한 스킬 (DMAP Web 메뉴)

| 카테고리 | 스킬 | 설명 |
|----------|------|------|
| Core | 공연 기획 제안서 자동 생성 | `/tpm:propose` — 12단계 전체 워크플로우 |
| Utility | 플러그인 초기설정 | `/tpm:setup` |
| Utility | 플러그인 추가 | `/tpm:add-ext-skill` |
| Utility | 플러그인 제거 | `/tpm:remove-ext-skill` |
| Utility | 도움말 | `/tpm:help` |

---

## 기획 제안서 작성 워크플로우

`/tpm:propose` 실행 시 아래 12단계가 순차 실행됩니다.

| Phase | 단계 | 담당 에이전트 | 산출물 |
|-------|------|--------------|--------|
| 1 | 작품 리서치 | researcher | `01-research.md` |
| 2 | 시장·관객 분석 | market-analyst | `02-market-analysis.md` |
| 3 | 문제 가설 설정 | market-analyst | `03-problem-hypothesis.md` |
| 4 | 킹핀 & 방향성 정의 | market-analyst | `04-direction.md` |
| 5 | 공연 컨셉 후보 도출 | market-analyst + proposal-writer (병렬) | `05-concept-candidates.md` |
| 6 | 핵심 컨셉 선정 | market-analyst + proposal-writer (병렬) | `06-core-concept.md` + `06-concept-matrix.svg` |
| 7 | 예산 계획 | budget-planner | `07-budget-plan.md` |
| 8 | 기획 제안서 작성 | proposal-writer | `08-proposal-{작품명}.md` |
| 9 | 컨셉 이미지 생성 | visual-creator | `images/` |
| 10 | 프레젠테이션 구성 | proposal-writer | `10-presentation-{작품명}.md` |
| 11 | 고객용 제안서 작성 | proposal-writer | `11-customer-proposal-{작품명}.md` + `proposal-client-{작품명}.docx` |
| 12 | 고객용 프레젠테이션 | proposal-writer | `12-customer-presentation-{작품명}.md` |

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
| `01-research.md` | 1 | 작품 리서치 결과 (유사 공연 레퍼런스 포함) |
| `02-market-analysis.md` | 2 | 시장·관객 분석 결과 |
| `03-problem-hypothesis.md` | 3 | 문제 가설 · 5WHY 분석 · 비즈니스 가치 |
| `04-direction.md` | 4 | 킹핀 문제 선정 · Needs Statement |
| `05-concept-candidates.md` | 5 | 공연 컨셉 후보 3~5개 |
| `06-core-concept.md` | 6 | 핵심 공연 컨셉 (3개 이하) |
| `06-concept-matrix.svg` | 6 | 컨셉 우선순위 매트릭스 시각화 |
| `07-budget-plan.md` | 7 | 항목별 예산 상세 · BEP 분석 |
| `08-proposal-{작품명}.md` | 8 | 내부 기획 제안서 (7섹션 + 부록) |
| `images/` | 9 | 컨셉 포스터·장면 이미지 (PNG) |
| `10-presentation-{작품명}.md` | 10 | 내부 프레젠테이션 개요 (10슬라이드) |

### 고객용 (외부)

| 파일 | Phase | 설명 |
|------|:-----:|------|
| `11-customer-proposal-{작품명}.md` | 11 | 고객용 제안서 마크다운 초안 |
| `proposal-client-{작품명}.docx` | 11 | 고객용 제안서 Word 파일 (이미지 삽입 최종본) |
| `12-customer-presentation-{작품명}.md` | 12 | 고객용 프레젠테이션 개요 (7슬라이드) |

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

## 요구사항

### 환경 변수

| 변수명 | 필수 | 설명 |
|--------|:----:|------|
| `GEMINI_API_KEY` | 이미지 생성 시 필수 | Google Gemini API Key |

설정 위치: `gateway/tools/.env` (`.env.example` 참조)

### 런타임 호환성

| 런타임 | 지원 |
|--------|:----:|
| Claude Code | ✅ |
| Codex CLI | 미검증 |
| Gemini CLI | 미검증 |

---

## 디렉토리 구조

```
tpm/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── agents/                          # AI 에이전트 정의
│   ├── researcher/
│   ├── market-analyst/
│   ├── budget-planner/
│   ├── proposal-writer/
│   └── visual-creator/
│       ├── AGENT.md                 # 에이전트 역할·워크플로우
│       ├── agentcard.yaml           # 페르소나·역량·제약
│       └── tools.yaml               # 사용 가능 도구 선언
├── skills/                          # 슬래시 명령 정의
│   ├── propose/                     # /tpm:propose — 핵심 오케스트레이터
│   ├── setup/                       # /tpm:setup
│   ├── help/                        # /tpm:help
│   ├── add-ext-skill/               # /tpm:add-ext-skill
│   ├── remove-ext-skill/            # /tpm:remove-ext-skill
│   └── core/
├── gateway/
│   ├── install.yaml                 # 의존성 설치 정의
│   ├── runtime-mapping.yaml         # 모델 티어·도구 매핑
│   └── tools/
│       ├── generate_image.py        # Gemini 이미지 생성 도구
│       ├── generate_docx.py         # Word 파일 변환 도구
│       └── .env.example
├── commands/                        # 슬래시 명령 라우팅
├── resources/
│   ├── company-profile.md           # 극단 고정 정보 (예산·전속 배우)
│   ├── guides/                      # Phase 3~6 방법론 가이드
│   │   ├── 03-problem-hypothesis-guide.md
│   │   ├── 04-direction-guide.md
│   │   ├── 05-ideation-guide.md
│   │   ├── 06-solution-selection-guide.md
│   │   └── theater-production-guide.md
│   └── templates/                   # 문서 템플릿
│       ├── proposal-template.md
│       ├── presentation-outline-template.md
│       ├── customer-proposal-template.md
│       └── customer-presentation-template.md
├── output/                          # 작품별 산출물 저장소
│   └── {작품명}/
│       ├── 01-research.md ~ 12-*.md
│       ├── images/
│       └── proposal-client-{작품명}.docx
└── README.md
```

---

## 라이선스

MIT License
