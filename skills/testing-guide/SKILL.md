---
name: testing-guide
description: Apply the team frontend testing guide when writing, reviewing, or refactoring frontend tests and choosing test coverage, structure, mocks, and UI assertions.
---

# Frontend Testing Guide

This guide defines the frontend testing standards our team uses when deciding what to test, how to structure tests, and which tradeoffs to make in this codebase.

## 1. Testing Philosophy And Value

### 1.1 Meaningful Failure

Tests are not written just to see `Pass`. They should fail when the behavior they protect is broken.

- A test that can keep passing after a meaningful regression does not improve safety. It only increases maintenance cost.
- When requirements change or a bug is introduced, the test must fail and provide a signal.
- Regression tests and snapshot tests are still valuable when they protect behavior that would fail after an unintended change.
- If a test does not meaningfully fail for any realistic change, reconsider whether it should exist at all. In many cases, deleting it is the better choice.
- If a test failure does not reveal which requirement was broken, improve the test name, setup, or assertions.

### 1.2 Value-Driven Target Selection

Do not try to test all code at the same density. Evaluate the protection each test adds to the existing suite against the cost of writing, reading, running, and maintaining it. The goal is reliable protection of important behavior, not the fewest tests or the highest coverage number.

- Before adding or retaining a test, identify the important regression it catches that existing tests do not adequately cover.
- When a new feature reuses shared behavior, check what existing tests already protect. Add tests for how the feature uses that behavior and for any differences in its contract, rather than copying all tests for the shared behavior.
- When an integration test already protects a behavior, do not automatically repeat it in unit tests for every participating component or composable. Add focused tests where they provide additional protection or clearer checks of important edge cases.
- Before removing a test, confirm that important requirements and failure cases remain protected.

Prioritize behaviors that matter most to users and the business:

- Core business logic
  Example: payment, authentication
- Complex data transformation logic
- Utility functions with frequent edge cases

Give lower priority to simple getters, setters, type declarations, and behavior already guaranteed by a library. Add tests when they protect an application requirement that those guarantees do not cover.

### 1.3 Test Boundaries And Duplication

1. 연결은 통합 테스트로 한 번만, 문구·조건 분기는 가장 가벼운 하위 경계에서 검증한다. (문구 분기마다 MSW·SSE 전체 마운트를 반복하지 않는다.)
2. 버그 리포트의 비교군(원래 되던 경로)은 수정 대상이 아니므로 새 테스트로 추가하지 않는다.
3. 타입 좁히기(typeof ... === 'string') 커버리지를 채우려고 도메인상 무의미한 동작을 명세화하지 않는다.
4. 새 테스트가 기존 테스트의 검증 범위를 포함하면, 기존 테스트를 확장하지 말고 흡수·제거한다.

## 2. Testing Strategy: Testing Trophy

We follow Kent C. Dodds' `Testing Trophy` model, with the strongest emphasis on integration tests.

Prefer tests that keep the real collaboration needed to deliver a feature intact. A test can exercise components, composables, and state management together while protecting one clearly defined behavior. The number of participating components and the number of concerns being tested are separate choices.

Keep each scenario focused on its intended behavior. Combine multiple actions when their sequence is itself the requirement, not merely because they can be placed in one workflow. Do not extend an existing test with unrelated assertions for a new feature when that behavior belongs in another test.

1. `Static (Lint/Type)`
   Use TypeScript and ESLint to catch typos, type mismatches, and other basic mistakes. Do not duplicate test coverage for things that static analysis already verifies well.
2. `Unit`
   Verify complex calculations or naturally small units such as pure functions, parsers, and validators.
3. `Integration`
   This is our primary tactic. Verify whether multiple components work together to deliver a feature. Prefer sociable tests with minimal mocking.
4. `E2E`
   Verify user scenarios in a real browser environment.
   Example: sign in -> search -> purchase
   This is the safest level, but also the most expensive, so apply it only to critical paths.

Narrow the test scope when:

- The setup is so large that the important condition is hard to see.
- The failure cause is difficult to isolate.
- Replacing external dependencies takes up most of the test.
- Slow tests start hurting the feedback loop.
- One complex rule needs many focused edge-case checks.

Keep the reason for narrowing the scope clear.

## 3. File Structure And Naming Conventions

- Place specs for simple modules or component-local behavior next to the source file.
- If a feature spans multiple components, place the spec under the feature's `tests/` directory or next to the representative entry component.
- Even when the filename follows a representative component name, the test scope should cover the user-observable behavior delivered with its child components and composables.

```text
src/
├── components/
│   ├── BaseButton.vue
│   ├── BaseButton.spec.ts
│   └── Cart/
│       ├── Cart.vue
│       ├── CartRow.vue
│       ├── useCart.ts
│       └── tests/
│           └── Cart.spec.ts
└── utils/
    ├── price.ts
    ├── date.ts
    └── tests/
        ├── price.spec.ts
        └── date.spec.ts
```

### `describe` Block Structure

Use `describe` to show the shared subject of the tests, not the internal implementation structure. Keep it flat by default instead of nesting it.

```tsx
// Components / classes
describe('Cart', () => {
  it('수량을 증가시키면 총액이 재계산된다', () => {
    // ...
  });

  it('수량은 1 미만으로 감소하지 않는다', () => {
    // ...
  });

  it('삭제 버튼을 누르면 해당 상품이 목록에서 사라진다', () => {
    // ...
  });
});

// Public functions
describe('formatPrice', () => {
  it('정수를 원화 문자열로 변환한다', () => {
    // ...
  });
});

describe('applyDiscount', () => {
  it('할인율만큼 차감된 금액을 반환한다', () => {
    // ...
  });
});
```

- Name each `describe` block after the test target. Example: component name, hook name, public function name
- Write each `it` or `test` block as a sentence that describes the expected outcome in that context.
- Do not nest child `describe` blocks. If you need another context, split it into another top-level `describe` block instead.
- Make test names specific enough to reveal both the situation and the expected result.
- Prefer capability-oriented names over implementation-shaped names.
  Example: `user can checkout with a valid cart` is better than `calls submitOrder with transformed payload`.
- Describe what the test actually exercises and observes. Do not claim to verify behavior that has been replaced by a test double.
- Use parameterized tests with readable case names for cases of the same behavior that share an execution flow and assertion structure. Declare each case's inputs and expected values explicitly.
- Split tests when cases require different actions or assertions, instead of selecting them with branches inside the test.
- Each test should be understandable on its own, even when it shares setup with other tests.

## 4. Test Writing Principles

### 4.1 Black-box Tests

Test externally observable behavior, not internal implementation details.

- Do not read private properties or component-internal state directly.
- Write tests from a black-box perspective. If input A is applied, does the user observe result B?
- Do not recompute expected values with the same logic as the implementation under test. Derive expectations independently from the requirements so that the test can catch errors in that logic.
- If a refactor breaks the test without changing behavior, the test was too coupled to the implementation.

### 4.2 Avoid Designing Only For Tests

Be careful not to damage production code readability or introduce unnecessary abstractions just to make tests easier to write.

- First check whether the behavior can be verified through an existing public entrypoint before changing production code for testability.
- Change the design only when the testing value clearly outweighs the design cost.
- In most cases, improving a function interface is better than making the design more complex for testability.

### 4.3 Coverage

Coverage is a signal, not the quality goal itself.

- Whether changed code protects important branches and failure cases matters more than total coverage.
- If PR coverage is low, explain why the code does not need tests or which higher-level test covers it.
- Use missing lines in the coverage report to find candidate test targets.
- Do not add meaningless tests just to increase coverage.

## 5. Practical Guide

### 5.1 Gherkin Scenarios And Given/When/Then

Use Gherkin's Given/When/Then structure to express the behavior each test protects.

A clear interface lets readers understand what behavior to expect without reading the implementation. Likewise, a test's name and Gherkin scenario should explain what behavior it guarantees: under which conditions, after which action, and with what expected outcome.

Readers should not need to inspect production code, fixture definitions, or helper implementations to understand that contract. Use familiar feature terms to explain the behavior. Add descriptive text to section comments where the names and code alone do not make the contract clear.

- `Given`: Conditions and inputs that determine the outcome, such as cart contents, user permissions, or an API response
- `When`: The action or event whose behavior the test verifies
- `Then`: Expected results observable at the test boundary, such as rendered UI, user-visible state, and outgoing requests or events that form part of the contract
- `And`: An additional condition, action, or expected result that continues the preceding step. Use `// And` within the corresponding `Given`, `When`, or `Then` section.

Keep the scenario and its verification aligned:

- Keep scenario-defining conditions, the core action, and expected results visible in the test body. Helpers can handle shared infrastructure and repetitive setup when their names and explicit inputs make the important conditions clear.
- Use setup code to establish the stated `Given` conditions, execute the stated `When` action, and assert every stated `Then` result, including any `And` steps. A descriptive comment does not replace the corresponding setup or assertion.
- One test may verify multiple results of the same behavior. Combine multiple actions only when their sequence is itself the requirement.
- Describe only the outcomes the test actually verifies. A test with a mocked API response can verify how the UI handles that response, but cannot establish that the server enforces the rule.

Example:

```tsx
it('상품 수량을 변경하면 장바구니 총액이 재계산된다', async () => {
  // Given: 10,000원 상품 한 개가 장바구니에 있다.
  const product = { id: 1, name: 'T-Shirt', price: 10000 };

  render(Cart, {
    props: {
      initialItems: [{ ...product, quantity: 1 }],
    },
  });

  // When: 사용자가 상품 수량을 한 개 늘린다.
  const incrementButton = screen.getByRole('button', { name: /increase quantity/i });
  await userEvent.click(incrementButton);

  // Then: 장바구니 총액이 20,000원으로 표시된다.
  expect(screen.getByText(/total: 20,000 KRW/i)).toBeInTheDocument();
});
```

For different cases of the same rule, make the relevant inputs and expected results explicit. This example checks the coupon rule just below and at its minimum order amount:

```tsx
it.each([
  { total: 49999, expectedPayment: '49,999원' },
  { total: 50000, expectedPayment: '45,000원' },
])(
  '장바구니가 $total원일 때 쿠폰을 적용하면 결제 금액은 $expectedPayment이다',
  async ({ total, expectedPayment }) => {
    // Given: 장바구니에 total원인 상품 한 개가 있다.
    const initialItems = [{ id: 1, name: '상품', price: total, quantity: 1 }];

    // And: 최소 주문 금액이 50,000원인 5,000원 할인 쿠폰이 있다.
    const availableCoupons = [
      { code: 'SAVE5000', minimumOrderAmount: 50000, discountAmount: 5000 },
    ];

    render(Cart, {
      props: { initialItems, availableCoupons },
    });

    // When: 사용자가 쿠폰을 선택한다.
    await userEvent.selectOptions(screen.getByRole('combobox', { name: '쿠폰' }), 'SAVE5000');

    // And: 쿠폰 적용 버튼을 누른다.
    await userEvent.click(screen.getByRole('button', { name: '쿠폰 적용' }));

    // Then: 최소 주문 금액을 충족한 경우에만 할인된 결제 금액이 표시된다.
    expect(screen.getByText(`최종 결제 금액: ${expectedPayment}`)).toBeInTheDocument();
  },
);
```

### 5.2 Mocking Policy

Limit mocking to the external world that we do not control. Compared with unit tests that heavily mock internal collaborators, integration tests with real collaboration boundaries are more resilient to refactoring and more trustworthy from the user's perspective.

- Mock API calls at the network boundary with `MSW (Mock Service Worker)` instead of mocking server logic directly.
- Prefer testing the integrated state where parent and child components actually collaborate.

#### 5.2.1 Shared MSW Testing Standard

For MSW-based tests, do not repeat the same setup in every spec. Build tests on top of a shared test environment, then override only the conditions required for the scenario with `server.use(...)`.

- Do not default to fine-grained `vi.mock()` calls against internal composables or API modules.
- Write overrides so that they reveal only the condition the test is trying to validate.
- Prefer helpers with obvious intent over large and noisy fixture payloads.

#### 5.2.2 Shared Assumptions For Component Tests

Reuse established setup helpers for dependencies outside the test's concern. For example, query client, store, injectables, and browser storage are usually better provided by the default test environment than recreated in each spec.

Keep scenario-specific conditions and the execution flow visible in the test body. Extract helpers to make scenarios easier to understand, not just to remove repeated lines. A little duplication is preferable to an abstraction that hides those details.

- Create only the minimum fixture data required for the scenario, including enough fields to represent the edge case.
- Avoid helpers that create excessive data, accept many scenario-specific parameters, or build deep dependency chains.
- Share helpers across features when multiple features reuse them; keep feature-specific helpers near that feature.
- Use shared cleanup to reset application state and restore test overrides between tests.
- Do not repeat cleanup in individual specs when the test environment already handles it.

#### 5.2.3 Test Double Usage Standard

In black-box component tests, keep real rendering whenever possible.

- Do not increase test double usage just to make the test easier to write.
- Consider stubs, spies, or mocks only when unrelated side effects make the test unstable or too noisy.
- If a test double is necessary, its reason should be easy to explain.

### 5.3 Async UI Waiting Principles

In component tests, the synchronization point should not be "has the request finished?" It should be "has the DOM reached the state the user is supposed to see?"

- Wait for appearance with `findBy*`.
- Wait for disappearance with `waitForElementToBeRemoved`.
- Use `waitFor(() => expect(...))` only when `findBy*` or dedicated removal APIs cannot express the expectation cleanly.
- If `waitFor` is required, keep only one assertion inside the callback.

`findBy*`, `waitFor`, and `waitForElementToBeRemoved` do not wait forever. They fail if the condition is not satisfied within the default timeout. Increase the timeout only when the specific test needs it.

### 5.4 Handling Non-Deterministic Inputs

Move unstable values such as current time or randomness to the test boundary.

- Freeze or patch the current time when it is part of the scenario.
- Use fixed values when randomness, UUIDs, or current time appear in inputs or results.
- When timezone behavior matters, verify the relevant base timezone, UTC conversion, and epoch values explicitly.
- For ordering-sensitive tests, make timestamps, IDs, and other sort inputs explicit.

```ts
// Before
const getDeadline = () => {
  const now = new Date();
  return addDays(now, 7);
};

// After
const getDeadline = (baseDate = new Date()) => {
  return addDays(baseDate, 7);
};

it('마감일은 기준일로부터 7일 후다', () => {
  // Given
  const fixedDate = new Date('2024-01-01');

  // When
  const deadline = getDeadline(fixedDate);

  // Then
  expect(deadline).toEqual(new Date('2024-01-08'));
});
```

### 5.5 Assertions And Failure Cases

Good assertions make it clear what broke when the test fails.

Keep core actions and required assertions in a clear sequence. Do not use `if`, conditional expressions, or short-circuit operators to skip them based on actual results. A missing required UI element or response field should fail the test.

- Assert the important user-visible results and outgoing request or event payloads that form the contract.
- When only some fields are contractual, assert those fields explicitly. Use exact objects when the whole payload is the contract.
- For lists, verify length, order, and important item identities together when they are part of the requirement.
- Avoid checks that only prove a value exists, or only count mock calls without verifying the intended result or payload.
- Cover necessary failure, permission, and authentication cases through their observable frontend behavior, such as error feedback, restricted actions, and sign-in flows.

### 5.6 Snapshot Usage

Use snapshots for a focused rendered structure or payload when its full shape is a stable contract and the diff is small enough for reviewers to understand.

- Stabilize volatile values with matchers, fixed time, or fixed IDs.
- Update snapshots intentionally and review their changes in the same PR as the code change.
- Prefer explicit assertions when only a few fields or UI properties matter.
- Avoid snapshots that cover unrelated UI or fields, contain unstable ordering or volatile values, or produce too much output to interpret.

## 6. UI Verification And Accessibility

We avoid pixel-perfect visual regression tests by default. Screen structure and styles change often, and their maintenance cost is high. Instead, prioritize stable properties that users actually perceive and interact with.

### What To Validate

Write tests against attributes that are relatively stable from a user perspective, rather than against volatile implementation details.

### Query Priority

| Priority | Query | When To Use |
| --- | --- | --- |
| 1 | `getByRole` | Most semantic UI elements such as buttons, inputs, links, headings, and dialogs. Use with `name` whenever possible |
| 2 | `getByLabelText` | Form fields connected to labels |
| 3 | `getByDisplayValue` | When the current selected value is the best identifier |
| 4 | `getByText` | When visible text is the most meaningful query |
| 5 | `getByTestId` | Only when the other options are not practical |

### `data-testid` Usage Standard

Use `data-testid` only for elements that cannot be identified well through accessibility roles or similar user-facing semantics. Do not add `aria-label` only to make tests easier to query.

```vue
<button data-testid="submit-button">Submit</button>
```

```ts
screen.getByTestId('submit-button'); // BAD
screen.getByRole('button', { name: /submit/i }); // GOOD
```

```vue
<div data-testid="product-card-skeleton">...</div>
```

```ts
screen.getByTestId('product-card-skeleton');
```

For repeated UI, narrow by user-visible context with `within(...)` before falling back to `data-testid`.

```ts
const row = screen.getByRole('row', { name: /Samsung Electronics/i });
expect(within(row).getByText('72,000')).toBeInTheDocument();
```

### Prefer To Avoid

- CSS class names
- DOM structure itself
- Positional selectors such as `nth-child`
- Auto-generated or meaningless `id` values
- Text that is likely to change often

## 7. Review Checklist

Start with the test's purpose and contribution to the suite:

1. What important behavior does this test protect beyond the protection already provided by other tests?
2. Does it contain only the setup, actions, and assertions needed for that behavior?
3. Can a reader understand the contract from the test name and Given/When/Then scenario without opening production code or helper definitions?

Then check how the test provides that protection:

- Would this test actually fail if that behavior broke?
- Does it preserve useful collaboration between real components while keeping its concern focused?
- If the scope was narrowed, is the reason clear?
- Do the name and description accurately reflect what the test exercises and observes?
- Are the scenario-defining conditions, core action, and expected results clear in the test body's `Given / When / Then` flow?
- Does the code establish the stated conditions, execute the stated action, and assert every stated result?
- Can a branch skip a required action or assertion and still let the test pass?
- Does the test verify externally observable behavior instead of internal implementation?
- Do test doubles replace external or unstable dependencies without replacing the behavior under test?
- Can query caches, stores, browser storage, mocks, or environment overrides leak between tests?
- Are time, UUIDs, randomness, and ordering deterministic?
- Are necessary failure, permission, and authentication cases covered?
- If a snapshot is used, can reviewers understand the diff?
- If test doubles keep increasing, should the test move to a larger scope?
- If setup helpers hide too many conditions, should those conditions move back into the test body?
