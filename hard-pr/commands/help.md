---
description: "Show release-workflow plugin usage and configuration guide"
---

# Release Workflow Help

## Overview

`release-workflow` 플러그인은 Atlassian Bitbucket과 Jira를 위한 멀티 브랜치 PR 생성 및 티켓 관리 자동화 도구입니다.

## Commands

### /release-workflow:release
메인 워크플로우 실행 - PR 생성 및 Jira 티켓 처리

```bash
# 기존 Jira 티켓으로 PR 생성
/release-workflow:release PROJ-123

# 특정 브랜치만 대상으로 PR 생성
/release-workflow:release PROJ-123 --target dev,main

# 새 Jira 티켓 생성과 함께 PR 생성
/release-workflow:release --new "새로운 기능 구현"
```

### /release-workflow:setup
초기 설정 - 환경변수 및 MCP 서버 설정 가이드

```bash
/release-workflow:setup
```

### /release-workflow:help
이 도움말 표시

```bash
/release-workflow:help
```

## Configuration

### Required Environment Variables

```bash
# ~/.zshrc 또는 ~/.bashrc에 추가

# Bitbucket 설정
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."  # https://id.atlassian.com/manage-profile/security/api-tokens

# Jira 설정
export JIRA_URL="https://your-company.atlassian.net"
export JIRA_USERNAME="your.email@company.com"
export JIRA_API_TOKEN="ATATT..."  # 동일한 토큰 사용 가능
```

### API Token 생성

1. https://id.atlassian.com/manage-profile/security/api-tokens 접속
2. "Create API token" 클릭
3. 토큰 이름 입력 (예: "Claude Code Release Workflow")
4. 생성된 토큰 복사 및 환경변수에 저장

## Workflow

```
/release-workflow:release PROJ-123

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

### "MCP 서버를 찾을 수 없습니다"
- `/release-workflow:setup`을 실행하여 MCP 서버 설정을 확인하세요
- 환경변수가 올바르게 설정되었는지 확인하세요

### "인증 실패"
- API 토큰이 만료되지 않았는지 확인하세요
- 환경변수의 이메일과 토큰이 일치하는지 확인하세요

### "브랜치를 찾을 수 없습니다"
- 대상 브랜치(dev, main, stg)가 원격 저장소에 존재하는지 확인하세요
- `--target` 옵션으로 존재하는 브랜치만 지정하세요

## Links

- [Bitbucket MCP Server](https://github.com/aashari/mcp-server-atlassian-bitbucket)
- [Jira MCP Server](https://github.com/sooperset/mcp-atlassian)
- [Atlassian API Token](https://id.atlassian.com/manage-profile/security/api-tokens)
