---
name: mcp-update
description: 프로젝트 .mcp.json 에 채워 넣은 MCP 서버를 .claude/settings.json 의 enabledMcpjsonServers 에 등록한다. 사용자가 /mcp-update 로 직접 호출할 때만 쓴다.
disable-model-invocation: true
---

# /mcp-update

`.mcp.json` 에 작성된 서버를 프로젝트 `.claude/settings.json` 의 `enabledMcpjsonServers` 에 등록한다. 보통 `/mcp-init` 으로 만든 예시를 실제 값으로 채운 뒤에 쓴다.

먼저 이 skill 폴더 기준 `../../shared/common-rules.md` 를 읽고 그 규칙을 따른다.

<steps>
1. `.mcp.json` 읽기
   - 서버 이름과 실행 방식(`command` 또는 `url`)만 정리한다.
   - 인자, env 값, 접속 문자열은 출력하지 않는다. 비밀번호나 토큰이 들어 있을 수 있다.
   - 파일이 없으면 `/mcp-init` 을 먼저 하라고 안내하고 멈춘다.

2. 미작성 서버 가려내기
   - 값에 `<...>` 형태의 자리표시자가 남아 있는 서버는 등록 대상에서 뺀다.
   - 어떤 서버의 어느 항목이 비어 있는지 항목 이름만 알려 준다. 값은 보여 주지 않는다.
   - 환경변수 참조(`${...}`)는 채워진 것으로 본다. 환경변수 설정은 사용자가 한다.

3. 등록 상태 비교
   - 프로젝트 `.claude/settings.json` 의 `enabledMcpjsonServers` 와 `enableAllProjectMcpServers` 를 읽는다.
   - 추가 대상: 채워진 서버 중 목록에 없는 것
   - 삭제 후보: 목록에는 있는데 `.mcp.json` 에 없는 이름 (삭제는 사용자가 정한다)
   - `enableAllProjectMcpServers` 가 `true` 이면 목록 없이도 모두 켜진다는 사실을 알린다.

4. 반영안 보여 주기
   - `enabledMcpjsonServers` 에 추가할 이름만 diff 로 보여 준다.
   - 기존 키(`env`, `enabledPlugins`, `hooks` 등)와 이미 등록된 이름은 그대로 둔다.
   - 파일이나 키가 없으면 새로 만들 내용 전체를 보여 준다.

5. 확인 후 반영하고 마무리
   - 사용자가 확인한 뒤에만 반영한다.
   - 세션을 다시 시작해야 반영된다고 알린다.
   - 서버가 실제로 연결되는지는 사용자가 세션에서 확인한다. DB 접속 문제는 조언만 한다.
</steps>
