# jm-project-kit 공통 규칙

jm-project-kit 의 모든 명령(`/project-init`, `/agent-update`, `/lsp-init`, `/mcp-init`)이 먼저 읽는 규칙이다.

<rule_discovery>
개발 규칙은 프로젝트 CLAUDE.md 가 가리키는 문서가 원본이다. 규칙 문서가 없거나, 있어도 해당 규칙이 빠져 있으면 다음 순서로 찾아서 따른다.

1. 빌드·포맷·린트 설정 (pom.xml, build.gradle, package.json, .editorconfig, jsconfig.json 등)
2. 같은 모듈의 기존 코드에서 일관되게 쓰는 패턴
3. 위에서도 정해지지 않거나 코드끼리 다르면 추측하지 말고 사용자에게 묻는다.

문서와 agent 규칙이 충돌하면 문서를 따른다.
</rule_discovery>

<project_naming>
`{프로젝트명}` 은 작업 폴더(프로젝트 루트 폴더)의 이름에서 따온다. 사용자가 따로 정해 주지 않았을 때 다음 규칙으로 만든다.

1. 폴더 이름을 소문자로 바꾸고, 밑줄·공백은 하이픈(`-`)으로 바꾼다.
2. 앞에 붙은 연도·번호 접두어(예: `2026_08_`)는 뺀다.
3. 예: `2026_08_tidal_dt` → `tidal-dt`, `My Shop_API` → `my-shop-api`
4. 폴더 이름만으로 뜻이 모호하면(예: `app`, `src`, `new`) 만들기 전에 사용자에게 묻는다.

이미 프로젝트 CLAUDE.md 나 `.claude/agents/` 에 정해진 이름이 있으면 그것을 따른다.
</project_naming>

<agent_roles>
agent 는 `{프로젝트명}-{역할}` 이름으로 프로젝트의 `.claude/agents/` 에 둔다. 틀은 이 플러그인의 `templates/agents/{역할}.md` 이다. skill 폴더 기준 `../../templates/agents/{역할}.md` 로 읽는다.

틀은 agent 로 등록되지 않는 복사용 원본이다. 프로젝트 agent 를 만들 때만 읽는다.

| 역할 | 틀 | 쓰는 때 |
| --- | --- | --- |
| 백엔드 | `backend-expert` | Controller, Service, Repository, REST API |
| DB | `db-expert` | 쿼리, 인덱스, 스키마, 영속성 계층 분석 |
| 프론트엔드 | `frontend-expert` | 화면 컴포넌트, 상태, 스타일, 차트 |
| 리뷰 | `fullstack-code-reviewer` | 변경 코드 품질 리뷰, PR 전 |
</agent_roles>

<skill_discovery>
agent 의 `<skills>` 블록은 이 PC 에 실제로 있는 skill 을 보고 채운다. 틀에는 skill 이름을 미리 적어 두지 않는다.

찾는 곳 (이름과 description 을 읽는다)
- 이 세션에서 사용 가능한 skill 목록
- `~/.claude/skills/*/SKILL.md` (전역)
- 프로젝트 `.claude/skills/*/SKILL.md`

고르는 방법
- description 이 역할의 작업(백엔드, DB, 프론트엔드, 리뷰)과 맞는 skill 만 그 역할의 agent 에 넣는다.
- 확인된 프로젝트 스택과 맞지 않으면 넣지 않는다. 예: React 전용 skill 은 React 프로젝트일 때만.
- jm-project-kit 자신의 skill 과, 역할과 무관한 일반 작업 절차 skill(기획, 디버깅 절차, 배포 등)은 넣지 않는다.
- 판단이 애매하면 넣지 않고 사용자에게 묻는다.
- 맞는 skill 이 없으면 `<skills>` 블록을 만들지 않는다.

적는 형식
```
<skills>
해당 작업일 때만 참고한다.
- {용도}: {skill 이름} ({조건이 있으면 적는다})
</skills>
```
</skill_discovery>

<safety>
- 이미 있는 파일(CLAUDE.md, agent, settings, .mcp.json)은 통째로 덮어쓰지 않는다. 바뀌는 항목만 diff 로 보여 주고 사용자 확인 후 반영한다.
- 근거가 없는 항목은 틀의 내용을 그대로 두고, 추측해서 채우지 않는다.
- 사용자가 말하지 않은 작업 원칙은 만들어 넣지 않는다.
- 환경(서버, DB, 도구 설치)은 사용자가 직접 한다. 필요한 명령은 안내만 하고 실행하지 않는다.
- 새로 만들거나 바꾼 agent, 플러그인 설정은 세션을 다시 시작해야 반영된다. 끝날 때 이 사실을 알린다.
</safety>
