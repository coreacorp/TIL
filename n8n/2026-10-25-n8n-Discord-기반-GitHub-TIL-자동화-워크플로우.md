### Context
* 일상적으로 학습한 기술 내용을 디스코드 메시지로 기록하면, 이를 자동으로 정제하여 GitHub TIL(Today I Learned) 저장소에 업로드하고 인덱스를 업데이트하는 자동화 시스템을 구축함.
* 수동으로 파일을 생성하고, 커밋(Commit)하며, README.md 파일을 수정하는 반복적인 작업을 n8n 워크플로우를 통해 해결하려는 목적임.

### Core
n8n 워크플로우는 다음과 같은 5단계의 노드 흐름으로 구성됨.

* **Discord Trigger**: `messageCreate` 이벤트를 감지하여 사용자가 채널에 보낸 메시지 원문을 수신함.
* **AI Agent (LLM)**: 수신된 텍스트를 분석하여 제목을 추출하고, 사전에 정의된 TIL 마크다운 형식(Context, Core, Insight)으로 변환함.
* **GitHub Node (File Push)**: 정제된 마크다운 내용을 `YYYY-MM-DD-제목.md` 형식의 파일명으로 지정된 저장소 경로에 신규 생성함.
* **GitHub Node (README Update)**: 기존 `README.md` 파일을 불러온 뒤, 새롭게 생성된 TIL 파일의 링크를 인덱스 목록 최상단에 추가하여 업데이트함.
* **Discord Node**: 모든 과정이 완료되면 디스코드 채널로 성공 메시지와 함께 GitHub 링크를 발송함.

```json
// AI Agent 노드에서 출력되는 데이터 구조 예시
{
  "title": "n8n 자동화 워크플로우 설계",
  "content": "### Context\n디스코드 메시지를 활용한...",
  "filename": "2026-10-25-n8n-자동화-워크플로우-설계.md"
}
```

### Insight
* n8n 워크플로우 설계의 핵심은 각 노드가 받아들이는 '입력(Input)' 데이터와 내보내는 '출력(Output)' 데이터의 구조를 정확히 매핑하는 것임.
* JSON 형태의 데이터 흐름을 이해하면 복잡한 데이터 변환(Data Transformation)도 추가 코드 작성 없이 노드 간 연결만으로 처리 가능함.
* GitHub 노드를 활용해 README.md를 업데이트할 때는 기존 파일의 SHA 값을 참조하여 업데이트(Update) 작업을 수행해야 충돌을 방지할 수 있음.

**출처:**
* [n8n Discord Trigger Node Documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.discordtrigger/)
* [n8n GitHub Node Documentation](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.github/)
* [GitHub API: Create or update file contents](https://docs.github.com/en/rest/repos/contents#create-or-update-file-contents)