---
description: "MCP 서버 연결 문제 진단 및 해결 가이드"
allowed-tools: [Bash, Read, Glob]
---

# MCP Doctor

MCP 서버 연결 상태를 진단하고 문제 해결 방법을 안내합니다.

## 진단 플로우

다음 순서로 시스템 상태를 점검합니다:

### Step 1: 환경 확인
시스템 의존성을 확인합니다:

```bash
# Node.js 버전 확인
node --version

# npx 사용 가능 여부
npx --version
```

**필수 요구사항:**
- Node.js 18 이상
- npx 사용 가능

### Step 2: MCP 설정 파일 검증
플러그인의 `.mcp.json` 파일을 확인합니다:

1. 파일 존재 여부 확인
2. JSON 문법 유효성 검증
3. 필수 서버 정의 확인:
   - `bitbucket`: Bitbucket PR 관리용
   - `atlassian`: Jira/Confluence 연동용

**올바른 설정 예시:**
```json
{
  "mcpServers": {
    "bitbucket": {
      "command": "npx",
      "args": ["-y", "@aashari/mcp-server-atlassian-bitbucket"],
      "env": {
        "ATLASSIAN_USER_EMAIL": "${ATLASSIAN_USER_EMAIL}",
        "ATLASSIAN_API_TOKEN": "${ATLASSIAN_API_TOKEN}"
      }
    },
    "atlassian": {
      "type": "sse",
      "url": "https://mcp.atlassian.com/v1/sse"
    }
  }
}
```

### Step 3: Bitbucket 환경변수 확인
Bitbucket MCP에 필요한 환경변수를 점검합니다:

```bash
# 환경변수 존재 여부 확인 (값은 마스킹)
echo "ATLASSIAN_USER_EMAIL: ${ATLASSIAN_USER_EMAIL:+설정됨}"
echo "ATLASSIAN_API_TOKEN: ${ATLASSIAN_API_TOKEN:0:10}..."
```

**필수 환경변수:**
| 변수명 | 설명 |
|--------|------|
| `ATLASSIAN_USER_EMAIL` | Atlassian 계정 이메일 |
| `ATLASSIAN_API_TOKEN` | API 토큰 (https://id.atlassian.com/manage-profile/security/api-tokens) |

**환경변수 미설정 시 해결 방법:**
```bash
# ~/.zshrc 또는 ~/.bashrc에 추가
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."

# 적용
source ~/.zshrc
```

### Step 4: Atlassian Remote MCP 상태
공식 Atlassian MCP는 OAuth 2.1 인증을 사용합니다.

**인증 방법:**
1. Claude Code에서 `/mcp` 입력
2. `atlassian` 서버 선택
3. 브라우저에서 Atlassian 계정으로 로그인
4. 권한 승인

**참고:** OAuth 토큰은 주기적으로 만료될 수 있습니다. 연결 오류 발생 시 `/mcp`로 재인증하세요.

### Step 5: 네트워크 연결 테스트
Atlassian 서버 접근 가능 여부를 확인합니다:

```bash
# Atlassian MCP 서버 연결 테스트
curl -s -o /dev/null -w "%{http_code}" https://mcp.atlassian.com/health || echo "연결 실패"
```

## 일반적인 문제와 해결책

### "MCP 서버를 찾을 수 없습니다"
1. Claude Code 재시작
2. `.mcp.json` 파일 위치 확인 (플러그인 루트 디렉토리)
3. JSON 문법 오류 확인

### "인증 실패" (Bitbucket)
1. `ATLASSIAN_USER_EMAIL`이 Atlassian 계정과 일치하는지 확인
2. API 토큰이 올바르게 복사되었는지 확인 (공백 없이)
3. 토큰 재생성 시도

### "인증 실패" (Atlassian/Jira)
1. `/mcp` 명령으로 재인증
2. Atlassian 계정에 해당 사이트 접근 권한 확인
3. 브라우저 쿠키/캐시 삭제 후 재시도

### "네트워크 오류"
1. 인터넷 연결 확인
2. VPN/프록시 설정 확인
3. 방화벽에서 `mcp.atlassian.com` 허용

## 진단 결과 출력 형식

```
[1/5] 환경 확인...
      ✓ Node.js: v20.10.0
      ✓ npx: 사용 가능

[2/5] MCP 설정 파일 검증...
      ✓ .mcp.json 존재
      ✓ JSON 문법 유효
      ✓ bitbucket 서버 정의됨
      ✓ atlassian 서버 정의됨

[3/5] Bitbucket 환경변수 확인...
      ✓ ATLASSIAN_USER_EMAIL: 설정됨
      ✓ ATLASSIAN_API_TOKEN: 설정됨 (ATATT***...)

[4/5] Atlassian Remote MCP 상태...
      ℹ OAuth 인증 필요
      → `/mcp` 명령으로 인증하세요

[5/5] 네트워크 연결 테스트...
      ✓ mcp.atlassian.com 접근 가능

📋 진단 결과: 1개 조치 필요
   1. Atlassian OAuth 인증 필요
      → Claude Code에서 `/mcp` 입력 후 atlassian 선택
```

## 추가 도움말

- 설정 가이드: `/hard-pr:setup`
- 전체 도움말: `/hard-pr:help`
- [Atlassian MCP 공식 문서](https://support.atlassian.com/atlassian-rovo-mcp-server/docs/getting-started-with-the-atlassian-remote-mcp-server/)
