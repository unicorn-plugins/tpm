# TPM 로컬 리소스 카탈로그

> tpm 플러그인의 로컬 리소스 목록. 에이전트와 스킬이 이 카탈로그를 참조하여 리소스를 활용함.

---

## 가이드

| 가이드명 | 설명 | 경로 |
|---------|------|------|
| theater-production-guide | 공연 기획 도메인 전문 가이드 (예산 항목, 시장 분석 프레임워크, 마케팅 채널, 용어 사전 포함) | `guides/theater-production-guide.md` |

## 템플릿

| 템플릿명 | 설명 | 경로 |
|---------|------|------|
| proposal-template | 기획 제안서 마크다운 템플릿 (7개 섹션 구조화) | `templates/proposal-template.md` |
| presentation-outline-template | 경영진 발표용 프레젠테이션 개요 템플릿 (9슬라이드 구조) | `templates/presentation-outline-template.md` |

## 도구

| 도구명 | 설명 | 경로 |
|--------|------|------|
| generate_image | Gemini (Nano Banana) 기반 이미지 생성 도구 (DMAP 마켓에서 복사). 실행 시 --api-key 파라미터 사용 필수 | `../gateway/tools/generate_image.py` |
