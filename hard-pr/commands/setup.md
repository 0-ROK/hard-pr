---
description: "Interactive setup for Atlassian API tokens and configuration"
---

# Release Workflow Setup

이 가이드는 `release-workflow` 플러그인을 설정하는 과정을 안내합니다.

## Step 1: Atlassian API Token 생성

1. https://id.atlassian.com/manage-profile/security/api-tokens 접속
2. Atlassian 계정으로 로그인
3. "Create API token" 클릭
4. 토큰 이름 입력: `Claude Code Release Workflow`
5. "Create" 클릭
6. 생성된 토큰을 안전한 곳에 복사

**중요**: 토큰은 한 번만 표시됩니다. 분실 시 새로 생성해야 합니다.

## Step 2: Bitbucket 환경변수 설정

터미널에서 쉘 설정 파일을 열어 환경변수를 추가합니다:

```bash
# zsh 사용자 (macOS 기본)
nano ~/.zshrc

# bash 사용자
nano ~/.bashrc
```

다음 내용을 추가합니다:

```bash
# ========================================
# Release Workflow Plugin - Bitbucket 설정
# ========================================

# Bitbucket MCP용 (API 토큰 인증)
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."  # API 토큰
```

저장 후 설정을 적용합니다:

```bash
source ~/.zshrc  # 또는 source ~/.bashrc
```

## Step 3: 환경변수 확인

설정이 올바른지 확인합니다:

```bash
echo "Email: $ATLASSIAN_USER_EMAIL"
echo "Token: ${ATLASSIAN_API_TOKEN:0:10}..."
```

두 값이 모두 출력되면 Bitbucket 설정이 완료된 것입니다.

## Step 4: Atlassian (Jira/Confluence) OAuth 인증

이 플러그인은 **공식 Atlassian Remote MCP Server**를 사용합니다.
Jira와 Confluence 연동은 OAuth 2.1 인증을 통해 이루어집니다.

### OAuth 인증 방법

1. Claude Code에서 `/mcp` 입력
2. `atlassian` 서버 선택
3. 브라우저가 열리면 Atlassian 계정으로 로그인
4. 요청된 권한 승인

**참고:**
- 환경변수 설정이 필요 없습니다 (브라우저에서 직접 로그인)
- OAuth 토큰은 주기적으로 만료될 수 있으며, 만료 시 `/mcp`로 재인증하면 됩니다
- Jira, Confluence, Compass를 모두 사용할 수 있습니다

## Step 5: Claude Code 재시작

환경변수와 MCP 서버 설정을 로드하려면 Claude Code를 재시작해야 합니다.

```bash
# 현재 Claude Code 세션 종료 후 다시 시작
```

## Step 6: 연결 테스트

### Bitbucket 연결 테스트
"내 Bitbucket 워크스페이스 목록을 조회해줘"라고 요청해보세요.

### Jira 연결 테스트
"내 Jira 프로젝트 목록을 조회해줘"라고 요청해보세요.

### MCP 상태 진단
문제가 발생하면 `/release-workflow:mcp-doctor`를 실행하여 진단하세요.

## Step 7: 첫 실행

피처 브랜치에서 다음 명령을 실행해보세요:

```bash
# 브랜치 이름에서 티켓 자동 추출
/release-workflow:release

# 또는 티켓 직접 지정
/release-workflow:release PROJ-123
```

## 자동 탐색 기능

플러그인은 다음을 자동으로 탐색합니다:

1. **Bitbucket 저장소**: `git remote get-url origin`에서 workspace/repo 추출
2. **Jira 티켓**: 브랜치 이름에서 티켓 번호 추출 (예: `feature/PROJ-123` → `PROJ-123`)
3. **Jira 프로젝트**: Jira MCP로 사용 가능한 프로젝트 목록 조회 및 제안

## Troubleshooting

문제가 발생하면 먼저 `/release-workflow:mcp-doctor`를 실행하여 자동 진단을 받으세요.

### 환경변수가 인식되지 않음

Claude Code를 완전히 종료한 후 다시 시작하세요:
```bash
# 터미널에서
source ~/.zshrc
# Claude Code 재시작
```

### Bitbucket API 토큰 인증 실패

1. 토큰이 올바르게 복사되었는지 확인 (공백 없이)
2. 이메일 주소가 Atlassian 계정과 일치하는지 확인
3. 토큰이 만료되지 않았는지 확인 (기본적으로 무기한)

### Atlassian (Jira/Confluence) 인증 실패

1. `/mcp` 명령으로 재인증 시도
2. 브라우저 쿠키/캐시 삭제 후 재시도
3. Atlassian 계정에 해당 사이트 접근 권한 확인

### MCP 서버 연결 실패

1. Node.js 18+ 설치 확인: `node --version`
2. 네트워크 연결 확인
3. `/release-workflow:mcp-doctor`로 상세 진단

### Bitbucket 권한 오류

API 토큰 소유자가 해당 워크스페이스/저장소에 접근 권한이 있는지 확인하세요.

### Jira 권한 오류

OAuth로 로그인한 계정이 해당 Jira 프로젝트에 이슈 조회/생성/코멘트 권한이 있는지 확인하세요.

## Setup Complete!

설정이 완료되면 다음 명령으로 워크플로우를 시작할 수 있습니다:

```bash
# 기본 사용 (브랜치에서 티켓 자동 추출)
/release-workflow:release

# 티켓 지정
/release-workflow:release PROJ-123

# 도움말
/release-workflow:help
```
