# AI Dev OS Plugin — Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](../../../LICENSE)

**AI Dev OS**의 4계층 모델을 Claude Code의 Skills / Hooks / Sub-agents로 실행할 수 있게 하는 플러그인입니다.

**[AI Dev OS](https://github.com/yunbow/ai-dev-os) 에코시스템의 일부입니다** — 암묵지를 강제 가능한 AI 코딩 규칙으로 변환하는 프레임워크 ([Lifespan Layers](https://github.com/yunbow/ai-dev-os#lifespan-layers--the-4-layer-model)).
프로젝트에 [AI Dev OS Rules](https://github.com/yunbow/ai-dev-os-rules-typescript)가 설정되어 있어야 합니다.

## 왜 이 플러그인인가?

AI Dev OS 가이드라인을 Claude Code의 **자동화 워크플로우**로 변환합니다:

- **11 Skills** — `/ai-dev-os-check`, `/ai-dev-os-scan`, `/ai-dev-os-extract` 등
- **3 Sub-agents** — 철학 어드바이저, 원칙 체커, 가이드라인 감사
- **Lifecycle Hooks** — 코드 변경 시 자동 검사, 커밋 전 리마인더
- **원 커맨드 설정** — `npx ai-dev-os init --rules typescript --plugin claude-code`

![AI Dev OS Check & Fix Report](../../../docs/images/ai-dev-os-check.png)

## 빠른 시작

```bash
npx ai-dev-os init --rules typescript --plugin claude-code
```

> submodule 추가, CLAUDE.md 템플릿 복사, hooks 병합을 자동으로 실행합니다.
> 자세한 내용은 [AI Dev OS CLI](https://github.com/yunbow/ai-dev-os-cli)를 참조하세요.

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0 및 프로젝트에 AI Dev OS 레이어 파일(L1-L3)이 필요합니다 ([TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python)).

<details>
<summary>수동 설정</summary>

```bash
# 1. AI Dev OS 규칙을 submodule로 추가
git submodule add https://github.com/yunbow/ai-dev-os-rules-typescript.git docs/ai-dev-os
# Python 프로젝트의 경우:
# git submodule add https://github.com/yunbow/ai-dev-os-rules-python.git docs/ai-dev-os

# 2. 이 플러그인을 submodule로 추가
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

3. `hooks/hooks.json`의 훅을 프로젝트의 `.claude/settings.json`에 복사
4. **Claude Code 채팅**에서 `/ai-dev-os-init`을 실행하여 4계층 구조 설정
5. 코딩 시작 — 훅이 자동으로 안내합니다

자세한 내용은 [운영 가이드](./docs/operation-guide.md)를 참조하세요.

</details>

## Skills (스킬)

| 스킬 | 명령 | 설명 |
|------|------|------|
| **Init** | `/ai-dev-os-init` | 초기화 마법사 — 30분 만에 프로젝트에 AI Dev OS 도입 |
| **Check** | `/ai-dev-os-check` | 가이드라인 준수 검사 (`git diff`, `--staged`, 브랜치 비교 지원) |
| **Scan** | `/ai-dev-os-scan` | 모든 소스 파일에 대한 프로젝트 전체 준수 스캔 |
| **Review** | `/ai-dev-os-review` | PR 전 셀프 리뷰 — L3 준수 + L2 설계 리뷰 + L1 정합성 통합 |
| **Extract** | `/ai-dev-os-extract` | 코드 리뷰 차이에서 새 규칙 추출 (룰 하베스팅) |
| **Why** | `/ai-dev-os-why` | 규칙의 근거를 L3→L2→L1까지 추적하여 설명 |
| **Plan** | `/ai-dev-os-plan` | 코딩 전 가이드라인 체크리스트가 포함된 구현 계획 생성 |
| **Ticket** | `/ai-dev-os-ticket` | 구현 요약과 체크리스트 후보가 포함된 티켓 생성 (로컬 파일 또는 GitHub Issue) |
| **Audit** | `/ai-dev-os-audit` | 4계층 건전성 감사: 의존성 규칙, 신선도, 커버리지, 일관성 |
| **Evolve** | `/ai-dev-os-evolve` | 최근 커밋을 분석하여 L1-L2 업데이트 제안 (SECI 나선) |
| **Report** | `/ai-dev-os-report` | 팀과 이해관계자를 위한 준수 요약 보고서 생성 |

## Sub-agents (서브 에이전트)

| 에이전트 | 모델 | 역할 |
|----------|------|------|
| `philosophy-advisor` | Opus | L1 철학에 기반한 아키텍처 결정 지원 |
| `principle-checker` | Sonnet | 코드 변경이 L2 원칙과 일치하는지 검증 |
| `guideline-auditor` | Sonnet | L3 가이드라인의 커버리지, 일관성, 신선도 감사 |

## Hooks (훅)

| 이벤트 | 트리거 | 동작 |
|--------|--------|------|
| PreToolUse (Write/Edit) | 코드 변경 시 | 경량 가이드라인 준수 검사 |
| PreToolUse (Bash: git commit) | 커밋 전 | `/ai-dev-os-check` 실행 알림 |
| PostToolUse (Write/Edit) | L1-L2 파일 편집 후 | 의존성 규칙 위반 경고 |

<details>
<summary>패키지 구조</summary>

```
ai-dev-os-plugin-claude-code/
├── .claude-plugin/
│   └── plugin.json              # 플러그인 매니페스트
├── skills/
│   ├── ai-dev-os-init/          # 초기화 마법사
│   ├── ai-dev-os-check/         # 가이드라인 준수 검사 (git diff)
│   ├── ai-dev-os-scan/          # 프로젝트 전체 준수 스캔
│   ├── ai-dev-os-review/        # PR 전 셀프 리뷰 (L1-L3)
│   ├── ai-dev-os-extract/       # 룰 하베스팅
│   ├── ai-dev-os-why/           # 규칙 근거 설명 (L3→L2→L1)
│   ├── ai-dev-os-audit/         # 4계층 건전성 감사
│   ├── ai-dev-os-plan/           # 가이드라인 기반 구현 계획
│   ├── ai-dev-os-ticket/        # 티켓 생성 (구현 요약 + 체크리스트 후보)
│   ├── ai-dev-os-evolve/        # SECI 나선 피드백 (L4→L1)
│   └── ai-dev-os-report/        # 준수 보고서 생성
├── agents/
│   ├── philosophy-advisor.md     # L1 철학 기반 아키텍처 판단
│   ├── principle-checker.md      # L2 원칙 정합성 검증
│   └── guideline-auditor.md      # L3 가이드라인 감사
├── hooks/
│   └── hooks.json                # 라이프사이클 이벤트 훅
├── templates/
│   ├── CLAUDE.md.template        # CLAUDE.md 템플릿
│   ├── ai-dev-os-starter/        # 최소 구성 템플릿
│   └── ai-dev-os-full/           # 전체 4계층 템플릿
└── settings.json                  # 기본 권한 설정
```

</details>

## 우선순위 캐스케이드

규칙이 충돌할 경우: 프레임워크 특정 > 공통 > 프로젝트 특정 > 판단 기준 > 철학. [→ 상세](https://github.com/yunbow/ai-dev-os/blob/main/spec/priority-cascade.md)

## 관련

| 저장소 | 설명 |
|---|---|
| [ai-dev-os](https://github.com/yunbow/ai-dev-os) | 프레임워크 사양 및 이론 |
| [ai-dev-os-rules-typescript](https://github.com/yunbow/ai-dev-os-rules-typescript) | TypeScript / Next.js / Node.js 가이드라인 |
| [ai-dev-os-rules-python](https://github.com/yunbow/ai-dev-os-rules-python) | Python / FastAPI 가이드라인 |
| [ai-dev-os-plugin-kiro](https://github.com/yunbow/ai-dev-os-plugin-kiro) | Kiro용 Steering Rules 및 Hooks |
| [ai-dev-os-plugin-cursor](https://github.com/yunbow/ai-dev-os-plugin-cursor) | Cursor Rules (.mdc) |
| [ai-dev-os-cli](https://github.com/yunbow/ai-dev-os-cli) | 설정 자동화 — `npx ai-dev-os init` |

## 라이선스

[MIT](../../../LICENSE)

---

Languages: [English](../../../README.md) | [日本語](../ja/README.md) | [简体中文](../zh-CN/README.md) | 한국어 | [Español](../es/README.md)
