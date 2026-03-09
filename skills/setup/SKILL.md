---
name: setup
description: tpm 플러그인 초기 설정 — generate_image 도구 확인 및 GEMINI_API_KEY 설정
type: setup
user-invocable: true
---

# Setup

[SETUP 활성화]

## 목표

tpm 플러그인의 초기 설정을 수행함.
`gateway/install.yaml`을 읽어 필요한 도구를 설치하고,
GEMINI_API_KEY 설정을 안내함.

## 활성화 조건

사용자가 `/tpm:setup` 호출 시.

## 워크플로우

이 워크플로우의 모든 Step은 직결형으로 수행하며, Agent 위임 없이 직접 도구를 사용함.

### Step 1: install.yaml 로드 (`ulw` 활용)

`gateway/install.yaml`을 읽어 설치 항목 확인:
- custom_tools: generate_image

### Step 2: generate_image 도구 확인 (`ulw` 활용)

generate_image 도구 파일 존재 확인:
```bash
test -f gateway/tools/generate_image.py && echo "OK" || echo "NOT FOUND"
```
파일이 없으면 사용자에게 안내: "generate_image.py 파일이 없습니다. plugins/tpm/gateway/tools/에서 확인하세요."

### Step 3: GEMINI_API_KEY 설정 안내

1. `gateway/tools/.env` 파일 존재 여부 확인
2. 미존재 시 `.env.example`을 기반으로 `.env` 파일 생성 안내:
   ```
   # gateway/tools/.env 파일을 생성하고 아래 내용을 입력하세요:
   GEMINI_API_KEY=your_api_key_here
   ```
3. 사용자에게 API 키 설정 여부 확인

### Step 4: Python 의존성 설치 확인 (`ulw` 활용)

```bash
python -c "import google.generativeai; print('OK')" 2>/dev/null || echo "NOT INSTALLED"
```
미설치 시 안내:
```
다음 명령을 실행하여 의존성을 설치하세요:
pip install python-dotenv google-genai
```

### Step 5: 적용 범위 설정

사용자에게 플러그인 적용 범위 선택 요청:
- 모든 프로젝트 → `~/.claude/CLAUDE.md`에 라우팅 힌트 추가
- 현재 프로젝트만 → `./CLAUDE.md`에 라우팅 힌트 추가

### Step 6: 설치 결과 요약 보고

```
✅ tpm 플러그인 설정 완료

## 설정 결과
- generate_image 도구: {상태}
- GEMINI_API_KEY: {설정됨/미설정 — 이미지 생성 불가}
- Python 의존성: {상태}

## 사용 시작
/tpm:propose — 공연 기획 제안서 자동 생성
/tpm:help — 전체 명령 안내
```

## MUST 규칙

| # | 규칙 |
|---|------|
| 1 | install.yaml을 기반으로 설치 항목 확인 (하드코딩 금지) |
| 2 | GEMINI_API_KEY는 `gateway/tools/.env` 파일에 저장 안내 |
| 3 | 의존성 미설치 시에도 다른 기능(리서치, 분석, 기획서)은 사용 가능함을 안내 |

## MUST NOT 규칙

| # | 금지 사항 |
|---|----------|
| 1 | GEMINI_API_KEY를 any 코드 파일이나 설정 파일에 하드코딩하지 않음 |
| 2 | .env 파일을 Git 저장소에 커밋하도록 안내하지 않음 |

## 검증 체크리스트

- [ ] install.yaml을 참조하여 설치 항목을 확인하는가
- [ ] GEMINI_API_KEY 설정 경로가 `gateway/tools/.env`로 명시되었는가
- [ ] 의존성 설치 명령이 포함되었는가
- [ ] 적용 범위 선택 단계가 있는가
