---
name: lsp-init
description: 프로젝트 스택을 확인해 필요한 LSP 플러그인을 전역 또는 프로젝트 settings 에 켠다. 사용자가 /lsp-init 으로 직접 호출할 때만 쓴다.
disable-model-invocation: true
argument-hint: "[전역|프로젝트] [언어 — 생략하면 프로젝트에서 감지한다]"
---

# /lsp-init

프로젝트에서 쓰는 언어를 감지해 맞는 LSP 플러그인을 켠다. 전역에 켤지 프로젝트에 켤지는 사용자가 고른다.

먼저 이 skill 폴더 기준 `../../shared/common-rules.md` 를 읽고 그 규칙을 따른다.

<scope_choice>
| 범위 | 반영 위치 | 효과 |
| --- | --- | --- |
| 전역 | `~/.claude/settings.json` 의 `enabledPlugins` | 모든 프로젝트에서 켜진다 |
| 프로젝트 | `.claude/settings.json` 의 `enabledPlugins` | 이 프로젝트에서만 켜지고, 파일이 git 에 있으면 팀과 공유된다 |
| 프로젝트(개인) | `.claude/settings.local.json` 의 `enabledPlugins` | 이 프로젝트에서만, 나만 켠다 |

- 인자에 `전역`, `global`, `user` 또는 `프로젝트`, `project`, `local` 이 있으면 그 범위로 한다.
- 없으면 시작할 때 사용자에게 고르게 한다. 선택지는 "전역", "프로젝트" 두 가지이고, 프로젝트를 고르면 팀 공유(`settings.json`)인지 개인(`settings.local.json`)인지를 이어서 묻는다.
- 참고 의견은 줄 수 있다. 여러 프로젝트에서 같이 쓰는 언어(HTML, CSS, JSON 등)는 전역이, 이 프로젝트 스택에만 필요한 것은 프로젝트가 어울린다. 결정은 사용자가 한다.
</scope_choice>

<detection>
증거가 있는 언어만 대상으로 삼는다.

| 증거 | LSP 플러그인 |
| --- | --- |
| pom.xml, build.gradle (Java) | `jdtls-lsp@claude-plugins-official` |
| package.json, jsconfig.json, tsconfig.json, .js/.ts 파일 | `typescript-lsp@claude-plugins-official` |
| .html, .css, .scss, .json 파일 | `web-lsp@local-plugins` |
| 그 밖의 언어 | `pyright-lsp`, `gopls-lsp`, `rust-analyzer-lsp`, `clangd-lsp`, `kotlin-lsp`, `csharp-lsp`, `php-lsp`, `lua-lsp`, `swift-lsp`, `ruby-lsp` (`@claude-plugins-official`) |
</detection>

<steps>
1. 범위 정하기: 위 `scope_choice` 대로 한다.

2. 감지
   - 인자로 언어를 받았으면 그것만 본다.
   - 현재 프로젝트 파일에서 위 표의 증거를 찾는다.
   - 전역을 골랐으면 감지된 언어를 기본 후보로 보여 주고, 사용자가 더하거나 뺄 수 있게 한다. 전역은 현재 프로젝트 밖에도 영향을 주기 때문이다.

3. 현재 상태 확인
   - `~/.claude/settings.json`, `.claude/settings.json`, `.claude/settings.local.json` 의 `enabledPlugins` 를 읽는다.
   - 고른 범위에 이미 켜져 있으면 대상에서 뺀다.
   - 다른 범위에만 켜져 있으면 대상에는 두되, 어느 범위에 켜져 있는지 알려 준다.

4. 언어 서버 실행 파일 확인 (읽기 전용)
   - 각 플러그인의 README(`~/.claude/plugins/marketplaces/*/plugins/{이름}/README.md`) 또는 `.lsp.json` 에서 필요한 실행 파일 이름을 찾는다.
   - `where.exe` 로 PATH 에 있는지만 확인한다. 설치는 사용자가 한다. 설치 명령은 안내만 한다.

5. 반영안 보여 주기
   - 고른 범위의 파일에서 `enabledPlugins` 에 추가할 항목만 diff 로 보여 준다.
   - 기존 키(`env`, `hooks`, `enabledMcpjsonServers` 등)는 그대로 둔다.
   - 파일이 없으면 새로 만들 내용 전체를 보여 준다.

6. 확인 후 반영하고 마무리
   - 플러그인이 설치되어 있지 않으면 `/plugin install {이름}@{마켓플레이스}` 를 사용자가 실행하도록 안내한다. 설치 범위도 고른 범위(전역은 user, 프로젝트는 project, 개인은 local)로 하라고 알린다.
   - 세션을 다시 시작해야 반영된다고 알린다.
</steps>
