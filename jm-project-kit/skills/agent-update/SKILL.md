---
name: agent-update
description: 개발하면서 정해진 규칙, 사용자의 교정과 확정한 결정을 프로젝트 agent 에 반영한다. 사용자가 /agent-update 로 직접 호출할 때만 쓴다.
disable-model-invocation: true
argument-hint: "[반영할 규칙 또는 대상 역할 — 생략하면 이번 세션과 memory 에서 찾는다]"
---

# /agent-update

개발하면서 쌓인 규칙을 프로젝트 agent(`.claude/agents/{프로젝트명}-*.md`)에 반영한다.

먼저 이 skill 폴더 기준 `../../shared/common-rules.md` 를 읽고 그 규칙을 따른다.

<steps>
1. 대상 확인
   - `.claude/agents/` 에서 프로젝트 agent 목록과 이름 접두어를 확인한다.
   - 프로젝트 agent 가 하나도 없으면 반영할 곳이 없다. `/project-init` 을 먼저 하라고 안내하고 멈춘다.

2. 반영 후보 모으기 (아래 네 곳, 인자가 있으면 인자만 본다)
   - 인자로 받은 규칙
   - 이번 대화에서 사용자가 정한 규칙, 교정한 내용, 확정한 접근
   - 자동 memory 의 `feedback`, `project` 항목 (시스템 프롬프트의 Memory 섹션에 적힌 경로의 MEMORY.md 에서 찾는다)
   - 새로 설치된 skill: 공통 규칙의 `skill_discovery` 로 찾고 고른 skill 중 agent 의 `<skills>` 블록에 아직 없는 것. skill 은 설치되어 있다는 사실 자체가 근거이므로 사용자가 말하지 않았어도 후보가 된다.
   - 반대로 `<skills>` 블록에 적혀 있는데 더 이상 설치되어 있지 않은 skill 은 삭제 후보로 따로 표시한다.

3. 걸러내기 — 다음은 반영하지 않는다
   - 이미 agent 에 들어 있는 규칙
   - 규칙 원본 문서(CLAUDE.md 의 `rule_sources`)에 이미 있는 규칙. 이 경우 agent 에 복제하지 않고 문서를 따르게 둔다.
   - 한 번만 쓰는 사실, 코드나 git 으로 알 수 있는 내용
   - 사용자가 말하지 않았는데 Claude 가 추론해서 만든 규칙

4. 역할 분류
   - 각 규칙이 어느 역할(백엔드, DB, 프론트엔드, 리뷰) agent 에 속하는지 정한다.
   - 여러 역할에 걸치면 각 agent 에 필요한 만큼만 적고 중복을 최소화한다.
   - 어느 쪽인지 모르겠으면 사용자에게 묻는다.

5. 반영안 보여 주기
   - agent 파일별로 바뀌는 줄만 diff 로 보여 주고, 각 규칙의 출처(대화, memory, 인자)를 함께 적는다.
   - 기존 XML 블록(`coding_rules`, `working_rules` 등) 중 맞는 곳에 넣는다. 맞는 블록이 없으면 새 블록을 제안한다.
   - 기존 규칙과 충돌하면 충돌 사실을 알리고 사용자가 정하게 한다.

6. 확인 후 반영
   - 사용자가 확인한 항목만 Edit 으로 반영한다. 파일을 통째로 다시 쓰지 않는다.
   - memory 항목은 지우거나 고치지 않는다.
   - 끝나면 반영된 규칙 목록을 보여 주고, 새 세션부터 적용된다고 알린다.
</steps>
