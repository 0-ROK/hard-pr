# Release Workflow

Atlassian Bitbucket과 Jira를 위한 멀티 브랜치 PR 생성 및 티켓 관리 자동화 Claude Code 플러그인입니다.

## Features

- 피처 브랜치에서 dev, main, stg 브랜치로 **동시 PR 생성**
- **Jira 티켓 자동 업데이트** (PR 링크 코멘트 추가)
- **새 Jira 티켓 생성** 옵션
- 특정 브랜치만 대상으로 PR 생성 가능

## Installation

### Option 1: Local Installation

```bash
# 플러그인 디렉토리로 이동 후
/plugin install ./release-workflow
```

### Option 2: npm (배포 후)

```bash
/plugin install @your-org/release-workflow
```

## Configuration

### 1. Atlassian API Token 생성

https://id.atlassian.com/manage-profile/security/api-tokens 에서 API 토큰을 생성합니다.

### 2. 환경변수 설정

`~/.zshrc` 또는 `~/.bashrc`에 추가:

```bash
# Bitbucket 설정
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."

# Jira 설정
export JIRA_URL="https://your-company.atlassian.net"
export JIRA_USERNAME="your.email@company.com"
export JIRA_API_TOKEN="ATATT..."
```

### 3. 설정 적용

```bash
source ~/.zshrc
```

## Usage

### 기본 사용법

```bash
# 기존 Jira 티켓으로 PR 생성
/release-workflow:release PROJ-123
```

### 옵션

```bash
# 특정 브랜치만 대상
/release-workflow:release PROJ-123 --target dev,main

# 새 Jira 티켓 생성과 함께
/release-workflow:release --new "새로운 기능 구현"
```

### 도움말

```bash
/release-workflow:help
```

### 초기 설정 가이드

```bash
/release-workflow:setup
```

## Workflow

```
/release-workflow:release PROJ-123

[1/4] 환경 검증...
      ✓ 현재 브랜치: feature/PROJ-123
      ✓ 커밋되지 않은 변경사항 없음

[2/4] Bitbucket PR 생성...
      ✓ PR → dev: https://bitbucket.org/.../pull-requests/1
      ✓ PR → main: https://bitbucket.org/.../pull-requests/2
      ✓ PR → stg: https://bitbucket.org/.../pull-requests/3

[3/4] Jira 티켓 업데이트...
      ✓ 코멘트 추가: PROJ-123

[4/4] 완료!
```

## Commands

| Command | Description |
|---------|-------------|
| `/release-workflow:release` | 메인 워크플로우 - PR 생성 및 Jira 처리 |
| `/release-workflow:help` | 도움말 표시 |
| `/release-workflow:setup` | 초기 설정 가이드 |

## Requirements

- **MCP Servers**:
  - [@aashari/mcp-server-atlassian-bitbucket](https://github.com/aashari/mcp-server-atlassian-bitbucket)
  - [mcp-atlassian](https://github.com/sooperset/mcp-atlassian)

- **Dependencies**:
  - Node.js 18+
  - Python 3.8+ (uvx)

## License

MIT
