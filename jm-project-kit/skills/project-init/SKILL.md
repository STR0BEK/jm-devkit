---
name: project-init
description: 프로젝트 루트에 CLAUDE.md 와 프로젝트 전용 agent 를 처음 만든다. 사용자가 /project-init 으로 직접 호출할 때만 쓴다.
disable-model-invocation: true
argument-hint: "[프로젝트명 — 생략하면 폴더 이름에서 만든다]"
---

# /project-init

프로젝트 루트에 CLAUDE.md 와 `{프로젝트명}-{역할}` agent 를 만든다.

먼저 이 skill 폴더 기준 `../../shared/common-rules.md` 를 읽고 그 규칙을 따른다. 규칙을 찾는 순서, 이름 규칙, 역할별 틀, 안전 규칙이 거기에 있다.

<steps>
1. 프로젝트명 정하기
   - 인자가 있으면 그것을 쓴다. 없으면 공통 규칙의 `project_naming` 으로 폴더 이름에서 만든다.
   - 뜻이 모호하면 만들기 전에 사용자에게 묻는다.

2. 현재 상태 확인
   - 루트 `CLAUDE.md`, `.claude/agents/`, `docs/`, `README*` 가 있는지 본다.
   - CLAUDE.md 나 agent 가 이미 있으면 덮어쓰지 않는다. 5, 6 단계에서 바뀌는 항목만 diff 로 보여 준다.

3. 규칙 원본 찾기
   - docs 폴더와 README 를 실제로 열어 본다. 파일 이름만 보고 짐작해서 적지 않는다.
   - 각 문서가 다루는 범위를 한 줄로 파악한다.

4. 스택 확인
   - pom.xml, build.gradle, package.json 같은 빌드 파일에서 확인한다.
   - 확인되지 않은 것은 적지 않는다.

5. CLAUDE.md 작성 (프로젝트 루트)
   - 아래 `claude_md_layout` 구성을 쓴다. 내용이 없는 블록은 만들지 않는다.
   - 사용자가 말하지 않은 작업 원칙은 만들어 넣지 않는다.

6. agent 작성 (`.claude/agents/{프로젝트명}-{역할}.md`)
   - 어떤 역할이 필요한지 사용자에게 확인한다. 해당 없는 역할은 만들지 않는다.
   - 플러그인 틀 `../../templates/agents/{역할}.md` (이 skill 폴더 기준)를 읽고, 3, 4 단계에서 확인한 사실만 반영한다.
   - 근거가 없는 항목은 틀의 내용을 그대로 둔다.
   - 틀의 XML 태그 구조(`role`, `core_rules` 등)와 frontmatter(`name`, `description`, `model`, `color`, `memory`)를 유지한다.
   - `<skills>` 블록은 틀에 없다. 공통 규칙의 `skill_discovery` 로 이 PC 에 있는 skill 을 찾아 역할에 맞는 것만 넣는다.
   - frontmatter 의 `name` 과 `description` 에 있는 `{...}` 자리표시자를 채운다. description 에는 프로젝트 이름, 4 단계에서 확인한 스택 한 줄, 쓰는 때를 적는다. 스택이 확인되지 않았으면 그 자리는 적지 않고 비운다.

7. 마무리
   - 만든 파일 목록을 보여 준다.
   - 새 agent 는 세션을 다시 시작해야 목록에 나타난다고 알린다.
   - 다음 단계로 `/lsp-init`, `/mcp-init` 을 안내한다. 자동으로 실행하지 않는다.
</steps>

<claude_md_layout>
- 제목: `# {프로젝트명} 프로젝트 지침`. 제목 아래에 공통 절차를 가리키는 문장은 쓰지 않는다. 팀원에게 이 플러그인이 없을 수 있어 CLAUDE.md 가 그 자체로 완결되어야 한다.
- `<rule_sources>`: 규칙의 원본 문서 경로와 각 문서가 다루는 범위
- `<project_agents>`: 이 프로젝트의 `{프로젝트명}-{역할}` agent 대응표 (agent 를 만든 뒤에만)
- `<working_principles>`: 사용자가 이 프로젝트에서 따로 정한 작업 원칙
- 스택이 빌드 파일에서 확인되면 한 줄로 요약해 적을 수 있다.
</claude_md_layout>
