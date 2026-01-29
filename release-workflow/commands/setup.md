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

## Step 2: 환경변수 설정

터미널에서 쉘 설정 파일을 열어 환경변수를 추가합니다:

```bash
# zsh 사용자
nano ~/.zshrc

# bash 사용자
nano ~/.bashrc
```

다음 내용을 추가합니다:

```bash
# Atlassian Bitbucket 설정
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT3..."

# Atlassian Jira 설정
export JIRA_URL="https://your-company.atlassian.net"
export JIRA_USERNAME="your.email@company.com"
export JIRA_API_TOKEN="ATATT3..."  # Bitbucket과 동일한 토큰 사용 가능
```

저장 후 설정을 적용합니다:

```bash
source ~/.zshrc  # 또는 source ~/.bashrc
```

## Step 3: 환경변수 확인

설정이 올바른지 확인합니다:

```bash
echo $ATLASSIAN_USER_EMAIL
echo $ATLASSIAN_API_TOKEN
echo $JIRA_URL
```

모든 값이 출력되면 설정이 완료된 것입니다.

## Step 4: MCP 서버 테스트

플러그인의 MCP 서버가 정상 작동하는지 테스트합니다.

### Bitbucket 연결 테스트
Bitbucket MCP 서버에 "내 워크스페이스 목록을 조회해줘"라고 요청해보세요.

### Jira 연결 테스트
Jira MCP 서버에 "최근 이슈 목록을 조회해줘"라고 요청해보세요.

## Step 5: 첫 실행

피처 브랜치에서 다음 명령을 실행해보세요:

```bash
/release-workflow:release TEST-001
```

## Troubleshooting

### 환경변수가 인식되지 않음

Claude Code를 완전히 종료한 후 다시 시작하세요:
```bash
# 터미널에서 Claude Code 종료 후
source ~/.zshrc
claude
```

### API 토큰 인증 실패

1. 토큰이 올바르게 복사되었는지 확인
2. 이메일 주소가 Atlassian 계정과 일치하는지 확인
3. 토큰이 만료되지 않았는지 확인 (무기한 유효)

### Bitbucket 권한 오류

API 토큰 소유자가 해당 워크스페이스/저장소에 접근 권한이 있는지 확인하세요.

### Jira 권한 오류

API 토큰 소유자가 해당 Jira 프로젝트에 이슈 조회/생성/코멘트 권한이 있는지 확인하세요.

## Configuration Complete

설정이 완료되면 다음 명령으로 워크플로우를 시작할 수 있습니다:

```bash
/release-workflow:release <JIRA-TICKET>
```

도움이 필요하면 `/release-workflow:help`를 실행하세요.
