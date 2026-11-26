# 게시와 이미지 첨부

연결된 GitHub 도구가 필요한 게시·첨부 기능을 제공하면 우선 사용한다. 본문 편집만 지원하고 파일 첨부가 없으면 GitHub CLI를 사용한다. 로컬 이미지를 실제 GitHub 첨부로 올리고, 반환되거나 본문에 반영된 URL을 사용한다.

## 본문 준비

저장소 PR 템플릿을 읽어 채운 뒤 임시 UTF-8 Markdown 파일로 저장한다. CLI에서는 이 파일을 `--body-file`로 전달한다. `--template` 이름만 지정하거나 커밋 메시지 자동 채우기로 완성된 본문을 대신하지 않는다.

다음은 첨부 전 로컬 본문의 예시다. 실제 캡처가 있는 경우에만 해당 셀을 작성한다.

```markdown
| 변경 전 (Before) | 변경 후 (After) |
| --- | --- |
| ![변경 전 목록](./before.png) | ![변경 후 목록](./after.png) |

시작 위치: 목록 화면의 표시된 새 항목 버튼.
![진입 영역 표시](./entry-marked.png)

1. 새 항목 버튼을 누릅니다. → 입력 창이 열립니다.
2. 필수 항목을 입력하고 저장합니다. → 성공 안내와 새 항목이 표시됩니다.
3. 새로고침합니다. → 새 항목이 유지됩니다.
```

사진별로 화면·버전을 구분하는 대체 텍스트를 사용한다. 일부 이미지가 없으면 빈 링크 대신 미확인 이유를 적는다. 계정은 권한 이름으로만 설명한다.

## 업로드

먼저 현재 설치된 `gh pr create --help` 또는 `gh pr edit --help`에서 `--attach` 지원을 확인한다. 지원하는 버전은 `--attach`로 지정한 로컬 파일 참조를 업로드 URL로 바꾼다. `--body-file` 안의 상대 이미지 경로는 본문 파일 위치 기준으로 맞춘다.

새 PR의 형식은 다음과 같다. 저장소·브랜치·제목은 확인한 실제 값으로 바꾸며, 셸 인자는 안전하게 인용한다.

```sh
gh pr create --repo OWNER/REPO --base BASE --head HEAD --title '작업 결과' --assignee @me --body-file /absolute/task/pr-body.md --attach /absolute/task/before.png --attach /absolute/task/after.png --attach /absolute/task/entry-marked.png
```

이미 PR이 있으면 `gh pr edit PR_URL --body-file ... --attach ... --add-assignee @me`로 갱신한다. 기존 본문의 사용자 편집과 이미 정상 업로드된 링크를 보존하며, 실제 있는 파일만 첨부한다. Draft가 필요하면 생성 시 `--draft`를 사용한다.

`--attach`가 없거나 사용할 수 없으면 설치된 `ego-browser` 스킬의 현재 가이드에 따라 ego lite에서 대상 PR 작성·편집 화면의 파일 첨부 기능을 사용한다. 업로드 결과의 이미지 URL을 본문에 배치한다. GitHub 인증은 해당 서비스의 세션을 사용하며 QA 앱의 테스트 계정을 GitHub에 입력하지 않는다. 업로드를 위해 공개 이미지 호스팅에 임의로 전송하거나 제품 저장소에 스크린샷을 커밋하지 않는다.

## 부분 실패와 검증

첨부 일부가 실패해도 PR 자체는 생성·갱신됐을 수 있다. 명령 종료 코드만 보고 `create`를 다시 실행하지 않는다. 반환 URL과 대상 head·base의 기존 PR을 조회하고, 같은 PR에서 실패한 첨부만 보충한다. 성공한 이미지를 반복 업로드하지 않는다.

게시된 본문을 다시 읽어 로컬 파일 참조가 실제 업로드 URL로 바뀌었는지 확인하고, ego lite에서 이미지가 정상 표시되는지 확인한다. 비공개 저장소는 리뷰어와 같은 접근 권한이 있는 세션으로 확인한다. 업로드·표시가 확인되지 않은 이미지는 완료 근거로 삼지 않고, 남은 내용을 본문과 결과에 표시한다.

공식 참고: [GitHub 파일 첨부](https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/attaching-files), [gh pr create](https://cli.github.com/manual/gh_pr_create), [gh pr edit](https://cli.github.com/manual/gh_pr_edit), [Issue 연결](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue).
