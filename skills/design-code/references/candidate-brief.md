# 후보 설계 지침

공통 설명의 문제 전체를 독립적으로 설계한다. 제공된 코드와 근거를 직접 확인하고, 기존 계약과 관례를 조사한다. 여러 에이전트가 같은 파일 시스템을 사용하므로 공유 코드나 다른 후보의 파일을 수정하지 않는다. 추가 에이전트는 생성하지 않는다.

## 필요한 원칙을 읽는다

메인과 각 후보는 아래 조건에 맞는 원칙의 `SKILL.md`를 직접 읽고 설계·비교에 적용한다. 이미 현재 맥락에서 읽은 파일은 다시 읽지 않는다. 링크가 없으면 설치 목록에서 같은 이름을 찾고, 그래도 없으면 누락을 알린다. 원칙을 이유로 합의한 범위를 확대하거나 제품 결정을 바꾸지 않는다.

| 적용 상황 | 읽을 원칙 |
| --- | --- |
| 핵심 데이터·접근 패턴·동시 상태를 정할 때 | [foundational-thinking](../../principles/principle-foundational-thinking/SKILL.md) |
| 새 요구사항을 기존 설계에 통합할 때 | [redesign-from-first-principles](../../principles/principle-redesign-from-first-principles/SKILL.md) |
| 구조 변경의 크기와 제거할 복잡성을 판단할 때 | [laziness-protocol](../../principles/principle-laziness-protocol/SKILL.md), [subtract-before-you-add](../../principles/principle-subtract-before-you-add/SKILL.md) |
| 인터페이스·계층·상태 소유권을 정할 때 | [minimize-reader-load](../../principles/principle-minimize-reader-load/SKILL.md); 상태·분기가 얽히면 [model-the-domain](../../principles/principle-model-the-domain/SKILL.md) |
| 외부 입력·프레임워크 경계를 정할 때 | [boundary-discipline](../../principles/principle-boundary-discipline/SKILL.md) |
| 정적 타입·시그니처를 설계할 때 | [type-system-discipline](../../principles/principle-type-system-discipline/SKILL.md); TypeScript면 [typescript-best-practices](../../typescript-best-practices/SKILL.md) |
| 사용자·호출자 경험과 범위를 절충할 때 | [experience-first](../../principles/principle-experience-first/SKILL.md) |
| 선례 없는 구조·상호작용을 선택할 때 | [exhaust-the-design-space](../../principles/principle-exhaust-the-design-space/SKILL.md) |
| 동작 검증 방법을 설계하거나 실험 결과로 선택할 때 | [test-behavior-not-implementation](../../principles/principle-test-behavior-not-implementation/SKILL.md), [prove-it-works](../../principles/principle-prove-it-works/SKILL.md) |
| 후보 분산 조사나 긴 자료를 다룰 때 | [guard-the-context-window](../../principles/principle-guard-the-context-window/SKILL.md) |

## 호출 예제에서 구조를 도출한다

실제 사용 예제로 호출자가 전달할 값, 받을 결과와 오류, 알아야 할 순서·수명 규칙을 보여 준다. 이를 바탕으로 타입·시그니처와 책임을 정하고, 작은 인터페이스 뒤에 복잡한 처리를 모은다.

각 상태의 소유자와 갱신 경로, 관련 실패·취소·재시도·동시 요청의 결과를 설명한다. 바뀌는 사용처와 공개 계약, 필요한 이전 방법과 검증 방법을 제시한다. 중요한 선택의 대안과 단점을 비교하되, 전체 재작성이나 차이를 위한 대안을 전제하지 않는다.

## 반환할 내용

1. 확인한 근거와 가정. 대량 원문 대신 다시 찾을 위치를 붙인다.
2. 추천 구조와 호출 예제, 타입·시그니처.
3. 책임 배치, 상태·데이터 흐름과 오류 처리.
4. 실제 검토한 대안, 추천 이유와 단점.
5. 변경 영향, 검증 방법과 수행한 실험 결과.
6. 해결하지 못한 결정이나 위험.

설계 선택을 좌우하는 사실에 한해 배정된 임시 공간에서 최소 실험을 할 수 있다. 실행하지 않은 검증은 제안으로 표시한다. 사용자에게 물어야 할 결정은 결과에 남겨 메인이 취합할 수 있게 한다.
