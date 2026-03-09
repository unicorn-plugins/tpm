---
name: add-ext-skill
description: 외부호출 스킬(ext-{대상플러그인}) 추가 유틸리티
type: utility
user-invocable: true
---

# Add External Skill

[ADD-EXT-SKILL 활성화]

## 목표

tpm 플러그인에 외부호출 스킬(ext-{대상플러그인})을 추가함.
DMAP 리소스 마켓플레이스에서 플러그인 목록을 탐색하고,
선택한 플러그인의 명세서를 기반으로 External 유형 스킬을 생성함.

## 활성화 조건

사용자가 `/tpm:add-ext-skill` 호출 시.

## 워크플로우

### Step 1: 대상 플러그인 탐색 (`ulw` 활용)

dmap 리소스 마켓플레이스에서 플러그인 카탈로그 다운로드:
```bash
curl https://raw.githubusercontent.com/unicorn-plugins/dmap/refs/heads/main/resources/plugin-resources.md > .dmap/plugin-resources.md
```
다운로드 실패 시:
- `.dmap/plugin-resources.md` 캐시 파일이 있으면 재사용
- 없으면 사용자에게 대상 플러그인명 직접 입력 요청

`.dmap/plugin-resources.md`의 플러그인 목록 파악.

### Step 2: 대상 플러그인 선택

사용자에게 추가할 플러그인 선택 요청.
이미 `skills/ext-{플러그인명}/SKILL.md`가 존재하면:
- "이미 ext-{플러그인명} 스킬이 존재합니다." 안내 후 중단

### Step 3: 플러그인 명세서 다운로드 (`ulw` 활용)

선택한 플러그인의 명세서 다운로드:
```bash
curl https://raw.githubusercontent.com/unicorn-plugins/dmap/refs/heads/main/resources/plugins/{분류}/{name}.md > .dmap/plugins/{name}.md
```
다운로드 실패 시:
- 캐시 파일이 있으면 재사용
- 없으면 사용자에게 안내하고 중단

`.dmap/plugins/{name}.md` 로드.

### Step 4: 도메인 컨텍스트 수집 (`ulw` 활용)

- `.dmap/tpm/requirements.md` (요구사항 정의서) 로드
- `.claude-plugin/plugin.json` (플러그인 메타데이터) 로드

### Step 5: ext-{대상플러그인} External 스킬 생성 (`ulw` 활용)

아래 "## 참고사항"의 External 유형 표준 골격을 기반으로 SKILL.md 작성:
- `skills/ext-{대상플러그인}/SKILL.md`
- 명세서의 제공 스킬(FQN), ARGS 스키마, 실행 경로를 반영
- 요구사항 정의서에서 tpm 도메인 컨텍스트(공연 기획 도메인)를 추출하여 반영

### Step 6: commands/ 진입점 생성 (`ulw` 활용)

```
commands/ext-{대상플러그인}.md:
---
description: {대상플러그인} 외부호출 스킬 실행
allowed-tools: Read, Write, Edit, Bash, Task, Skill
---

~/.claude/plugins/cache/tpm/tpm/ 하위 최신 버전 디렉토리의 skills/ext-{대상플러그인}/SKILL.md 파일을 읽고 실행하세요.
```

### Step 7: help 스킬 업데이트 (`ulw` 활용)

`skills/help/SKILL.md`의 슬래시 명령 테이블에 행 추가:
```
| `/tpm:ext-{대상플러그인}` | {대상플러그인} 외부호출 스킬 실행 |
```

## 명령어

| 명령 | 설명 |
|------|------|
| `/tpm:add-ext-skill` | 외부호출 스킬 추가 시작 |

## 참고사항

### External 유형 표준 골격 (ext-{대상플러그인} SKILL.md 작성 시 사용)

```markdown
---
name: ext-{대상플러그인}
description: 외부 플러그인 위임으로 {대상플러그인} 워크플로우 실행
type: external
user-invocable: true
---

# {대상플러그인}과 연동

[EXT-{대상플러그인} 활성화]

## 목표

{대상플러그인} 플러그인의 워크플로우를 활용하여 {목적}을 수행함.
tpm의 공연 기획 도메인 컨텍스트를 수집하고 적절한 경로를 분기하여 외부 스킬에 위임.

## 선행 요구사항

{대상플러그인} 플러그인이 설치되어 있어야 함.
확인: `claude plugin list`
설치: `claude plugin marketplace add {org}/{repo}`, `claude plugin install {plugin}@{marketplace}`

## 활성화 조건

사용자가 `/tpm:ext-{대상플러그인}` 호출 시 또는 관련 키워드 감지 시.

## 크로스-플러그인 스킬 위임 규칙

> 스킬 목록은 대상 플러그인 명세서의 "제공 스킬" 섹션을 참조.

| 외부 스킬 | FQN | 용도 |
|----------|-----|------|
| {스킬 1} | `{FQN 1}` | {설명} |

## 도메인 컨텍스트 수집

> 수집 대상은 대상 플러그인 명세서의 "도메인 컨텍스트 수집 가이드"를 참조.

| 수집 대상 | 소스 | 용도 |
|----------|------|------|
| tpm 플러그인 메타데이터 | `.claude-plugin/plugin.json` | 플러그인 식별 |
| 공연 기획 요구사항 | `.dmap/tpm/requirements.md` | 도메인 컨텍스트 |
| 진행 중인 제안서 | `output/04-proposal-*.md` (있는 경우) | 현재 기획 컨텍스트 |

## 워크플로우

### Phase 0: 선행 확인 (`ulw` 활용)

외부 플러그인 설치 확인. 미설치 시 설치 안내.

### Phase 1: 도메인 컨텍스트 수집 (`ulw` 활용)

컨텍스트 수집 테이블에 따라 메타데이터와 요구사항 수집.

### Phase 2: 경로 분기 결정

수집된 컨텍스트를 기반으로 실행 경로 결정.

> 경로 결정은 대상 플러그인 명세서의 "실행 경로" 섹션 참조.

| 조건 | 경로 | 외부 스킬 |
|------|------|----------|
| {조건} | {경로명} | {스킬} |

### Phase 3: 외부 스킬 위임 → Skill: {external-skill}

- **INTENT**: {위임 목적}
- **ARGS**: {
    "source_plugin": "tpm",
    ... (명세서의 ARGS 스키마 채움)
  }
- **RETURN**: {기대 결과}

### Phase 4: 결과 검증 및 보고 (`ulw` 활용)

산출물 존재 확인 후 사용자에게 완료 보고.

## 완료 조건

- [ ] 외부 플러그인 설치 확인
- [ ] 도메인 컨텍스트 수집 완료
- [ ] 외부 스킬 위임 및 워크플로우 완료
- [ ] 산출물 존재 확인

## 검증 프로토콜

산출물 파일 존재 확인 및 내용 검증.

## 상태 정리

완료 시 임시 파일 없음.

## 취소

사용자가 "취소", "중단" 요청 시 즉시 중단.

## 재개

마지막 완료된 Phase부터 재시작 가능.

## 스킬 부스팅

| 단계 | OMC 스킬 | 목적 |
|------|----------|------|
| Phase 0~1 | `ulw` | 수집 작업 완료 보장 |
| Phase 4 | `ulw` | 검증 + 보고 완료 보장 |

## MUST 규칙

| # | 규칙 |
|---|------|
| 1 | 외부 플러그인 설치 여부를 Phase 0에서 반드시 확인 |
| 2 | 도메인 컨텍스트 수집 완료 후 외부 스킬에 위임 |
| 3 | Skill→Skill 입력 전달 규약 준수 (ARGS에 source_plugin: "tpm" 포함) |

## MUST NOT 규칙

| # | 금지 사항 |
|---|----------|
| 1 | 외부 플러그인의 내부 워크플로우를 직접 실행하지 않음 |
| 2 | 자체 Agent를 생성하지 않음 |

## 검증 체크리스트

- [ ] 선행 요구사항 섹션에 외부 플러그인 설치 확인 방법이 있는가
- [ ] 도메인 컨텍스트 수집 대상에 tpm 고유 컨텍스트가 포함되었는가
- [ ] Skill→Skill 입력 전달 규약이 적용되었는가
```

## MUST 규칙

| # | 규칙 |
|---|------|
| 1 | 플러그인 목록을 마켓플레이스에서 동적으로 탐색 |
| 2 | 중복 스킬 존재 여부 확인 후 생성 |
| 3 | help 스킬 업데이트 필수 |
| 4 | External 유형 표준 골격을 준수하여 스킬 생성 |

## MUST NOT 규칙

| # | 금지 사항 |
|---|----------|
| 1 | 기존 ext-{} 스킬을 덮어쓰지 않음 (중복 확인 후 중단) |
| 2 | help 스킬의 테이블 구조(헤더, 구분선)를 훼손하지 않음 |

## 검증 체크리스트

- [ ] 플러그인 마켓플레이스 탐색 단계가 있는가
- [ ] 중복 스킬 확인 로직이 있는가
- [ ] External 유형 표준 골격이 ## 참고사항 섹션에 인라인되었는가
- [ ] help 스킬 업데이트 단계가 있는가
