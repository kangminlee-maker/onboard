# Project Onboard

Set up or update Claude Code project settings for the current repository.

## Mode Detection

Check if `CLAUDE.md` exists in the project root.
- **If NOT found** → Run "Initial Setup" (Phase 1 → Phase 2 → Phase 3)
- **If found** → Run "Update" (Phase 2 → Phase 3)

---

## Phase 1: Gather User Input (Initial Setup Only)

Ask the user the following questions using AskUserQuestion. Do NOT skip this phase. Do NOT guess answers.

### Process Guidelines
- Even when only one option exists, present it and confirm with the user before applying.
- Show all auto-detected information transparently. Do not hide or filter scan results.
- Present risks alongside each option at decision time, not in a separate section after decisions.
- For pure technical decisions: present pros/cons with a recommendation, then let the user decide.

### Required Questions

1. **Project overview**: "What does this project do? (1-2 sentences)"
2. **Terminology**: "Are there terms in this project that are easily confused or have specific meanings? List them with correct vs incorrect usage. (Skip if none)"
3. **Abbreviations**: "What abbreviations does this project use? e.g., PRD, QA, API (Skip if none)"
4. **Custom prohibitions**: "Any project-specific rules Claude must follow beyond the defaults? (Skip if none)"
5. **LLM-native project**: "Does this project use LLM as a core value generator? (e.g., LLM interprets meaning, generates content, makes semantic decisions) (yes/no)"

Store all answers for use in Phase 3.

---

## Phase 2: Auto-Detect from Codebase

Scan the project and collect the following. If the repo is empty (no source files), mark each as `EMPTY` and move on.

### 2.1 Programming Language(s)
- Check file extensions (`.ts`, `.py`, `.go`, `.rs`, `.java`, etc.)
- Check `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `pom.xml`
- Result: list of programming languages, primary programming language first

### 2.2 Framework(s)
- Check dependencies in package.json, requirements.txt, pyproject.toml, etc.
- Look for: React, Next.js, Vue, Svelte, Django, FastAPI, Flask, Express, NestJS, Spring, etc.
- Result: list of frameworks

### 2.3 Package Manager
- `package-lock.json` → npm
- `yarn.lock` → yarn
- `pnpm-lock.yaml` → pnpm
- `bun.lockb` → bun
- `requirements.txt` / `pyproject.toml` → pip / poetry
- `go.sum` → go modules
- Result: package manager name

### 2.4 Verification Commands
- From `package.json` scripts: look for `test`, `lint`, `typecheck`, `check`, `build`
- From `Makefile`: look for `test`, `lint`, `check` targets
- From `pyproject.toml`: tool configs (pytest, ruff, mypy)
- Result: exact commands to run for verify loop (e.g., `npm run lint && npm run test`)
- If empty repo: use placeholder `{{add verify commands}}`

### 2.5 Directory Structure
- Run a top-level directory listing
- Identify: source dirs (`src/`, `app/`, `lib/`), test dirs (`tests/`, `__tests__/`), config files
- Result: brief structure summary

### 2.6 File Naming Convention
- Analyze existing filenames in source directories
- Detect: `kebab-case`, `camelCase`, `PascalCase`, `snake_case`
- Result: detected convention for files and directories

### 2.7 Default Branch
- Run `git branch --show-current` or check `git remote show origin`
- Result: branch name (default: `main`)

### 2.8 Global Parallel Work Protocol
- Check if `~/.claude/CLAUDE.md` exists and contains a `## Parallel Work Protocol` section
- If found: note that the global protocol is active (Agent Teams 기반). CLAUDE.md 템플릿에서 글로벌 설정과 동기화 확인.
- If NOT found: flag as missing — recommend adding to `~/.claude/CLAUDE.md` (Agent Teams 기반 프로토콜이 프로젝트 CLAUDE.md에는 포함되지만, 글로벌 적용을 위해 추가 권장)
- Result: `present` or `missing` (with recommendation)

### 2.9 Code Style Summary
- For the detected primary programming language, derive specific conventions:
  - **TypeScript**: type vs interface, enum policy, assertion policy, function style
  - **Python**: type hints policy, docstring style, import style
  - **Go**: error handling pattern, naming conventions
  - **Other**: derive equivalent conventions from the generic rules below

### 2.10 LLM Usage Detection
- Check dependencies for LLM-related packages: `anthropic`, `openai`, `@ai-sdk/*`, `langchain`, `llamaindex`, `cohere`, `mistralai`, `ollama`, `replicate`, `together`
- Check for prompt-related files/directories: `prompts/`, `prompt-templates/`, `*.prompt`, `*.prompt.md`
- Check for LLM config patterns: model name references, API key env vars (`OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, etc.)
- Result: `detected` or `not detected`
- Cross-reference with user answer from Phase 1 question 5. User confirmation takes priority.

---

## Phase 3: Generate Files

### Section Marker Convention

Use HTML comment markers to separate auto-detected content from user-provided content:
```
<!-- auto:section-name -->
(auto-generated content here)
<!-- /auto:section-name -->
```

On **Update** mode: only overwrite content between `<!-- auto:* -->` markers. Preserve everything else.

---

### File 1: `CLAUDE.md` (project root)

```markdown
# CLAUDE.md — {project name from user or directory name}

## Project Overview

{user's answer to project overview question}

## User Communication

사용자는 제품/도메인 전문가이며 개발 비전문가. 빠른 결정, 철저한 검증. 간결함 선호.

### 소통 규칙
- 기술 용어 유지 + 설명 첨부. 쉬운 말로 대체 금지.
- 학술적/현학적 용어 지양. 쉬운 말로 같은 정확성 달성.
- 비유/은유 금지. 직접 설명.
- 리스크는 선택 시점에 제시. 결정 후 추가 리스크 금지.
- 선택지가 하나뿐이어도 사용자에게 확인. 자동 적용 금지.
- 순수 기술 결정: Builder 소관이지만, 장단점 + 추천 안내.
- 시스템이 아는 모든 정보 제공. 정보와 결정을 구분.
- 이름은 역할 기준. 실제로 일어나는 일을 반영.
- 의미의 정확성 > 편의성. 기존 용어 의미 확장 금지, 정확한 새 개념 추가.
- 복잡도 = 통제 가능성. 크기가 아님.
- 문서를 위한 문서 금지. 기존 문서에 간결 포함.
- 전역 규칙은 model-independent 위치(README.md 등)에 배치.
- 행위 주체는 설계 원칙에서 도출. 원칙에서 논리적으로 추론.

### 작업 방식
- 주요 결정마다 병렬 에이전트 리뷰 (6인).
- 2라운드 구조: Round 1 독립 리뷰 → 종합 → Round 2 재응답.
- 리뷰 기준: 목적·철학·persona 정합성 우선. 단순 오류 검출보다 상위.
- 전문가 만장일치도 근거 약하면 재검토. 합의 자체가 아니라 합의의 논리를 평가.
- 결정은 빠르게, 검증은 철저하게.
- 커밋은 의미 있는 작업 단위로.
- 상위설계: 레거시 무관. 세부구현: 레거시 참고 (실패 반복 방지).
- 좋은 이름이 떠오르지 않으면 기존 이름 유지.

### Anti-Patterns (X → O)
| 하지 말 것 | 해야 할 것 |
|-----------|-----------|
| 기술 용어를 쉬운 말로 대체 | 용어 유지 + 설명 첨부 |
| 학술적 용어로 정확성 추구 | 쉬운 말로 같은 정확성 달성 |
| 별도 문서로 원칙을 장황하게 서술 | 기존 문서에 간결 포함 |
| model-dependent 위치에 전역 규칙 배치 | model-independent 위치에 배치 |
| 리스크를 결정 후 별도 섹션에 제시 | 선택 시점에 각 선택지 리스크 제시 |
| 전문가 만장일치를 무비판적 수용 | 합의 근거를 별도 검증 |
| "파급범위가 크다"로 차선책 선택 | "통제 가능한 복잡도인가" 기준 |
| 편의를 위해 기존 용어 의미 확장 | 의미가 정확한 새 개념 추가 |

## Tech Stack

<!-- auto:tech-stack -->
- Programming Language: {detected}
- Framework: {detected}
- Package Manager: {detected}
<!-- /auto:tech-stack -->

## Verification Loop

<!-- auto:verify -->
After every change: {detected verify commands}.
Before PR: {detected full verify command}.
<!-- /auto:verify -->

## Code Style

Follow `@.claude/rules/coding-conventions.md` for all code.

## Project Patterns

Follow `@.claude/rules/project-patterns.md` for file naming, conventions, and terminology.

## Plan Mode — Design Protocol

Every non-trivial task starts in plan mode. Complete all 4 steps before switching to auto-accept.

**Step 1 — Scope Lock**
- In scope: concrete outcomes this change delivers
- Out of scope: anything else (tag Phase 2 if worth revisiting)
- Affected surface: which existing files/modules are touched
- Never expand scope mid-design

**Step 2 — Contracts First**
Define before writing any implementation:
- Input/output types: exact signatures for every public function/endpoint
- State transitions: all states, triggers, and illegal transitions
- Error cases: every failure mode with its type
- Invariants: conditions that must always hold

**Step 3 — Pre-mortem**
Answer explicitly before finalizing:
1. "If this fails in production, what breaks first?"
2. "What system state does this design not handle?"
3. "What assumption about existing code might be wrong?"
→ Any gap found → revise Step 2 contracts first

**Step 4 — Simplicity Gate**
- Remove any abstraction layer that isn't required for correctness
- Every new file/type/function must trace to a Step 1 requirement
- Plan must be understandable in <5 minutes by a new reader

**Transition**: If Claude can't 1-shot the implementation from this plan, the plan isn't done. Return to plan mode.

## Parallel Work

<!-- auto:parallel-work -->
### 기본: Agent Teams 기반 프로토콜

병렬 수정 작업 시 Agent Teams를 사용한다.

1. **사전 파일 충돌 분석**: 작업 분배 전, 각 에이전트가 수정할 파일 목록을 식별하고 겹치는 파일을 찾는다.
2. **독립 파일**: 병렬 실행 (TaskCreate, 동시 시작)
3. **공유 파일**: 순차 실행 (TaskCreate with blockedBy). "큰 작업 먼저" — 작업량이 큰 에이전트가 공유 파일을 먼저 수정.
4. **Agent Teams 사용**: TeamCreate → TaskCreate(blockedBy) → 에이전트 spawn → TaskList로 작업 확인 → TaskUpdate로 완료 보고

### Fallback: Worktree 격리

Agent Teams를 사용할 수 없는 환경에서는 git worktree로 물리적 격리:
- One agent per file. `git worktree add .claude/worktrees/<n> origin/main`
- 작업 완료 후 수동 merge 필요
<!-- /auto:parallel-work -->

## Ontology as Code

개념-계약-구현 사슬이 끊기지 않아야 한다:

```
concept → contract → artifact seat → type/interface → field name → variable name → filesystem path
```

- 하나의 개념에는 하나의 canonical label만 쓴다
- 같은 개념을 상황마다 다른 이름으로 부르지 않는다
- authority 순서: concept definition → contract → implementation. prose가 code를 이기는 구조가 아니라, 개념이 구현을 이끄는 구조.
- 기본 posture는 fail-close: semantic quality가 증명되지 않았으면 자동으로 truth로 승격하지 않는다

상세 규칙은 `@.claude/rules/ontology-as-code.md` 참조.

{if LLM-native project}
## LLM-Native Development

<!-- auto:llm-native -->
이 프로젝트는 LLM을 핵심 가치 생성 엔진으로 사용한다.

- **LLM**: semantic engine — 의미 해석, 판단, 생성
- **Runtime**: control layer — bounded I/O, state transition, safety gate
- **Script**: efficiency layer — 품질 무관 결정론적 반복 작업

기본 방향: LLM이 가치를 만들고, runtime이 안전하게 감싸고, script가 낭비를 제거한다.

### 의사결정 프레임
1. 의미 판단이 필요한가 → LLM core
2. 경계를 강하게 제어해야 하는가 → runtime gate
3. 품질 저하 없이 완전히 고정 가능한가 → script automation

상세 규칙은 `@.claude/rules/llm-native-development.md` 참조.
<!-- /auto:llm-native -->
{/if}

## Available Commands & Agents

### Individual Expert (`/ask-{agent}`)
단일 전문가 관점 상담. 각 에이전트가 자신의 전문 영역에서 답변.

| Command | Role |
|---------|------|
| `/ask-philosopher` | 시스템 목적·철학 정합성 |
| `/ask-ux_expert` | 사용자 경험 |
| `/ask-systems_architect` | 시스템 아키텍처 |
| `/ask-product_engineer` | 제품 엔지니어링 |
| `/ask-reliability_engineer` | 신뢰성 엔지니어링 |
| `/ask-integration_engineer` | 통합·연동 |

### Team Review (`/ask-review`)
6인 에이전트 패널 리뷰. 5인 독립 리뷰 → Philosopher 종합 → 5인 재응답 → 최종 합의.

### Multi-Agent Analysis (Prism)
| Command | Purpose |
|---------|---------|
| `/prism:plan` | 다각도 분석 → Devil's Advocate 검증 → 3인 위원회 토론 → 실행 계획 |
| `/prism:prd` | PRD를 정책 문서 대비 충돌·모호성 분석 |
| `/prism:incident` | 인시던트 포스트모템. 동적 관점 → DA 검증 → Tribunal |

## Prohibitions

- No skipped error handling
- No commits without tests
- No breaking API changes without discussion
- No scope expansion beyond Step 1 lock
- No implementation with unresolved pre-mortem gaps
- No abstractions without concrete in-scope justification
{append user's custom prohibitions here, if any}

## Self-Improvement

After every correction → update this file with a prevention rule.
```

---

### File 2: `.claude/rules/coding-conventions.md`

```markdown
# Coding Conventions

Self-contained, explicit, linearly readable code.

## File Structure
- One concern = one file. Co-locate types, constants, logic.
- Acceptable duplication over fragile shared abstraction.
- Export only what's consumed externally.
- Soft limit: 300 lines. Split by concern, not category.

## Naming
Names eliminate the need for comments:
- Booleans: `is/has/should/can`
- Transforms: `to/from/parse`
- Validation: `validate/assert`
- Events: `handle/on`
- Factories: `create/build/make`
- Fetching: `fetch/load/get`
- Constants: `UPPER_SNAKE_CASE`
- Avoid: single-letter vars, `data`/`info`/`temp`/`obj`, abbreviations

## Functions
- Pure by default: inputs → outputs, no side effects.
- Separate computation (pure) from coordination (I/O).
- Max 40 lines, max 3 params (options object beyond that).
- Early returns over nested if/else.
- No nested ternaries.

## Errors
- Fail fast: validate at boundary, not deep inside.
- Never empty catch. Include context: what failed, which inputs, why.

## Control Flow
- Linear top-to-bottom. No implicit event chain ordering.
- Explicit parallel vs sequential async.

## Config as Data
- Business rules as data structures, not procedural branches.
- New option = data change, not logic change.

## Module Boundaries
```
[External] → [Boundary: parse/validate] → [Pure Logic] → [Boundary: persist/send]
```
Business logic never imports I/O directly. Pass deps as params.

## Comments & Tests
- Comments: WHY only. Workarounds: link to issue. Delete commented-out code.
- Tests: co-locate. Behavior names. AAA pattern. Mock I/O only.

<!-- auto:programming-language-rules -->
{Generate programming-language-specific rules here based on the detected primary programming language.

For TypeScript:
- Explicit types on all function params and returns.
- States → discriminated unions, not boolean flags.
- Options → string literal unions, not enums.
- `unknown` + type guards, not `any`. No `as` assertions.
- Max 2 levels of generic nesting. Extract intermediate types.
- `type` over `interface`. Never `enum`.
- `function` keyword for top-level. Arrow for inline only.
- Result/Either for business logic. try/catch only at I/O boundaries.
- No shared barrel type files. No default exports.

For Python:
- Type hints on all function params and returns.
- Use dataclasses or Pydantic models for structured data.
- Use `Enum` for fixed option sets.
- Prefer `pathlib` over `os.path`.
- Use `raise` with specific exception types, not bare `raise`.
- Docstrings: Google style.
- Imports: standard lib → third-party → local, separated by blank lines.

For Go:
- Always check returned errors. No `_` for error values.
- Use named return values only when they improve readability.
- Interfaces: accept interfaces, return structs.
- Prefer table-driven tests.
- Use `context.Context` as first param for functions with I/O.

For other programming languages:
Derive equivalent conventions from the generic rules above, adapted to the programming language's idioms and best practices.}
<!-- /auto:programming-language-rules -->

## Do NOT
- Nested ternaries
- Empty catch blocks
- Mixed I/O and business logic
- Commented-out code
- Magic numbers
- Mutate function params
```

---

### File 3: `.claude/rules/project-patterns.md`

```markdown
# Project Patterns — {project name}

## File & Directory Naming

<!-- auto:naming -->
- Directories: {detected convention, e.g., `kebab-case/`}
- Files: {detected convention}
<!-- /auto:naming -->

## Language Protocol
- All files on `{default branch}`: English.
- Config keys, enum values, file paths: always English.
- User-facing string values: follow locale setting.
- Comments in code: English.

## Terminology Boundaries

{user's terminology answer, or "No project-specific terminology defined yet." if skipped}

| Term | Usage | NOT |
|------|-------|-----|
{rows from user input}

## Abbreviation Registry

{user's abbreviation answer, or "`N/A`" if skipped}
No other abbreviations without adding to this registry.

## Branch Rule

<!-- auto:branch -->
`{detected default branch}` = always deployable. All work on feature branches.
<!-- /auto:branch -->
```

---

### File 4: `.claude/rules/ontology-as-code.md` (always generated)

```markdown
# Ontology as Code

개념-계약-구현이 일관되게 연결된 상태를 유지한다.

## 1. Canonical Mapping Rule

아래 사슬이 끊기지 않아야 한다:

```
concept → contract → artifact seat → type/interface → field name → variable name → filesystem path
```

이 연결 중 하나라도 끊어지면 Ontology as Code 적합성이 떨어진다.

## 2. Same Concept, Same Label

- 하나의 개념에는 하나의 canonical label만 쓴다
- 같은 개념을 상황마다 다른 이름으로 부르지 않는다
- 하나의 label은 하나의 conceptual axis에서만 쓴다

## 3. Ownership Rule

### LLM owns meaning
- semantic interpretation, judgment
- relevance 판단, evidence sufficiency 판단
- disagreement / uncertainty 보존

### Runtime owns deterministic contract execution
runtime이 맡을 수 있는 일은 아래를 모두 만족해야 한다:
1. 입력이 명시적이다
2. 규칙이 사전에 고정되어 있다
3. hidden interpretation이 없다
4. 같은 입력이면 같은 출력이 나온다

실무 shortcut: script로 안전하게 자동화할 수 없는 일은 LLM 소유다.

### Runtime must not reason
runtime은 의미를 대신 판단하는 것이 아니라, **계약 미달 output을 통과시키지 않는 것**이다.
- prompt를 semantic하게 보정하지 않는다
- relevance를 새로 판단하지 않는다
- 부족한 reasoning을 추론으로 보완하지 않는다
- LLM output을 해석해서 살리지 않는다

## 4. Interface Rule

### Artifact-first interface
LLM/runtime interface는 자유 텍스트 conversation이 아니라 artifact seat 중심으로 설계한다.
handoff는 아래를 포함해야 한다:
1. input artifact refs
2. optional embedded primary content
3. output artifact seat
4. bounded execution directives

### Two-layer input model
LLM 입력은 두 층으로 나뉜다:
1. **선언형 handoff 입력** — runtime이 고정
2. **자율 탐색 입력** — LLM이 판단

### Boundary is first-class
경계를 다룰 때 최소 아래를 분리한다:
- 경계 정책 (BoundaryPolicy)
- 경계 제시 (BoundaryPresentation)
- 경계 강제 프로필 (BoundaryEnforcementProfile)
- 실효 경계 상태 (EffectiveBoundaryState)

규칙:
- declared policy와 실제 enforcement를 혼동하지 않는다
- 여러 제약이 충돌하면 가장 강한 deny가 승리한다
- 경계 때문에 필요한 근거를 확보하지 못하면 타협하지 않고 fail-close 한다

## 5. Execution Rule

- **Prompt path는 reference realization**: 같은 contract, 같은 execution order, 같은 output shape, 같은 artifact truth
- **한 번에 한 경계씩 치환**: prompt-backed path로 먼저 작동 → 결과 확인 → 한 책임만 runtime으로 치환
- **Always keep working**: 매 단계에서 시스템이 작동 가능한 상태 유지. prompt 영역과 구현 영역 사이 interface가 항상 유효해야 한다.

## 6. Context Isolation Rule

LLM 실행 단위는 필요할 때 맥락 격리 추론 단위(ContextIsolatedReasoningUnit)로 분리한다.
필요 속성:
1. 메인 콘텍스트와 상태를 공유하지 않는다
2. 계약된 입력만 받는다
3. 계약된 출력만 낸다
4. 독립적으로 의미 판단을 수행한다

## 7. Authority Rule

권장 순서:
1. concept definition (lexicon)
2. charter / methodology / interface 원칙 문서
3. 개별 contract 문서
4. type/interface
5. runtime writer/reader
6. legacy prototype prose

prose가 code를 이기는 구조가 아니라, **concept → contract → implementation** 순서가 유지되어야 한다.

## 8. Fail-Close Rule

- 호출이 되었다는 것과 semantic result가 usable하다는 것은 다르다
- invocation usability와 semantic usefulness를 분리한다
- semantic quality가 아직 증명되지 않았으면 자동으로 truth로 승격하지 않는다
- 기본 posture는 fail-close

## 9. Checklist

새 기능을 만들기 전에 점검:
1. 이 기능의 canonical concept는 무엇인가
2. 그 concept는 정의되어 있는가
3. 입력/출력 contract가 문서화되었는가
4. artifact seat가 정해졌는가
5. LLM 소유와 runtime 소유가 정확히 분리되었는가
6. runtime이 semantic reasoning을 먹고 있지 않은가
7. prompt-backed reference path가 먼저 작동하는가
8. implementation replacement가 같은 artifact truth를 유지하는가
9. fail-close posture가 정의되어 있는가
10. 이름이 canonical terminology와 일치하는가
```

---

### File 5: `.claude/rules/llm-native-development.md` (LLM-native projects only)

Generate this file only if the project is identified as LLM-native (user confirmed in Phase 1 question 5, or LLM dependencies detected in Phase 2 section 2.10).

```markdown
# LLM-Native Development

이 프로젝트는 LLM을 핵심 가치 생성 엔진으로 사용한다. 아래 원칙을 따른다.

## 1. 역할 분리

### LLM — Semantic Engine
- 애매한 입력 해석, 불완전한 문제 정의 보완
- 요약, 분류, 패턴 추출, canonical candidate 생성
- semantic interpretation, judgment, relevance 판단
- uncertainty 보존 — 확신이 낮으면 낮다고 말한다

### Runtime — Control Layer
- bounded input/output, state transition, safety gate
- idempotency, retry, timeout, fallback
- auth, permission, audit, observability
- redaction, retention, purge

### Script — Efficiency Layer
- 품질 무관 결정론적 반복 작업
- deterministic 변환, 검증, 동기화
- batch 작업, 운영 자동화

**경계 원칙**: script로 안전하게 자동화할 수 없는 일은 LLM 소유다.

## 2. 의사결정 프레임

기능을 설계할 때 아래 세 질문을 순서대로 본다.

### 질문 1: 의미 판단이 필요한가?
입력이 애매하거나, 정답이 하나가 아니거나, 맥락에 따라 결과가 달라지면 → **LLM core**

### 질문 2: 경계를 강하게 제어해야 하는가?
입출력 범위 제한, 상태 전이 안전, 비용/권한/감사 관리가 필요하면 → **runtime gate**

### 질문 3: 품질 저하 없이 완전히 고정 가능한가?
입력 조건 명확 + 결과 deterministic + 의미 품질 영향 없음 → **script automation**

## 3. 개발 순서

1. 사용자 가치와 성공 정의를 자연어로 쓴다
2. 작업을 LLM core / runtime gate / script candidate로 분해한다
3. 좋은 출력과 나쁜 출력 예시를 모은다
4. quality rubric과 eval set을 만든다
5. prompt, context assembly, retrieval, tool use 전략을 실험한다
6. baseline보다 나은 변형을 고른다
7. runtime hardening
8. deterministic script 후보를 자동화한다
9. production에서 sampled review와 promote workflow 유지

**원칙**: behavior-first, eval-first, automation-last.
단, 명백한 deterministic subtask는 예외 — 초기에 script로 빼는 편이 맞다.

## 4. 성공 기준

- **usefulness**: 사용자가 다시 쓰고 싶은 결과인가
- **faithfulness**: 입력과 근거를 벗어나지 않았는가
- **specificity**: 구체적이고 적용 가능한가
- **actionability**: 다음 행동으로 이어지는가
- **robustness**: noisy/partial/ambiguous input에서도 깨지지 않는가
- **calibration**: 확신이 낮을 때 보류하거나 검토 요청하는가

exact-match 재현성은 우선 기준이 아니다.

## 5. 테스트 전략

### Deterministic Test (runtime + script)
parser/unit, schema validation, auth, retry/fallback, state transition, audit, script pre/postcondition

### Semantic Eval (LLM)
eval set, rubric scoring, pairwise comparison, adversarial input, abstain behavior, human preference

### Production Review
sampled output review, failure taxonomy, promoted artifact review, regression tracking

## 6. Ontology와 Schema의 역할

ontology가 강제해야 하는 것:
- 어떤 입력을 받고 어떤 결과를 남기는가
- 어떤 상태 전이와 review workflow가 있는가
- 어떤 경계와 책임이 있는가
- 어떤 검증과 traceability가 필요한가

ontology가 너무 일찍 고정하면 안 되는 것:
- 모델의 표현 방식 세부
- 결과물의 문장 구조
- 의미 판단을 지나치게 작은 enum으로 축소하는 것
- open-ended task에 단일 정답을 가정하는 것

ontology는 semantic freedom을 죽이는 틀이 아니라, semantic work를 안전하게 운영하는 틀이어야 한다.

## 7. Anti-Patterns

- semantic task를 enum mapping으로 축소
- output 자유도를 줄여 무난하지만 쓸모없는 결과
- deterministic 작업을 계속 LLM에 맡기기
- script 조건이 명확한데 범용 agent로 덮기
- eval 없이 runtime hardening
- exact-match만으로 품질 증명
- uncertainty/abstain을 실패로 간주
- prompt/context/retrieval 실험 없이 schema만 정교하게
- 형식 안정성만 개선하고 semantic quality regression을 보지 않기
- 짧고 안전하지만 의미 없는 출력으로 수렴

## 8. LLM 협업자 기본 지시문

다른 LLM이나 Codex류 협업자에게 아래를 명시한다:

> 이 작업의 목적은 LLM을 더 나은 방식으로 사용하는 것이다.
> LLM이 담당하는 부분은 deterministic correctness보다 semantic usefulness가 더 중요하다.
> runtime은 bounded input, bounded output, state transition, safety를 관리하는 gate로 설계하라.
> 결과물의 질에 영향을 주지 않는 반복 작업은 먼저 script로 자동화할 수 있는지 검토하라.
> 구현 전에 좋은 출력과 나쁜 출력 예시, quality rubric, eval strategy를 먼저 정의하라.
> 정확히 같은 결과를 강제하기보다, 유용하고 강건한 결과를 안정적으로 내는 방향으로 설계하라.
> 짧고 안전하지만 의미 없는 출력으로 수렴하면 실패로 간주하라.
```

---

## Phase 4: Report

After generating all files, output a summary:

```
## Onboard Complete

| File | Status |
|------|--------|
| CLAUDE.md | Created / Updated |
| .claude/rules/coding-conventions.md | Created / Updated |
| .claude/rules/project-patterns.md | Created / Updated |
| .claude/rules/ontology-as-code.md | Created / Updated |
| .claude/rules/llm-native-development.md | Created / Updated / Skipped (not LLM-native) |

### Auto-Detected
- Programming Language: {value}
- Framework: {value}
- Package Manager: {value}
- Verify: {value}
- Naming: {value}
- Branch: {value}
- Parallel Work Protocol: {present in global / missing — recommendation shown}
- LLM Usage: {detected / not detected}

### From User Input
- Overview: {provided / skipped}
- Terminology: {provided / skipped}
- Abbreviations: {provided / skipped}
- Custom rules: {provided / skipped}
- LLM-native: {yes / no}
```

If in Update mode, also list which `<!-- auto:* -->` sections were changed.
