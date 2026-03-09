# TPM — Theater Production Manager

> 연극·뮤지컬 공연 기획 제안서 자동화 플러그인

---

## 개요

TPM은 공연 기획사 내부 팀이 연극·뮤지컬 기획 제안서를 작성할 때, 작품 리서치부터 컨셉 이미지가 포함된 최종 제안서·프레젠테이션까지 전 과정을 자동화하는 DMAP 플러그인입니다.

**주요 기능:**
- 작품·국내외 공연 자료 리서치 및 레퍼런스 수집
- 시장 동향, 타겟 관객층, 경쟁 공연 분석 및 마케팅 전략 수립
- 공연 규모·조건에 맞는 제작비·마케팅비 항목별 예산 계획
- 분석 결과 기반 기획 제안서(Markdown) 자동 작성
- Gemini 기반 기획 컨셉 이미지 및 공연 핵심 장면 이미지 생성
- 경영진 승인용 프레젠테이션 개요 자동 구성

---

## 설치

### 사전 요구사항

- [Claude Code](https://claude.com/claude-code) CLI 설치
- Python 3.8+ (이미지 생성 기능 사용 시)
- Google Gemini API Key (이미지 생성 기능 사용 시)

### 플러그인 설치

**방법 1: 마켓플레이스 — GitHub (권장)**

```bash
# 1. GitHub 저장소를 마켓플레이스로 등록
claude plugin marketplace add {owner}/tpm

# 2. 플러그인 설치
claude plugin install tpm@{marketplace-name}

# 3. 설치 확인
claude plugin list
```

**방법 2: 마켓플레이스 — 로컬**

```bash
# 1. 로컬 경로를 마켓플레이스로 등록
claude plugin marketplace add ./tpm

# 2. 플러그인 설치
claude plugin install tpm@{marketplace-name}

# 3. 설치 확인
claude plugin list
```

> **설치 후 setup 스킬 실행:**
> ```
> /tpm:setup
> ```
> - `gateway/install.yaml`을 읽어 필수 도구 자동 설치
> - 이미지 생성 도구(generate_image) 의존성 설치 안내
> - 환경 변수 설정 안내 (`GEMINI_API_KEY`)
> - 플러그인 활성화 확인

### 처음 GitHub을 사용하시나요?

다음 가이드를 참고하세요:

- [GitHub 계정 생성 가이드](https://github.com/unicorn-plugins/gen-ma-plugin/blob/main/resources/guides/github/github-account-setup.md)
- [Personal Access Token 생성 가이드](https://github.com/unicorn-plugins/gen-ma-plugin/blob/main/resources/guides/github/github-token-guide.md)
- [GitHub Organization 생성 가이드](https://github.com/unicorn-plugins/gen-ma-plugin/blob/main/resources/guides/github/github-organization-guide.md)

---

## 업그레이드

### Git Repository 마켓플레이스

```bash
# 마켓플레이스 업데이트
claude plugin marketplace update {marketplace-name}

# 플러그인 재설치
claude plugin install tpm@{marketplace-name}

# 설치 확인
claude plugin list
```

> **갱신이 반영되지 않는 경우:**
> ```bash
> claude plugin remove tpm@{marketplace-name}
> claude plugin marketplace update {marketplace-name}
> claude plugin install tpm@{marketplace-name}
> ```

### 로컬 마켓플레이스

```bash
# 1. 로컬 플러그인 소스 갱신
cd ./tpm
git pull origin main

# 2. 마켓플레이스 업데이트
claude plugin marketplace update {marketplace-name}

# 3. 플러그인 재설치
claude plugin install tpm@{marketplace-name}
```

> **setup 재실행**: 업그레이드 후 새 도구가 추가된 경우 `/tpm:setup`을 재실행할 것.

---

## 사용법

### 슬래시 명령

| 명령 | 설명 |
|------|------|
| `/tpm:setup` | 플러그인 초기 설정 (의존성 설치, 환경 변수, 도구 검증) |
| `/tpm:propose` | 공연 기획 제안서 자동 생성 (전체 워크플로우) |
| `/tpm:help` | 사용법 및 명령어 안내 |
| `/tpm:add-ext-skill` | 외부호출 스킬 추가 |
| `/tpm:remove-ext-skill` | 외부호출 스킬 제거 |

### 사용 예시

```
사용자: /tpm:propose
→ TPM이 기획할 작품명과 공연 규모를 묻고,
  리서치 → 시장분석 → 예산계획 → 기획서 작성 → 이미지 생성 → 프레젠테이션 개요까지
  전 과정을 자동으로 처리하여 output/ 디렉토리에 산출물 저장
```

### 산출물

| 파일 | 설명 |
|------|------|
| `output/01-research.md` | 작품 리서치 결과 |
| `output/02-market-analysis.md` | 시장·관객 분석 결과 |
| `output/03-budget-plan.md` | 예산 계획서 |
| `output/04-proposal-{작품명}.md` | 기획 제안서 |
| `output/images/` | 컨셉 이미지 (PNG) |
| `output/06-presentation-{작품명}.md` | 프레젠테이션 개요 |

---

## 에이전트 구성

| 에이전트 | 티어 | 역할 |
|----------|------|------|
| researcher | MEDIUM | 연극·뮤지컬 후보 작품 발굴, 국내외 공연 자료 수집, 레퍼런스 리서치 |
| market-analyst | HIGH | 시장 동향, 타겟 관객층, 경쟁 공연 분석 및 마케팅 전략 수립 |
| budget-planner | MEDIUM | 제작비·마케팅비 항목별 예산 계획 수립 (공연 도메인 특화) |
| proposal-writer | MEDIUM | 기획 제안서 및 프레젠테이션 개요 문서 작성 |
| visual-creator | LOW | Gemini 기반 기획 컨셉 이미지 및 공연 핵심 장면 이미지 생성 |

---

## 요구사항

### 필수 도구

| 도구 | 유형 | 용도 |
|------|------|------|
| generate_image | Custom | 기획 컨셉 이미지 및 공연 장면 이미지 생성 (Gemini 기반) |

### 환경 변수

| 변수명 | 필수 | 설명 |
|--------|:----:|------|
| `GEMINI_API_KEY` | 이미지 생성 시 필수 | Google Gemini API Key |

> 환경 변수 설정 위치: `gateway/tools/.env` (`.env.example` 참조)

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
├── agents/
│   ├── researcher/
│   │   ├── AGENT.md
│   │   ├── agentcard.yaml
│   │   └── tools.yaml
│   ├── market-analyst/
│   │   ├── AGENT.md
│   │   ├── agentcard.yaml
│   │   └── tools.yaml
│   ├── budget-planner/
│   │   ├── AGENT.md
│   │   ├── agentcard.yaml
│   │   └── tools.yaml
│   ├── proposal-writer/
│   │   ├── AGENT.md
│   │   ├── agentcard.yaml
│   │   └── tools.yaml
│   └── visual-creator/
│       ├── AGENT.md
│       ├── agentcard.yaml
│       └── tools.yaml
├── skills/
│   ├── core/
│   │   └── SKILL.md
│   ├── propose/
│   │   └── SKILL.md
│   ├── setup/
│   │   └── SKILL.md
│   ├── help/
│   │   └── SKILL.md
│   ├── add-ext-skill/
│   │   └── SKILL.md
│   └── remove-ext-skill/
│       └── SKILL.md
├── gateway/
│   ├── install.yaml
│   ├── runtime-mapping.yaml
│   └── tools/
│       ├── generate_image.py
│       └── .env.example
├── commands/
│   ├── propose.md
│   ├── setup.md
│   ├── help.md
│   ├── add-ext-skill.md
│   └── remove-ext-skill.md
├── resources/
│   ├── guides/
│   │   └── theater-production-guide.md
│   ├── templates/
│   │   ├── proposal-template.md
│   │   └── presentation-outline-template.md
│   └── resource.md
├── .gitignore
└── README.md
```

---

## 라이선스

MIT License
