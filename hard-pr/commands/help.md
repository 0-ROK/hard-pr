---
description: "Show hard-pr plugin usage and configuration guide"
---

# Release Workflow Help

## Overview

`hard-pr` 플러그인은 Atlassian Bitbucket과 Jira를 위한 멀티 브랜치 PR 생성 및 티켓 관리 자동화 도구입니다.

## Commands

### /hard-pr:release
메인 워크플로우 실행 - PR 생성 및 Jira 티켓 처리

```bash
# 기존 Jira 티켓으로 PR 생성
/hard-pr:release PROJ-123

# 특정 브랜치만 대상으로 PR 생성
/hard-pr:release PROJ-123 --target dev,main

# 새 Jira 티켓 생성과 함께 PR 생성
/hard-pr:release --new "새로운 기능 구현"
```

### /hard-pr:setup
초기 설정 - 환경변수 및 MCP 서버 설정 가이드

```bash
/hard-pr:setup
```

### /hard-pr:help
이 도움말 표시

```bash
/hard-pr:help
```

### /hard-pr:mcp-doctor
MCP 서버 연결 문제 진단 및 해결 가이드

```bash
/hard-pr:mcp-doctor
```

## Configuration

### Bitbucket 환경변수 (필수)

```bash
# ~/.zshrc 또는 ~/.bashrc에 추가

export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."  # https://id.atlassian.com/manage-profile/security/api-tokens
```

### Atlassian (Jira/Confluence) 인증

공식 Atlassian Remote MCP를 사용하며, OAuth 2.1로 인증합니다.

```bash
# Claude Code에서 실행
/mcp
# → atlassian 선택 → 브라우저에서 로그인
```

### API Token 생성

1. https://id.atlassian.com/manage-profile/security/api-tokens 접속
2. "Create API token" 클릭
3. 토큰 이름 입력 (예: "Claude Code Release Workflow")
4. 생성된 토큰 복사 및 환경변수에 저장

## Workflow

```
/hard-pr:release PROJ-123

[1/4] 환경 검증...
      ✓ 현재 브랜치: feature/PROJ-123
      ✓ 커밋되지 않은 변경사항 없음

[2/4] Bitbucket PR 생성...
      ✓ PR → dev
      ✓ PR → main
      ✓ PR → stg

[3/4] Jira 티켓 업데이트...
      ✓ 코멘트 추가

[4/4] 완료!
```

## Troubleshooting

문제가 발생하면 먼저 `/hard-pr:mcp-doctor`를 실행하여 자동 진단을 받으세요.

### "MCP 서버를 찾을 수 없습니다"
- `/hard-pr:setup`을 실행하여 MCP 서버 설정을 확인하세요
- Claude Code를 재시작하세요

### "Bitbucket 인증 실패"
- API 토큰이 만료되지 않았는지 확인하세요
- 환경변수의 이메일과 토큰이 일치하는지 확인하세요

### "Jira 인증 실패"
- `/mcp` 명령으로 재인증하세요
- Atlassian 계정에 해당 사이트 접근 권한 확인하세요

### "브랜치를 찾을 수 없습니다"
- 대상 브랜치(dev, main, stg)가 원격 저장소에 존재하는지 확인하세요
- `--target` 옵션으로 존재하는 브랜치만 지정하세요

## Links

- [Atlassian Remote MCP Server (공식)](https://github.com/atlassian/atlassian-mcp-server)
- [Atlassian MCP 문서](https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/)
- [Bitbucket MCP Server](https://github.com/aashari/mcp-server-atlassian-bitbucket)
- [Atlassian API Token](https://id.atlassian.com/manage-profile/security/api-tokens)
