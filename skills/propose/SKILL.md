---
name: propose
description: 공연 기획 제안서 자동 생성 — 11단계 전 과정 오케스트레이션 (리서치→시장분석→문제가설→킹핀→방향성→컨셉후보→핵심컨셉→예산→제안서→이미지→PPT)
type: orchestrator
user-invocable: true
---

# Propose

[PROPOSE 활성화]

## 목표

사용자가 제시한 연극/뮤지컬 주제 또는 후보 작품을 입력하면,
리서치 → 시장 분석 → 문제 가설 → 킹핀 & 방향성 → 공연 컨셉 후보 → 핵심 컨셉 선정 → 예산 계획 → 기획 제안서 → 컨셉 이미지 → 프레젠테이션 개요까지
11단계 전 과정을 자동으로 오케스트레이션하여 최종 산출물을 생성함.

## 활성화 조건

사용자가 `/tpm:propose` 호출 시 또는 "기획", "제안서", "공연 기획", "뮤지컬 기획" 키워드 감지 시.

## 에이전트 호출 규칙

### 에이전트 FQN

| 에이전트 | FQN | 티어 |
|----------|-----|------|
| researcher | `tpm:researcher:researcher` | MEDIUM |
| market-analyst | `tpm:market-analyst:market-analyst` | HIGH |
| budget-planner | `tpm:budget-planner:budget-planner` | MEDIUM |
| proposal-writer | `tpm:proposal-writer:proposal-writer` | MEDIUM |
| visual-creator | `tpm:visual-creator:visual-creator` | LOW |

### 프롬프트 조립 절차

1. `agents/{agent-name}/` 에서 3파일 로드 (AGENT.md + agentcard.yaml + tools.yaml)
2. `gateway/runtime-mapping.yaml` 참조하여 구체화:
   - 모델 구체화: tier → tier_mapping에서 모델 결정
   - 툴 구체화: tools.yaml 추상 도구 → tool_mapping에서 실제 도구 결정
   - 금지액션 구체화: forbidden_actions → action_mapping에서 제외 도구 결정
   - 최종 도구 = (구체화된 도구) - (제외 도구)
3. 프롬프트 조립: AGENT.md + agentcard.yaml + tools.yaml
4. 인격 주입: agentcard.yaml의 persona → 프롬프트 앞에 인격 컨텍스트 추가
   "당신은 {persona.profile.nickname}입니다. 답변 시 별명 '{persona.profile.nickname}'를 표시하세요. {persona.style} {persona.background}"
5. Task(subagent_type=FQN, model=구체화된 모델, prompt=조립된 프롬프트) 호출

## 워크플로우

이 워크플로우의 모든 단계는 `/oh-my-claudecode:ralph` 패턴을 활용하여 완료까지 지속 실행함.

### Phase 0: 초기 설정

1. 사용자에게 기획할 연극/뮤지컬 주제 또는 후보 작품명 입력 요청
2. 작품명을 기반으로 디렉토리명 결정: `{작품명-slug}` (공백은 하이픈, 한글 그대로 사용)
   - 예: "해와달을 모티브로 한 어린이 연극" → `해와달이된오누이`, "신데렐라 뮤지컬" → `신데렐라`
   - **이후 모든 산출물 경로는 `output/{작품명}/` 하위에 저장**
3. `output/{작품명}/images/` 디렉토리 생성 확인
4. 기존 `output/{작품명}/` 디렉토리가 있으면 덮어쓰기 여부 확인

### Phase 1: 작품 리서치 → Agent: researcher (`/oh-my-claudecode:research` 활용)

- **TASK**: 사용자가 제시한 주제/작품에 대한 국내외 자료 수집 및 유사 공연 레퍼런스 리서치
- **EXPECTED OUTCOME**: `output/{작품명}/01-research.md` — 후보 작품 리스트, 유사 공연 3건 이상, 레퍼런스 자료
- **MUST DO**: 최소 3개 이상 유사 공연 레퍼런스 포함, 국내외 자료 교차 수집
- **MUST NOT DO**: 시장 분석, 예산 산정, 기획서 작성 금지
- **CONTEXT**: 사용자가 입력한 주제/작품명

### Phase 2: 시장·관객 분석 → Agent: market-analyst (`/oh-my-claudecode:analyze` 활용)

- **TASK**: Phase 1 리서치 결과를 기반으로 시장 동향, 타겟 관객층, 경쟁 공연 분석 및 마케팅 전략 수립
- **EXPECTED OUTCOME**: `output/{작품명}/02-market-analysis.md` — 시장 분석, 타겟 관객 프로파일, 마케팅 전략안
- **MUST DO**: 경쟁 공연 최소 2건 비교 분석 포함
- **MUST NOT DO**: 예산 산정, 기획서 작성 금지
- **CONTEXT**: `output/{작품명}/01-research.md` 파일 경로 전달

### Phase 3: 문제 가설 설정 → Agent: market-analyst (`/oh-my-claudecode:analyze` 활용)

- **GUIDE**: `resources/guides/03-problem-hypothesis-guide.md`
- **TASK**: 리서치 및 시장·관객 분석 데이터를 기반으로 관객·시장이 겪는 핵심 문제 3개를 도출하고, 각각 5WHY 분석으로 근본원인을 도출. 근본원인 해소 시 관객·제작사가 얻는 비즈니스 가치를 각각 3개 이하로 정의
- **EXPECTED OUTCOME**: `output/{작품명}/03-problem-hypothesis.md` — 문제 3개, 5WHY 분석, 다층적 근본원인 검토, 비즈니스 가치(관객/제작사)
- **MUST DO**: 가이드(`resources/guides/03-problem-hypothesis-guide.md`) 준수 / 문제는 관객·시장 관점으로 작성 / 01-research.md 및 02-market-analysis.md 데이터를 근거로 활용
- **MUST NOT DO**: 솔루션(공연 컨셉) 포함 금지 / 제작자 관점의 문제 작성 금지
- **CONTEXT**: `output/{작품명}/01-research.md`, `output/{작품명}/02-market-analysis.md`

### Phase 4: 킹핀 문제 + 방향성 정의 → Agent: market-analyst (`/oh-my-claudecode:analyze` 활용)

- **GUIDE**: `resources/guides/04-direction-guide.md`
- **TASK**: 문제 가설의 3개 문제를 영향력·빈도·심각도·근본성·해결가능성 5가지 기준으로 평가하여 킹핀 문제를 선정하고, `'{타겟 관객유형}는 {목적}을 위하여 {원하는 공연 경험}이 필요하다.'` 형식의 Needs Statement로 공연 방향성을 정의
- **EXPECTED OUTCOME**: `output/{작품명}/04-direction.md` — 문제 인과관계 분석, 5가지 기준 평가표, 킹핀 문제 선정, Needs Statement
- **MUST DO**: 가이드(`resources/guides/04-direction-guide.md`) 준수 / 5가지 기준 평가표 포함 / Needs Statement는 특정 작품/형식이 아닌 경험으로 표현
- **MUST NOT DO**: 킹핀 문제를 데이터 없이 직감으로 선정 금지 / Needs Statement에 구체적 솔루션 포함 금지
- **CONTEXT**: `output/{작품명}/03-problem-hypothesis.md`, `output/{작품명}/02-market-analysis.md`

### Phase 5: 공연 컨셉 후보 도출 → Agent: market-analyst(병렬), proposal-writer(리드·수렴) (`/oh-my-claudecode:ralph` 활용)

- **GUIDE**: `resources/guides/05-ideation-guide.md`
- **TASK**: market-analyst와 proposal-writer가 각자 SCAMPER·Steal & Synthesize 기법으로 Big Idea 3개, Little Win Idea 2개, Crazy Idea 1개를 도출(병렬)한 후, proposal-writer가 유사도 평가표(컨셉 70%/형식 30%)를 작성하여 유사도 0.7 이상 아이디어를 합쳐 공연 컨셉 후보를 수렴
- **EXPECTED OUTCOME**: `output/{작품명}/05-concept-candidates.md` — 에이전트별 아이디어 표, 유사도 평가, 수렴된 컨셉 후보 3~5개(각 핵심 경험 가치·공연 형태·차별화·Needs Statement 연결 포함)
- **실행**: market-analyst 아이디어 발상은 `Task(run_in_background=true)`로 동시 실행, 수렴은 proposal-writer가 순차 수행
- **MUST DO**: 가이드(`resources/guides/05-ideation-guide.md`) 준수 / 각 후보의 Needs Statement 연결 명시 / 타 분야 벤치마킹 사례 최소 3개 포함
- **MUST NOT DO**: 유사도 0.7 미만 아이디어 강제 합치기 금지 / 01-research.md 레퍼런스와 동일한 공연 복사 금지
- **CONTEXT**: `output/{작품명}/01-research.md`, `output/{작품명}/02-market-analysis.md`, `output/{작품명}/03-problem-hypothesis.md`, `output/{작품명}/04-direction.md`

### Phase 6: 평가 + 핵심 컨셉 선정 → Agent: market-analyst, proposal-writer(리드) (`/oh-my-claudecode:ralph` 활용)

- **GUIDE**: `resources/guides/06-solution-selection-guide.md`
- **TASK**: market-analyst와 proposal-writer가 관객 매력도(A) 3표·실현 가능성(F) 3표를 투표(병렬)한 후, proposal-writer가 결과를 집계하고 X축=실현가능성/Y축=관객매력도의 2×2 매트릭스를 SVG로 시각화하여 핵심 공연 컨셉 3개 이하를 선정
- **EXPECTED OUTCOME**: `output/{작품명}/06-core-concept.md` — 투표 결과 집계표, 우선순위 매트릭스 SVG(`output/{작품명}/06-concept-matrix.svg`), 핵심 공연 컨셉(3개 이하, 선정 근거·Needs Statement 연결 포함)
- **실행**: 투표는 2개 에이전트를 `Task(run_in_background=true)`로 동시 실행, 집계 및 선정은 proposal-writer가 순차 수행
- **MUST DO**: 가이드(`resources/guides/06-solution-selection-guide.md`) 준수 / No Brainers 영역 컨셉 우선 선정 / 최소 1개 컨셉의 Needs Statement 연결 검증
- **MUST NOT DO**: 투표 없이 직감으로 선정 금지 / 핵심 컨셉 3개 초과 선정 금지
- **CONTEXT**: `output/{작품명}/05-concept-candidates.md`, `output/{작품명}/04-direction.md`

### Phase 7: 예산 계획 → Agent: budget-planner (`ulw` 활용)

- **TASK**: 선정된 핵심 공연 컨셉과 공연 규모에 맞는 항목별 예산 계획서 및 손익분기점 분석
- **EXPECTED OUTCOME**: `output/{작품명}/07-budget-plan.md` — 항목별 예산 상세표, BEP 분석, 시나리오별 수익
- **MUST DO**: 도메인 가이드(`resources/guides/theater-production-guide.md`)의 표준 예산 항목 참조 / 핵심 컨셉 기반 예산 산정
- **MUST NOT DO**: 마케팅 전략 판단, 기획서 작성, 외부 검색 금지
- **CONTEXT**: `output/{작품명}/01-research.md`, `output/{작품명}/02-market-analysis.md`, `output/{작품명}/06-core-concept.md`

### Phase 8: 기획 제안서 작성 → Agent: proposal-writer (`/oh-my-claudecode:ralph` 활용)

- **TASK**: 선행 분석 및 핵심 컨셉 데이터를 통합하여 기획 제안서 작성
- **EXPECTED OUTCOME**: `output/{작품명}/08-proposal-{작품명}.md` — 7섹션 + 부록 구조의 기획 제안서
- **MUST DO**: `resources/templates/proposal-template.md` 템플릿 준수 / 01~06 분석 결과 모두 반영 / 핵심 공연 컨셉(06-core-concept.md) 중심으로 구성
- **MUST NOT DO**: 선행 분석 결과를 임의로 변경하거나 생략 금지
- **CONTEXT**: `output/{작품명}/01-research.md`, `output/{작품명}/02-market-analysis.md`, `output/{작품명}/04-direction.md`, `output/{작품명}/06-core-concept.md`, `output/{작품명}/07-budget-plan.md`, `resources/templates/proposal-template.md`

### Phase 9: 컨셉 이미지 생성 → Agent: visual-creator (`ulw` 활용)

- **TASK**: 핵심 공연 컨셉을 반영한 포스터/무드보드 이미지 및 핵심 장면 이미지 생성
- **EXPECTED OUTCOME**: `output/{작품명}/images/concept-poster.png` + `output/{작품명}/images/scene-01.png` (최소 2장)
- **MUST DO**: `--api-key` 파라미터로 GEMINI_API_KEY 전달하여 generate_image.py 실행. 실행 명령: `python gateway/tools/generate_image.py --api-key {GEMINI_API_KEY} --prompt "{프롬프트}" --output-dir output/{작품명}/images --output-name {파일명}`
- **MUST NOT DO**: 기획서 내용 수정 금지, 네트워크 접근 금지
- **CONTEXT**: `output/{작품명}/08-proposal-{작품명}.md`, `output/{작품명}/06-core-concept.md`, GEMINI_API_KEY 값
- **이미지 생성 실패 시**: "GEMINI_API_KEY 미설정으로 이미지 생성을 건너뜁니다. `/tpm:setup`을 실행하여 API 키를 설정하세요." 안내 후 Phase 10으로 진행

### Phase 10: 프레젠테이션 구성 → Agent: proposal-writer (`/oh-my-claudecode:ralph` 활용)

- **TASK**: 기획 제안서를 경영진 발표용 9슬라이드 개요로 변환
- **EXPECTED OUTCOME**: `output/{작품명}/10-presentation-{작품명}.md` — 9슬라이드 구조 PPT 개요
- **MUST DO**: `resources/templates/presentation-outline-template.md` 템플릿 준수 / 이미지 삽입 위치 표시 / 핵심 컨셉 및 방향성 강조
- **MUST NOT DO**: 기획서 원본 수정 금지
- **CONTEXT**: `output/{작품명}/08-proposal-{작품명}.md`, `output/{작품명}/04-direction.md`, Phase 9 이미지 경로 목록, `resources/templates/presentation-outline-template.md`

### Phase 11: 완료 보고

모든 산출물 파일 존재 확인 후 사용자에게 최종 보고:
```
✅ 공연 기획 제안서 자동 생성 완료

## 산출물 목록
- 리서치 보고서:       output/{작품명}/01-research.md
- 시장 분석 보고서:    output/{작품명}/02-market-analysis.md
- 문제 가설:          output/{작품명}/03-problem-hypothesis.md
- 킹핀 & 방향성:      output/{작품명}/04-direction.md
- 컨셉 후보:          output/{작품명}/05-concept-candidates.md
- 핵심 공연 컨셉:      output/{작품명}/06-core-concept.md
- 예산 계획서:         output/{작품명}/07-budget-plan.md
- 기획 제안서:         output/{작품명}/08-proposal-{작품명}.md
- 컨셉 이미지:         output/{작품명}/images/concept-poster.png
- 프레젠테이션 개요:   output/{작품명}/10-presentation-{작품명}.md
```

## 완료 조건

- [ ] Phase 1~10 모두 완료
- [ ] `output/{작품명}/` 디렉토리에 산출물 파일 존재
- [ ] 핵심 공연 컨셉(06-core-concept.md) 생성 확인
- [ ] 기획 제안서(08-proposal-*.md) 생성 확인
- [ ] 프레젠테이션 개요(10-presentation-*.md) 생성 확인

## 검증 프로토콜

Phase 4 완료 후 market-analyst의 Needs Statement 확인:
- 특정 솔루션이 아닌 경험 방향으로 표현되었는가
- 킹핀 문제와 연결되어 있는가

Phase 6 완료 후 핵심 컨셉 확인:
- No Brainers 영역 컨셉이 포함되었는가
- Needs Statement와 연결되어 있는가

Phase 8 완료 후 proposal-writer의 결과를 확인:
- 7개 섹션이 모두 채워졌는가
- 선행 분석 데이터가 통합되었는가

Phase 11에서 모든 산출물 파일 존재 확인.

## 상태 정리

완료 시 `.dmap/state/propose-state.json` 파일이 있으면 삭제.

## 취소

사용자가 "취소", "중단", "cancelomc" 요청 시 즉시 중단.

## 재개

마지막 완료된 Phase부터 재시작 가능.
`output/{작품명}/` 디렉토리의 기존 파일을 확인하여 완료된 Phase 판단.

| 파일 존재 여부 | 재개 시작 Phase |
|-------------|----------------|
| 01-research.md 없음 | Phase 1 |
| 02-market-analysis.md 없음 | Phase 2 |
| 03-problem-hypothesis.md 없음 | Phase 3 |
| 04-direction.md 없음 | Phase 4 |
| 05-concept-candidates.md 없음 | Phase 5 |
| 06-core-concept.md 없음 | Phase 6 |
| 07-budget-plan.md 없음 | Phase 7 |
| 08-proposal-*.md 없음 | Phase 8 |
| images/ 없음 | Phase 9 |
| 10-presentation-*.md 없음 | Phase 10 |

## MUST 규칙

| # | 규칙 |
|---|------|
| 1 | 11개 Phase를 순서대로 실행 (선행 결과가 다음 Phase의 CONTEXT로 전달됨) |
| 2 | 에이전트 호출 시 3파일 로드 → runtime-mapping.yaml 구체화 → Task 호출 절차 준수 |
| 3 | Phase 3~6 가이드 파일(`resources/guides/03~06-*.md`)을 에이전트에 CONTEXT로 전달 |
| 4 | Phase 5·6의 병렬 태스크는 `Task(run_in_background=true)`로 동시 실행 |
| 5 | Phase 9(이미지 생성) 실패 시 안내 후 Phase 10으로 진행 (중단 금지) |
| 6 | 각 Phase 완료 시 `output/{작품명}/` 디렉토리에 중간 산출물 저장 |
| 7 | propose 스킬은 라우팅/오케스트레이션만 담당 — 직접 분석/작성 금지 |

## MUST NOT 규칙

| # | 금지 사항 |
|---|----------|
| 1 | propose 스킬이 직접 리서치, 분석, 문제 가설 설정, 기획서 작성을 수행하지 않음 |
| 2 | 이미지 생성 실패를 이유로 전체 워크플로우를 중단하지 않음 |
| 3 | 선행 Phase 결과 없이 다음 Phase를 진행하지 않음 |
| 4 | Phase 3·4의 가이드를 무시하고 임의로 문제 가설·킹핀을 정의하지 않음 |

## 검증 체크리스트

- [ ] 에이전트 FQN이 모두 정확한가 (tpm:{name}:{name} 형식)
- [ ] 에이전트 호출 규칙(3파일 로드, runtime-mapping 참조, Task 호출)이 명시되었는가
- [ ] 각 Phase에 5항목(TASK, EXPECTED OUTCOME, MUST DO, MUST NOT DO, CONTEXT)이 포함되었는가
- [ ] Phase간 산출물 경로(output/{작품명}/0N-*.md)가 명확한가
- [ ] Phase 3~6에 가이드 파일 참조가 명시되었는가
- [ ] Phase 5·6 병렬 실행 방식이 명시되었는가
- [ ] Phase 9 실패 시 처리 방안이 명시되었는가
- [ ] 완료 조건, 검증 프로토콜, 상태 정리, 취소/재개 섹션이 포함되었는가
