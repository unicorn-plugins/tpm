# 핵심 공연 컨셉 선정 가이드

## 목적

공연 컨셉 후보들을 투표 방식으로 평가하고 우선순위 매트릭스로 시각화하여,
기획서 작성과 예산 계획의 기반이 될 핵심 공연 컨셉(1~3개)을 선정함.

## 입력 (이전 단계 산출물)

| 산출물 | 파일 경로 | 활용 방법 |
|--------|----------|----------|
| 공연 컨셉 후보 | `output/{작품명}/05-concept-candidates.md` | 투표 및 평가 대상 컨셉 목록 |

## 출력 (이 단계 산출물)

| 산출물 | 파일 경로 |
|--------|----------|
| 핵심 공연 컨셉 | `output/{작품명}/06-core-concept.md` |

## 방법론

### 1단계: 우선순위 평가 (투표)

#### 투표 방식

각 에이전트가 두 가지 기준으로 독립적으로 투표함:

| 투표 기준 | 설명 | 표수 |
|---------|------|------|
| **A (Audience Appeal)** | 관객 매력도·시장 잠재력이 높은 컨셉 | 각 3표 |
| **F (Feasibility)** | 제작 실현 가능성이 높은 컨셉 | 각 3표 |

#### 투표 규모

| 에이전트 | A 투표 | F 투표 |
|---------|--------|--------|
| market-analyst | 3표 | 3표 |
| proposal-writer | 3표 | 3표 |
| **합계** | **6표** | **6표** |

### 2단계: 우선순위 매트릭스

#### 축 설정
- **X축**: 제작 실현 가능성 (낮음 → 높음)
- **Y축**: 관객 매력도 / 시장 임팩트 (낮음 → 높음)

#### 4개 영역

| 영역 | 위치 | 전략 |
|------|------|------|
| **No Brainers** | 실현 가능성 높음 + 관객 매력도 높음 | 1순위 — 즉시 추진 |
| **Big Bets** | 실현 가능성 낮음 + 관객 매력도 높음 | 2순위 — 전략적 투자 검토 |
| **Utilities** | 실현 가능성 높음 + 관객 매력도 낮음 | 3순위 — 보완적 활용 |
| **Unwise** | 실현 가능성 낮음 + 관객 매력도 낮음 | 4순위 — 보류/폐기 |

#### 핵심 컨셉 선정 기준
1. **No Brainers** 영역의 컨셉 우선 선정
2. No Brainers가 부족하면 **Big Bets** 컨셉도 포함 (예산·파트너십으로 실현 가능성 보완 가능한 경우)
3. 핵심 공연 컨셉은 **3개 이하**로 선정
4. 최소 1개는 반드시 Needs Statement(04-direction.md)와 강하게 연결되어야 함

### SVG 매트릭스 작성 형식

```xml
<svg width="600" height="500" xmlns="http://www.w3.org/2000/svg">
  <rect width="600" height="500" fill="#f9f9f9"/>
  <!-- X축 -->
  <line x1="100" y1="400" x2="550" y2="400" stroke="#333" stroke-width="2"/>
  <!-- Y축 -->
  <line x1="100" y1="400" x2="100" y2="50" stroke="#333" stroke-width="2"/>
  <!-- 중심선 -->
  <line x1="325" y1="50" x2="325" y2="400" stroke="#999" stroke-width="1" stroke-dasharray="5,5"/>
  <line x1="100" y1="225" x2="550" y2="225" stroke="#999" stroke-width="1" stroke-dasharray="5,5"/>
  <!-- 축 레이블 -->
  <text x="320" y="440" text-anchor="middle" font-size="14">제작 실현 가능성</text>
  <text x="50" y="230" text-anchor="middle" font-size="14"
        transform="rotate(-90, 50, 230)">관객 매력도</text>
  <!-- 영역 배경 -->
  <rect x="100" y="50" width="225" height="175" fill="#ffcccc" opacity="0.3"/>
  <rect x="325" y="50" width="225" height="175" fill="#ccffcc" opacity="0.3"/>
  <rect x="100" y="225" width="225" height="175" fill="#ffffcc" opacity="0.3"/>
  <rect x="325" y="225" width="225" height="175" fill="#cce5ff" opacity="0.3"/>
  <!-- 영역명 -->
  <text x="210" y="130" text-anchor="middle" font-size="12" font-weight="bold">Big Bets</text>
  <text x="435" y="130" text-anchor="middle" font-size="12" font-weight="bold">No Brainers</text>
  <text x="210" y="310" text-anchor="middle" font-size="12" font-weight="bold">Unwise</text>
  <text x="435" y="310" text-anchor="middle" font-size="12" font-weight="bold">Utilities</text>
  <!-- 컨셉 포인트 예시 -->
  <circle cx="450" cy="100" r="8" fill="#0066cc"/>
  <text x="465" y="105" font-size="12">C1</text>
  <!-- 범례 -->
  <text x="100" y="470" font-size="11">C1: {컨셉명}</text>
</svg>
```

## 출력 형식

### 06-core-concept.md

```markdown
# 핵심 공연 컨셉 선정

## 1. 투표 결과

| 컨셉 ID | 컨셉명 | 관객 매력도 (A) | 실현 가능성 (F) | A+F 합계 |
|--------|------|--------------|--------------|---------|
| C1 | {컨셉명} | {표수}/6 | {표수}/6 | {합계} |
| C2 | ... | | | |

## 2. 우선순위 매트릭스

![컨셉 우선순위 매트릭스](./06-concept-matrix.svg)

| 영역 | 해당 컨셉 |
|------|---------|
| No Brainers | {컨셉 목록} |
| Big Bets | {컨셉 목록} |
| Utilities | {컨셉 목록} |
| Unwise | {컨셉 목록} |

## 3. 핵심 공연 컨셉

### 핵심 컨셉 1: {컨셉명} ⭐ 최우선

- **선정 근거**: {선정 이유}
- **관객 매력도**: {A 투표수}/6
- **실현 가능성**: {F 투표수}/6
- **핵심 경험 가치**: {관객에게 제공하는 가치}
- **공연 형태**: {장르·규모·형식}
- **Needs Statement 연결**: {방향성과의 연결 설명}

### 핵심 컨셉 2: {컨셉명}
(동일 구조, 선택 시 작성)

## 4. 선정 과정 요약

{투표 결과 분석 → 매트릭스 배치 → 최종 선정 과정 간략 설명}
```

## 품질 기준

### 완료 체크리스트
- [ ] 투표 결과 집계표 포함 (2에이전트 × A3표/F3표)
- [ ] 우선순위 매트릭스 SVG 파일 생성
- [ ] 4개 영역 분류 완료
- [ ] 핵심 컨셉 3개 이하 선정
- [ ] No Brainers 영역 컨셉 우선 선정 원칙 준수
- [ ] 각 핵심 컨셉의 선정 근거 명시
- [ ] 최소 1개 컨셉의 Needs Statement 연결 확인
- [ ] 05-concept-candidates.md의 모든 후보가 투표 대상에 포함됨

### 정량 기준
- 투표 규모: A 6표 + F 6표 (2명 × 3표)
- 핵심 컨셉: 1~3개
- SVG 매트릭스: 1개

## 주의사항

- 에이전트별 독립 투표 (상호 영향 없이)
- 투표 결과 없이 직감만으로 선정 금지
- No Brainers 우선 선정 원칙 준수
- 핵심 컨셉은 이후 Phase 7(예산계획) 및 Phase 8(기획서)의 핵심 기반이 됨
- 선정된 컨셉이 04-direction.md의 Needs Statement를 달성할 수 있는지 최종 검증
