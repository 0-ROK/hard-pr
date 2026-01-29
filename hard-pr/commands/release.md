---
description: "Create PRs to dev, main, stg branches and update Jira ticket"
argument-hint: "[JIRA-TICKET] [--target branches] [--new 'title']"
allowed-tools: [Bash, Read, Glob]
---

# Release Workflow

피처 브랜치에서 dev, main, stg 브랜치로 Bitbucket PR을 동시 생성하고 Jira 티켓을 처리하는 자동화 워크플로우입니다.

## 입력 파라미터
- `$ARGUMENTS`: Jira 티켓 번호 또는 옵션 (선택적)
  - `PROJ-123`: 기존 티켓에 PR 링크 추가
  - `--new "제목"`: 새 Jira 티켓 생성과 함께 PR 생성
  - `--target dev,main`: 특정 브랜치만 대상으로 PR 생성
  - **인자 없음**: 브랜치 이름에서 티켓 번호 자동 추출

## 실행 단계

### 1단계: 환경 검증

현재 Git 상태를 확인합니다:

```bash
# 현재 브랜치 확인
git branch --show-current

# 커밋되지 않은 변경사항 확인
git status --porcelain

# 원격 저장소 정보 확인
git remote -v
```

**검증 기준:**
- 현재 브랜치가 main, dev, stg가 아닌 피처 브랜치여야 함
- 커밋되지 않은 변경사항이 없어야 함
- 원격 저장소가 Bitbucket이어야 함

오류 발생 시:
- 피처 브랜치가 아닌 경우: 경고 메시지 출력 후 중단
- 커밋되지 않은 변경사항: 커밋 또는 스태시 안내

### 2단계: 저장소 정보 자동 추출

Git remote URL에서 workspace와 repo_slug를 **자동으로** 추출합니다:

```bash
# Bitbucket remote URL 파싱
git remote get-url origin
```

**지원 URL 형식:**
- SSH: `git@bitbucket.org:workspace/repo.git`
- HTTPS: `https://bitbucket.org/workspace/repo.git`

**추출 정보:**
- `workspace`: Bitbucket 워크스페이스 이름
- `repo_slug`: 저장소 슬러그

### 3단계: Jira 프로젝트/티켓 자동 탐색

**인자가 없거나 브랜치 이름에서 추출해야 하는 경우:**

1. **브랜치 이름에서 티켓 번호 추출 시도**
   ```
   feature/PROJ-123-some-feature → PROJ-123
   bugfix/PROJ-456 → PROJ-456
   PROJ-789-hotfix → PROJ-789
   ```

   정규식 패턴: `([A-Z]+-\d+)`

2. **Jira MCP로 프로젝트 존재 여부 확인**

   `jira_get_projects` 도구로 사용 가능한 프로젝트 목록 조회

   추출된 프로젝트 키(예: PROJ)가 목록에 있는지 확인

3. **매칭 결과에 따른 처리**

   - **매칭 성공**: 해당 티켓 번호 사용
   - **매칭 실패**: 사용 가능한 프로젝트 목록을 제안하고 사용자에게 선택 요청

   ```
   브랜치 이름에서 Jira 티켓을 찾을 수 없습니다.

   사용 가능한 프로젝트:
   1. PROJ - Project Name
   2. DEV - Development
   3. QA - Quality Assurance

   티켓 번호를 입력하거나, --new "제목" 옵션으로 새 티켓을 생성하세요.
   ```

### 4단계: 브랜치 푸시 (필요시)

현재 브랜치가 원격에 푸시되지 않았다면:

```bash
git push -u origin $(git branch --show-current)
```

### 5단계: Bitbucket PR 생성

각 대상 브랜치(dev, main, stg)에 대해 Bitbucket MCP 서버의 `bb_post` 도구를 사용하여 PR을 생성합니다.

**PR 생성 요청 (각 브랜치별):**

도구: `bb_post`
경로: `/repositories/{workspace}/{repo_slug}/pullrequests`
본문:
```json
{
  "title": "[TICKET] Feature description from branch name",
  "source": {
    "branch": {
      "name": "현재_브랜치_이름"
    }
  },
  "destination": {
    "branch": {
      "name": "대상_브랜치"
    }
  },
  "description": "## Summary\n- Feature implementation\n\n## Related\n- Jira: [TICKET_URL]"
}
```

**대상 브랜치 (기본값):**
1. `dev` - 개발 브랜치
2. `main` - 메인 브랜치
3. `stg` - 스테이징 브랜치

`--target` 옵션이 있으면 지정된 브랜치만 대상으로 합니다.

**에러 처리:**
- 이미 PR이 존재하는 경우: `bb_get`으로 기존 PR 조회 후 URL 반환
- 대상 브랜치가 존재하지 않는 경우: 스킵하고 경고 메시지 출력

### 6단계: Jira 티켓 처리

#### 6-1. 기존 티켓 업데이트 (티켓 번호가 있는 경우)

Jira MCP 서버를 사용하여 티켓에 코멘트를 추가합니다:

1. **티켓 조회**: `jira_get_issue` 도구로 티켓 존재 여부 확인
2. **코멘트 추가**: `jira_add_comment` 도구로 PR 링크 추가

코멘트 내용:
```
PR이 생성되었습니다:
- dev: [PR URL]
- main: [PR URL]
- stg: [PR URL]

생성 시간: [현재 시간]
```

#### 6-2. 새 티켓 생성 (`--new "제목"` 옵션인 경우)

1. **프로젝트 선택**: `jira_get_projects`로 프로젝트 목록 조회 후 제안
2. **티켓 생성**: `jira_create_issue` 도구 사용
   - 프로젝트: 사용자 선택 또는 기본 프로젝트
   - 이슈 타입: Task
   - 요약: 지정된 제목
   - 설명: PR 링크 포함

### 7단계: 결과 요약

실행 결과를 다음 형식으로 출력합니다:

```
============================================
Release Workflow 완료
============================================

[Bitbucket PR]
  - dev:  https://bitbucket.org/{workspace}/{repo}/pull-requests/{id}
  - main: https://bitbucket.org/{workspace}/{repo}/pull-requests/{id}
  - stg:  https://bitbucket.org/{workspace}/{repo}/pull-requests/{id}

[Jira]
  - 티켓: https://{domain}.atlassian.net/browse/{ticket}
  - 상태: 코멘트 추가됨

============================================
다음 단계:
1. 각 PR에 리뷰어 지정
2. CI/CD 파이프라인 결과 확인
3. 코드 리뷰 완료 후 머지
============================================
```

## 필요 조건

### MCP 서버 (플러그인 설치 시 자동 설정)
- Bitbucket MCP: `@aashari/mcp-server-atlassian-bitbucket`
- Jira MCP: `mcp-atlassian` (sooperset)

### 환경변수

**필수:**
```bash
export ATLASSIAN_USER_EMAIL="your.email@company.com"
export ATLASSIAN_API_TOKEN="ATATT..."  # https://id.atlassian.com/manage-profile/security/api-tokens
```

**Jira 전용 (선택적 - 없으면 ATLASSIAN_* 사용):**
```bash
export JIRA_URL="https://your-company.atlassian.net"
export JIRA_USERNAME="your.email@company.com"
export JIRA_API_TOKEN="ATATT..."
```

### 권한
- Bitbucket: 저장소 읽기/쓰기, PR 생성
- Jira: 이슈 조회/생성/코멘트 추가

## 사용 예시

```bash
# 브랜치 이름에서 티켓 자동 추출
/hard-pr:release

# 특정 티켓 지정
/hard-pr:release PROJ-123

# 특정 브랜치만 대상
/hard-pr:release --target dev,main

# 새 Jira 티켓 생성
/hard-pr:release --new "새로운 기능 구현"
```
