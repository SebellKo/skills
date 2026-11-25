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
| [reflect](skills/reflect/SKILL.md) | 명시적으로 호출하면 대화와 제공한 피드백에서 지속적으로 적용할 스킬 개선안을 제안하고, 승인된 수정을 반영합니다. |

작업·회고·TypeScript 스킬은 `skills/<스킬 이름>/`, 원칙 스킬은 `skills/principles/<스킬 이름>/`에서 관리합니다.

`dev-mode`는 위의 다섯 모듈 스킬과 함께 설치하고, `$dev-mode 작업 설명`으로 명시적으로 호출합니다. 기능 구현·버그 수정·단순 수정·리팩터링 플레이북 중 작업에 맞는 흐름을 선택합니다.

## 원칙 스킬

각 원칙은 독립 스킬입니다. 작업 스킬의 메인과 서브에이전트는 해당 상황의 원칙 파일을 직접 읽습니다. 별도 공통 원칙 스킬이나 `dev-mode` 사전 실행은 필요하지 않습니다.

| 스킬 | 원칙 |
| --- | --- |
| [principle-laziness-protocol](skills/principles/principle-laziness-protocol/SKILL.md) | 최소한으로 해결하기 |
| [principle-foundational-thinking](skills/principles/principle-foundational-thinking/SKILL.md) | 기초부터 생각하기 |
| [principle-redesign-from-first-principles](skills/principles/principle-redesign-from-first-principles/SKILL.md) | 새 요구사항을 처음부터 고려해 재설계하기 |
| [principle-attack-the-premise](skills/principles/principle-attack-the-premise/SKILL.md) | 전제 공격하기 |
| [principle-subtract-before-you-add](skills/principles/principle-subtract-before-you-add/SKILL.md) | 추가하기 전에 덜어내기 |
| [principle-minimize-reader-load](skills/principles/principle-minimize-reader-load/SKILL.md) | 읽는 사람의 부담 줄이기 |
| [principle-outcome-oriented-execution](skills/principles/principle-outcome-oriented-execution/SKILL.md) | 목표 상태에 도달하기 |
| [principle-experience-first](skills/principles/principle-experience-first/SKILL.md) | 사용자 경험 우선하기 |
| [principle-exhaust-the-design-space](skills/principles/principle-exhaust-the-design-space/SKILL.md) | 대안을 충분히 탐색하기 |
| [principle-model-the-domain](skills/principles/principle-model-the-domain/SKILL.md) | 도메인을 구조로 표현하기 |
| [principle-boundary-discipline](skills/principles/principle-boundary-discipline/SKILL.md) | 경계에서 검증하기 |
| [principle-type-system-discipline](skills/principles/principle-type-system-discipline/SKILL.md) | 타입으로 잘못된 상태 막기 |
| [principle-prove-it-works](skills/principles/principle-prove-it-works/SKILL.md) | 실제로 동작함을 증명하기 |
| [principle-fix-root-causes](skills/principles/principle-fix-root-causes/SKILL.md) | 근본 원인 고치기 |
| [principle-sequence-verifiable-units](skills/principles/principle-sequence-verifiable-units/SKILL.md) | 검증 가능한 단위로 진행하기 |
| [principle-test-behavior-not-implementation](skills/principles/principle-test-behavior-not-implementation/SKILL.md) | 구현보다 동작 테스트하기 |
| [principle-guard-the-context-window](skills/principles/principle-guard-the-context-window/SKILL.md) | 컨텍스트 보호하기 |

[typescript-best-practices](skills/typescript-best-practices/SKILL.md)는 TypeScript 작업에서 사용하며, 타입·경계 원칙과 구체적인 코드 예제를 연결합니다.

작업 스킬을 설치할 때 참조하는 원칙 스킬도 같은 스킬 루트에 함께 설치하세요. 이 저장소의 `skills/` 내부 구조를 그대로 로컬 스킬 루트에 복사하면 25개 스킬의 참조가 연결됩니다. `principles/`에는 상위 `SKILL.md`를 만들지 않고 17개 독립 스킬을 하위 폴더로 둡니다. 각 작업 스킬은 단독으로 호출할 수 있습니다. 원칙과 TypeScript 스킬은 pstack의 명시적 호출 설정을 Codex의 `allow_implicit_invocation: false`로 옮겼으며, 작업 스킬이 필요한 파일을 읽거나 사용자가 이름으로 직접 호출할 수 있습니다.

원칙과 TypeScript 스킬의 출처는 [pstack 원본](https://github.com/cursor/plugins/tree/41e400932eca14f289e7db717a70455256b6d388/pstack)입니다. 영문 본문을 유지하고 Codex 호출 설정과 참조 링크만 조정했습니다. 17개 원칙 스킬의 원본 커밋과 MIT 고지는 상위 [LICENSE](skills/principles/LICENSE)에서 함께 관리합니다. `reflect`의 고지는 [자체 LICENSE](skills/reflect/LICENSE)에 있습니다. TypeScript와 나머지 스킬은 각 폴더의 `THIRD_PARTY_NOTICES.txt`를 유지합니다.

원칙은 `skills/principles/` 폴더를 `LICENSE`와 함께 통째로 설치하세요. 개별 설치 도구로 원칙 폴더만 스킬 루트에 복사하면 참조 경로가 달라지고 공통 고지가 빠집니다. 원칙 하나를 별도로 복사·배포할 때도 이 파일을 해당 배포본에 포함하고 참조 경로를 맞춰야 합니다.

`$reflect 이번 작업에서 스킬 개선점을 찾아줘`로 회고를 시작합니다. 피드백 문서가 있으면 경로를 함께 전달할 수 있습니다. 수정안은 근거와 대상 파일을 포함하며, 이미 구체적인 수정이 승인된 경우를 제외하면 검토 후 승인을 기다립니다. `dev-mode`가 자동으로 실행하는 단계는 아닙니다.
