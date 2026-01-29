# Hard PR

Atlassian Bitbucket과 Jira를 위한 멀티 브랜치 PR 생성 및 티켓 관리 자동화 Claude Code 플러그인입니다.

## Features

- 피처 브랜치에서 dev, main, stg 브랜치로 **동시 PR 생성**
- **Jira 티켓 자동 업데이트** (PR 링크 코멘트 추가)
- **새 Jira 티켓 생성** 옵션
- **자동 탐색**: 브랜치 이름에서 티켓 번호 추출, Git remote에서 저장소 정보 추출

## Installation

### Step 1: 저장소 클론

```bash
git clone https://github.com/0-ROK/hard-pr.git ~/.claude/plugins/hard-pr
```

### Step 2: 플러그인 설치

Claude Code에서:
```bash
/plugin install ~/.claude/plugins/hard-pr
```

### Step 3: 환경변수 설정

`~/.zshrc` 또는 `~/.bashrc`에 추가:

```bash
# 필수: Atlassian 공통 설정
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."  # https://id.atlassian.com/manage-profile/security/api-tokens

# 선택: Jira 전용 설정 (없으면 위의 ATLASSIAN_* 값 사용)
export JIRA_URL="https://your-company.atlassian.net"
export JIRA_USERNAME="your.email@company.com"
export JIRA_API_TOKEN="ATATT..."
```

설정 적용:
```bash
source ~/.zshrc
```

### Step 4: Claude Code 재시작

MCP 서버 설정을 로드하려면 재시작이 필요합니다.

## Usage

### 기본 사용법

```bash
# 브랜치 이름에서 티켓 자동 추출 (예: feature/PROJ-123 → PROJ-123)
/hard-pr:release

# 특정 티켓 지정
/hard-pr:release PROJ-123
```

### 옵션

```bash
# 특정 브랜치만 대상
/hard-pr:release PROJ-123 --target dev,main

# 새 Jira 티켓 생성과 함께
/hard-pr:release --new "새로운 기능 구현"
```

### 기타 명령어

```bash
# 도움말
/hard-pr:help

# 설정 가이드
/hard-pr:setup
```

## Workflow

```
/hard-pr:release

[1/5] 환경 검증...
      ✓ 현재 브랜치: feature/PROJ-123
      ✓ 커밋되지 않은 변경사항 없음

[2/5] 저장소 정보 추출...
      ✓ workspace: my-workspace
      ✓ repo: my-repo

[3/5] Jira 티켓 탐색...
      ✓ 브랜치에서 추출: PROJ-123

[4/5] Bitbucket PR 생성...
      ✓ PR → dev
      ✓ PR → main
      ✓ PR → stg

[5/5] Jira 티켓 업데이트...
      ✓ 코멘트 추가

완료!
```

## Auto-Discovery

플러그인은 다음을 자동으로 탐색합니다:

| 항목 | 탐색 방법 |
|------|----------|
| **Bitbucket 저장소** | `git remote get-url origin`에서 workspace/repo 추출 |
| **Jira 티켓** | 브랜치 이름에서 패턴 매칭 (`feature/PROJ-123` → `PROJ-123`) |
| **Jira 프로젝트** | Jira MCP로 사용 가능한 프로젝트 목록 조회 및 제안 |

## Commands

| Command | Description |
|---------|-------------|
| `/hard-pr:release` | 메인 워크플로우 - PR 생성 및 Jira 처리 |
| `/hard-pr:help` | 도움말 표시 |
| `/hard-pr:setup` | 초기 설정 가이드 |

## Requirements

### MCP Servers (플러그인 설치 시 자동 설정)
- [@aashari/mcp-server-atlassian-bitbucket](https://github.com/aashari/mcp-server-atlassian-bitbucket)
- [mcp-atlassian](https://github.com/sooperset/mcp-atlassian)

### Dependencies
- Node.js 18+
- Python 3.8+ (uvx)

## Update

```bash
cd ~/.claude/plugins/hard-pr
git pull
```

## Uninstall

```bash
# 플러그인 제거
/plugin uninstall hard-pr

# 파일 삭제
rm -rf ~/.claude/plugins/hard-pr
```

## License

MIT
