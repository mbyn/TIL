### Context
*   TIL(Today I Learned) 문서를 체계적으로 관리하고 GitHub에 자동으로 게시하는 과정의 비효율성을 해결하기 위함.
*   수동 파일 생성, 내용 작성, GitHub 업로드 및 README.md 업데이트에 드는 시간 절약 및 휴먼 에러 방지.

### Core
*   **워크플로우 개요**: n8n을 활용하여 사용자 입력(메모)을 구조화된 TIL 문서(JSON 및 Markdown)로 변환하고, 이를 GitHub 저장소에 업로드 및 기존 README.md 파일을 업데이트하는 자동화 파이프라인.
*   **주요 n8n 노드 구성**:
    *   **Trigger (트리거)**: 워크플로우를 시작하는 노드. (예: Webhook, Manual Trigger)
    *   **Function (함수) 또는 Set (설정)**: 사용자의 파편화된 메모를 TIL 문서 규칙에 맞는 JSON 데이터 및 Markdown 콘텐츠로 변환. (e.g., 날짜 포맷, 파일명 생성, 문서 구조화)
    *   **HTTP Request (HTTP 요청) 또는 GitHub API (GitHub API)**:
        *   **TIL 파일 생성/업데이트**: GitHub API의 'Create or update file contents' (PUT /repos/{owner}/{repo}/contents/{path}) 엔드포인트를 사용하여 TIL Markdown 파일을 지정된 디렉토리(예: `n8n/2026-03-02-n8n을-이용한-TIL-자동화-및-GitHub-업로드.md`)에 업로드.
        *   **README.md 업데이트**: 동일한 GitHub API 엔드포인트를 사용하여 `README.md` 파일의 내용을 업데이트. 일반적으로 새로운 TIL 목록을 추가하거나 요약 정보를 갱신.
        *   **인증**: GitHub Personal Access Token (PAT)을 사용하여 API 요청 인증.
```markdown
PUT /repos/{owner}/{repo}/contents/{path}
Host: api.github.com
Authorization: token YOUR_GITHUB_PAT
Content-Type: application/json

{
  "message": "TIL 문서 추가: 2026-03-02-n8n을-이용한-TIL-자동화-및-GitHub-업로드.md",
  "committer": {
    "name": "Your Name",
    "email": "your_email@example.com"
  },
  "content": "BASE64_ENCODED_FILE_CONTENT",
  "sha": "CURRENT_FILE_SHA_IF_UPDATING" // 파일을 업데이트할 경우 필수
}
```
*   **Base64 인코딩**: GitHub API는 파일 내용을 Base64로 인코딩된 문자열로 요구하므로, n8n 내부에서 `crypto.createHash('base64')` 또는 유사한 기능을 통해 변환 과정을 거침.

### Insight
*   n8n을 활용한 이 자동화 워크플로우는 문서 작성 및 관리의 효율성을 극대화하며, 일관된 문서 형식을 유지하는 데 기여함.
*   GitHub API에 대한 이해와 Base64 인코딩 처리 등 기술적인 복잡성이 존재하지만, n8n의 시각적 워크플로우 빌더를 통해 이를 비교적 쉽게 구현할 수 있음.
*   향후 개선점으로는 새 TIL 문서가 추가될 때마다 자동으로 목차를 생성하여 README.md에 반영하거나, 특정 디렉토리 구조를 감지하여 분류하는 기능을 추가할 수 있음.
*   **출처:**
    *   [n8n GitHub Integration](https://n8n.io/integrations/github)
    *   [GitHub API Documentation: Create or update file contents](https://docs.github.com/en/rest/repos/contents#create-or-update-file-contents)