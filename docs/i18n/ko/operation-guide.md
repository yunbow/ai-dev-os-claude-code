# AI Dev OS Plugin — Claude Code — 운영 가이드

## 목차

1. [전제 조건](#1-전제-조건)
2. [설치](#2-설치)
3. [초기 설정](#3-초기-설정)
4. [일상 워크플로우](#4-일상-워크플로우)
5. [정기 유지보수](#5-정기-유지보수)
6. [팀 온보딩](#6-팀-온보딩)
7. [언어 정책](#7-언어-정책)
8. [CI 통합](#8-ci-통합)
9. [문제 해결](#9-문제-해결)

---

## 1. 전제 조건

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) >= 1.0.0
- 프로젝트에 AI Dev OS 레이어 파일(L1-L3)이 설정되어 있을 것 (템플릿: [TypeScript](https://github.com/yunbow/ai-dev-os-rules-typescript) / [Python](https://github.com/yunbow/ai-dev-os-rules-python))

---

## 2. 설치

### 방법 A: Git Submodule (권장)

AI Dev OS 업데이트를 프로젝트와 분리하여 관리할 수 있습니다:

```bash
git submodule add https://github.com/yunbow/ai-dev-os-plugin-claude-code.git .claude/plugins/ai-dev-os
```

### 방법 B: 직접 클론

```bash
git clone https://github.com/yunbow/ai-dev-os-plugin-claude-code.git
cp -r ai-dev-os-plugin-claude-code/skills/ .claude/skills/
cp -r ai-dev-os-plugin-claude-code/agents/ .claude/agents/
```

### 훅 설정

`hooks/hooks.json`의 훅 정의를 프로젝트의 `.claude/settings.json`에 복사합니다:

```json
{
  "permissions": { ... },
  "hooks": {
    "PreToolUse": [ ... ],
    "PostToolUse": [ ... ],
    "UserPromptSubmit": [ ... ]
  }
}
```

전체 훅 설정은 `hooks/hooks.json`을 참조하세요.

#### 훅 병합 가이드

`.claude/settings.json`이 이미 존재하고 다른 설정을 포함하고 있는 경우, 훅을 수동으로 병합하세요:

#### 방법A: 수동 병합

1. `hooks/hooks.json`과 `.claude/settings.json`을 나란히 열기
2. `hooks/hooks.json`에서 각 배열(`PreToolUse`, `PostToolUse`, `UserPromptSubmit`)을 복사
3. `settings.json`에 이미 `hooks` 키가 있으면 기존 배열에 **추가**
4. `settings.json`에 `hooks` 키가 없으면 전체 `hooks` 객체를 추가

**확인**: 병합 후 `settings.json`의 `hooks` 키 아래에 `PreToolUse`, `PostToolUse`, `UserPromptSubmit` 배열이 포함되어 있는지 확인하세요.

---

## 3. 초기 설정

### 설정 마법사 실행

```text
/ai-dev-os-init [tech-stack]
```

예시:

```text
/ai-dev-os-init nextjs
```

마법사가 수행하는 작업:

1. 기술 스택, 프로젝트 규모, 기존 규칙 파일에 대해 질문
2. 기존 규칙 감지 및 가져오기 (`.cursorrules`, `CLAUDE.md`, `.eslintrc`)
3. 4계층 디렉토리 구조 생성
4. 가이드라인 참조와 우선순위 캐스케이드가 포함된 `CLAUDE.md` 생성
5. 선택적으로 git submodule 격리 설정

### 설정 확인

초기화 후 다음 구조를 확인합니다:

```text
ai-dev-os/
├── 01_philosophy/
├── 02_decision-criteria/
├── 03_guidelines/
│   ├── common/
│   └── frameworks/
└── 04_ai-frames/
```

---

## 4. 일상 워크플로우

### 4.1 구현 전 계획

중요한 변경의 경우, 가이드라인 기반 계획을 먼저 생성합니다:

```text
/ai-dev-os-plan JWT 사용자 인증 추가
```

수행 내용:

1. 요청을 분석하고 영향받는 파일 식별
2. 파일을 관련 AI Dev OS 가이드라인에 매핑
3. 적용 가능한 규칙의 체크리스트 생성
4. 코드 작성 전에 계획을 제시하여 승인 받기

훅이 구현 관련 프롬프트를 감지하면 `/ai-dev-os-plan` 사용을 제안합니다.

### 4.2 구현 대신 티켓 생성

구현을 나중으로 미루고 싶을 때(팀 배정이나 백로그 관리 등) 티켓을 생성합니다:

```text
/ai-dev-os-ticket JWT 사용자 인증 추가
```

수행 내용:

1. `/ai-dev-os-plan`과 동일한 분석 수행 (영향 파일, 가이드라인 매핑, 체크리스트)
2. 티켓 출력 대상 확인 (CLAUDE.md에 설정이 없는 경우):
   - **로컬 파일**: 지정 디렉토리에 `TICKET-001-add-user-auth.md`로 저장
   - **GitHub Issue**: `gh issue create`로 이슈 생성
3. 미리보기를 표시하여 승인 받기
4. 티켓 생성 — **코드는 작성하지 않음**

티켓 출력 대상을 사전 설정하려면 프로젝트의 `CLAUDE.md`에 추가합니다:

```markdown
## Ticket Settings
- output: file
- path: docs/tickets/phase1
```

GitHub Issue의 경우:

```markdown
## Ticket Settings
- output: github-issue
```

### 4.3 코드 작성

평소처럼 코드를 작성합니다. 훅이 자동으로:

- 매 Write/Edit 작업마다 가이드라인 준수를 검사
- L1-L2 파일 편집 시 의존성 규칙 위반 경고
- 커밋 전 준수 검사 실행 알림

### 4.4 커밋 전

준수 검사를 실행합니다:

```text
/ai-dev-os-check
```

수행 내용:

1. `CLAUDE.md`를 파싱하여 적용 가능한 가이드라인 찾기
2. `git diff`에서 변경 파일 가져오기
3. 파일을 관련 가이드라인에 매핑
4. 준수 검사 실행 및 보고서 출력

### 4.5 코드 리뷰 후

리뷰에서 AI가 생성한 코드를 수정했을 때 새 규칙을 추출합니다:

```text
/ai-dev-os-extract [file-path]
```

수행 내용:

1. 차이를 분석하여 *왜* 변경했는지 식별
2. `MUST` / `MUST NOT` 형식으로 규칙 후보 생성
3. 대상 가이드라인 파일과 L2 원칙 링크 제안
4. 승인 후 가이드라인에 규칙 추가

### 4.6 규칙 이해

팀 구성원이 규칙에 의문이 있을 때:

```text
/ai-dev-os-why [rule-or-guideline]
```

예시:

```text
/ai-dev-os-why "왜 any 타입이 금지인가요?"
```

---

## 5. 정기 유지보수

### 월간: 건전성 감사

```text
/ai-dev-os-audit
```

검사 항목:

- 의존성 규칙 준수 (L1에 도구 특정 용어 없음, L2에 프레임워크 세부사항 없음)
- 신선도 (L1: 5년, L2: 3년, L3: 12개월, L4: 4개월)
- 추적 가능성 (L3→L2 링크, 고립된 규칙)
- 커버리지 (모든 파일 패턴에 가이드라인 존재)
- 일관성 (모순되는 규칙 없음)

### 분기별: SECI 나선 진화

```text
/ai-dev-os-evolve
```

최근 커밋과 리뷰 패턴을 분석하여 L1 철학과 L2 원칙의 업데이트를 제안합니다. 이것은 "지식 나선"으로, 일상적인 실천에서 암묵지를 형식지로 변환하는 과정입니다.

### 주간/월간: 준수 보고서

```text
/ai-dev-os-report 1w
/ai-dev-os-report 1m
```

팀 미팅이나 이해관계자 업데이트에 적합한 요약 보고서를 생성합니다.

---

## 6. 팀 온보딩

### 신규 구성원용

1. `ai-dev-os/01_philosophy/core-values.md` 읽기 (5분)
2. `ai-dev-os/02_decision-criteria/` 훑어보기 (10분)
3. 불분명한 규칙에 대해 `/ai-dev-os-why` 실행
4. 코딩 시작 — 훅이 자동으로 준수를 안내

### AI Dev OS를 도입하는 팀용

1. 프로젝트에서 `/ai-dev-os-init` 실행
2. **starter** 템플릿(L3만)으로 시작
3. 코드 리뷰마다 `/ai-dev-os-extract` 사용
4. 2-4주 후 `/ai-dev-os-audit`으로 갭 식별
5. `/ai-dev-os-evolve`로 L1-L2를 점진적으로 구축

---

## 7. 언어 정책

플러그인의 모든 소스 파일(skills, agents, hooks, templates)은 **영어**로 관리됩니다. 이는 LLM의 최적 정확도와 폭넓은 접근성을 보장하기 위함입니다. 번역 문서는 `docs/i18n/` 아래에 참고용으로 제공되지만, 영어가 유일한 정보 원천(Single Source of Truth)입니다.

---

## 8. CI 통합

훅은 개발 중 실시간 조언을 제공하지만 AI 코딩 도구 내에서만 작동합니다. 팀 전체의 컴플라이언스를 강제하려면 CI 파이프라인에 AI Dev OS 검사를 통합하세요.

### 강제 수준

| 수준 | 동작 | 권장 대상 |
|------|------|---------|
| **권고** | 훅이 개발 중 경고, CI가 위반을 보고하지만 차단하지 않음 | 개인 개발자, 소규모 팀 |
| **경고** | CI가 PR에 위반 코멘트를 게시하지만 병합 허용 | AI Dev OS를 단계적으로 도입 중인 팀 |
| **차단** | CI가 중대한 위반(보안, 인증)에서 실패 | 성숙한 규칙을 보유한 확립된 팀 |

권고 수준에서 시작하여 규칙이 성숙함에 따라 단계적으로 올리세요(`[draft]` → `[proven]`).

---

## 9. 문제 해결

### 훅이 트리거되지 않음

- 훅이 `.claude/settings.json`에 있는지 확인 (`hooks/hooks.json`을 직접 읽는 것이 아님)
- `jq`가 설치되어 있는지 확인 (`jq --version`)
- Windows에서 bash가 PATH에 포함되어 있는지 확인

### 스킬을 찾을 수 없음

- 스킬 파일이 `.claude/skills/` 디렉토리에 있는지 확인
- 각 스킬에 유효한 YAML frontmatter가 있는 `SKILL.md` 파일이 필요
- `ls .claude/skills/`로 확인

### 오탐 위반

- 검사가 너무 엄격한 경우 `03_guidelines/`의 해당 가이드라인 편집
- 삭제 전 `/ai-dev-os-why`로 규칙의 근거 확인
- 완전히 삭제하기보다 규칙 완화 고려

### 성능

- 훅 검사는 매 Write/Edit마다 실행 — 너무 느리면 가이드라인 범위 축소
- 무거운 작업에는 `context: fork` 스킬(extract, audit, evolve) 사용
- 서브 에이전트는 격리된 컨텍스트에서 실행되어 메인 세션을 오염시키지 않음

---

Languages: [English](../../operation-guide.md) | [日本語](../ja/operation-guide.md) | [简体中文](../zh-CN/operation-guide.md) | 한국어 | [Español](../es/operation-guide.md)
