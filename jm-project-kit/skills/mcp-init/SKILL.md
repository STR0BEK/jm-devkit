---
name: mcp-init
description: 프로젝트 루트에 DB MCP 서버 예시가 들어 있는 .mcp.json 을 만든다. 사용자가 /mcp-init 으로 직접 호출할 때만 쓴다.
disable-model-invocation: true
argument-hint: "[DB 종류 — 생략하면 빌드 파일에서 확인한다]"
---
# /mcp-init

프로젝트 루트에 `.mcp.json` 을 만들고, 그 안에 DB MCP 서버 예시 하나를 넣는다. 사용자가 자리표시자를 실제 값으로 채운 뒤 `/mcp-update` 로 settings 에 등록한다. 범위는 프로젝트뿐이다. 전역(user 범위) MCP 는 다루지 않는다.

먼저 이 skill 폴더 기준 `../../shared/common-rules.md` 를 읽고 그 규칙을 따른다.

<steps>
1. 현재 상태 확인
   - 루트에 `.mcp.json` 이 이미 있으면 덮어쓰지 않는다. 서버 이름과 실행 방식만 정리해 보여 주고, 접속 정보 값은 출력하지 않는다. 서버를 settings 에 등록하려면 `/mcp-update` 를 쓰라고 안내하고 멈춘다.
   - 새 서버를 기존 파일에 추가하고 싶다고 하면, 추가할 항목만 diff 로 보여 주고 확인 후 반영한다.

2. 필요한 정보 정하기

   - DB 종류: 인자 또는 빌드 파일(pom.xml, build.gradle 의 JDBC 드라이버 등)에서 확인한다. 확인되지 않으면 추측하지 않고 묻는다.
   - 운영체제: Windows 이면 `cmd /c npx ...` 로 감싸 실행한다. 이 프로젝트의 기존 서버가 이 방식이면 그것을 따른다.
3. 예시 작성 (서버 하나)

   - 서버 이름은 DB 이름을 쓴다. 프로젝트명과 DB 이름이 다를 수 있어서 `<DB이름>` 자리표시자로 둔다. DB 가 여러 개여도 DB 이름은 서로 다르므로 서버 이름이 겹치지 않는다.
   - PostgreSQL 이면 `@modelcontextprotocol/server-postgres` 를 쓴다. 다른 DB 는 쓸 MCP 패키지를 사용자에게 확인한다.
   - 접속에 필요한 값은 아래 자리표시자로 둔다. 값을 추측해서 채우지 않는다.

   | 항목        | 자리표시자     |
   | ----------- | -------------- |
   | 호스트(url) | `<호스트>`   |
   | 포트        | `<포트>`     |
   | DB 이름     | `<DB이름>`   |
   | 사용자      | `<사용자>`   |
   | 비밀번호    | `<비밀번호>` |


   - 접속 문자열은 `postgresql://<사용자>:<비밀번호>@<호스트>:<포트>/<DB이름>` 형태로 `args` 에 넣는다.
4. 비밀번호 처리 확인

   - `.gitignore` 에 `.mcp.json` 이 있으면(git 에 올라가지 않으면) 자리표시자를 그대로 쓰고, 이 파일이 git 에서 제외되어 있다고 알린다.
   - `.mcp.json` 이 `.gitignore` 에 없으면 비밀번호 자리는 `${DB_PASSWORD}` 처럼 환경변수 참조로 쓴다. 환경변수 설정은 사용자가 직접 하며, 파일에 실제 비밀번호를 쓰지 말라고 알린다.
5. 파일 생성과 안내

   - 만든 `.mcp.json` 내용을 그대로 보여 준다.
   - 다음 순서를 안내한다: `<...>` 자리를 실제 값으로 채운다 → `/mcp-update` 를 실행한다 → 세션을 다시 시작한다.
   - DB 서버와 접속 정보는 사용자가 직접 준비한다. 조언만 한다.

<example>
Windows, PostgreSQL 인 경우의 결과이다.

```json
{
  "mcpServers": {
    "<DB이름>": {
      "command": "cmd",
      "args": [
        "/c",
        "npx",
        "-y",
        "@modelcontextprotocol/server-postgres",
        "postgresql://<사용자>:<비밀번호>@<호스트>:<포트>/<DB이름>"
      ]
    }
  }
}
```

</example>
