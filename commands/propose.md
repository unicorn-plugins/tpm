---
description: 공연 기획 제안서 자동 생성 (리서치 → 분석 → 예산 → 기획서 → 이미지 → 프레젠테이션)
allowed-tools: Read, Write, Edit, Bash, Glob, Grep, Task, Skill, WebSearch, WebFetch
---

## {PLUGIN_DIR} 찾기 
1. CLAUDE.md에서 `{plugin-name}`에 해당하는 `{PLUGIN_DIR}`을 읽음. 없으면 `2~6`번 수행    
2. 아래 후보 경로 중 존재하는 첫 번째를 `PLUGIN_BASE_DIR`로 선택: {plugin-name}: tpm
   - `/mnt/.local-plugins/cache/unicorn/{plugin-name}` (Cowork VM)
   - `~/.claude/plugins/cache/unicorn/{plugin-name}` (Mac/Linux CLI)
   - `%APPDATA%/Claude/plugins/cache/unicorn/{plugin-name}` (Windows CLI)
3. `PLUGIN_BASE_DIR` 하위의 버전 디렉토리를 시맨틱 버전 비교하여 최신 버전 선택
4. 해당 디렉토리의 절대 경로를 `{PLUGIN_DIR}`에 바인딩
5. 이후 모든 `{PLUGIN_DIR}/...` 경로를 절대 경로로 치환하여 파일을 읽음
6. 현재 프로젝트의 CLAUDE.md에 `{plugin-name}: {PLUGIN_DIR}`을 기록하여 이후 중복 계산 안하게 함     

## Skill 실행  
`{PLUGIN_DIR}/skills/propose/SKILL.md 파일을 읽고 실행하세요.
