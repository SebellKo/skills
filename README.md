# Skills

독립적으로 사용하거나 워크플로에서 조합해 사용하는 개인 스킬 모음입니다.

| 스킬 | 역할 |
| --- | --- |
| [clarify-requirements](skills/clarify-requirements/SKILL.md) | 사용자 설명, 디자인, 백엔드 자료를 바탕으로 요구사항과 완료 조건을 정리합니다. |
| [design-code](skills/design-code/SKILL.md) | 독립적인 서브에이전트 설계를 메인이 비교·통합해 하나의 코드 설계안으로 정리합니다. |
| [implement-code](skills/implement-code/SKILL.md) | 서브에이전트에게 구현을 위임하고 메인이 실제 변경과 실행 결과를 검토합니다. |
| [qa-feature](skills/qa-feature/SKILL.md) | 독립 QA 에이전트가 변경 전 기준과 변경 후 기능·회귀를 검증하고 웹 화면은 ego lite로 확인합니다. |
| [review-code](skills/review-code/SKILL.md) | Interrogate, 구조·유지보수, Devil’s critic의 독립 리뷰를 메인이 검증하고 통합합니다. |
| [dev-mode](skills/dev-mode/SKILL.md) | 명시적으로 호출하면 작업을 네 플레이북으로 분류하고 요구사항·설계·구현·리뷰·QA를 연결해 진행합니다. |

각 스킬은 `skills/<스킬 이름>/`에서 관리합니다.

`dev-mode`는 위의 다섯 모듈 스킬과 함께 설치하고, `$dev-mode 작업 설명`으로 명시적으로 호출합니다. 기능 구현·버그 수정·단순 수정·리팩터링 플레이북 중 작업에 맞는 흐름을 선택합니다.
