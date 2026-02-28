# 04 - Testing

## No Mocks

The stance: never mock. Test actual implementations against real dependencies.

If you can't test something without mocking, that's a design problem - your boundaries are in the wrong place. Fix the design, don't paper over it with `jest.mock()`.

## Why This Matters for Agentic Coding

AI defaults to mock-heavy tests. Every model has been trained on millions of test files full of `jest.mock`, `sinon.stub`, and `unittest.mock`. Left unconstrained, you get:

```ts
jest.mock("$/lib/db")
jest.mock("$/lib/auth")
jest.mock("$/lib/email")

test("user signup", () => {
  // mocked db, mocked auth, mocked email
  // testing literally nothing
})
```

Coverage goes up. Confidence doesn't. This is coverage theater.

The model will generate these tests eagerly because they're easy to write, always pass, and look productive. That's exactly why they're dangerous.

> **🌶️ Hot take:** Code coverage is a vanity metric. 90% coverage means nothing if your app breaks the moment all the pieces run together. Unit tests with mocks tell you each piece works in isolation - great, but your users don't run your app in isolation. What matters is whether the system works end-to-end with real data, real dependencies, and real failure modes. If your test suite is green and your app is broken, your test suite is lying to you.

## What To Do Instead

Test real behavior with real dependencies:

* **Databases** - Test against the real database engine you use in production. Use testcontainers or a dedicated test instance with proper setup and teardown. If your tests break during cleanup, good - that's a problem you'd rather find now than in production. Stop treating your database like a sacred artifact only gods can touch. In the real world, it's under constant pressure from users trying to break things.
* **API endpoints** - Spin up the actual server. Hit real routes. Assert real responses.
* **Side effects** - If the function sends an email, test against a real SMTP test server, not a mock.

```ts
test("user signup flow", async () => {
  const db = await createTestDb()      // real database instance
  const app = createApp({ db })        // real app instance

  const res = await app.request("/signup", {
    method: "POST",
    body: JSON.stringify({ email: "test@test.com", password: "secure123" }),
  })

  expect(res.status).toBe(201)

  // verify the user actually exists in the real database
  const user = await db.query("SELECT * FROM users WHERE email = ?", ["test@test.com"])
  expect(user).toBeDefined()
})
```

Slower than mocks. Tests something real.

## Don't Duplicate Logic Into Tests

Another AI habit - reimplementing business logic inside the test to assert against:

```ts
// Bad: duplicating the pricing calculation in the test
test("calculates price", () => {
  const price = 100
  const tax = price * 0.21       // duplicated logic
  const total = price + tax      // duplicated logic
  expect(calc(100)).toBe(total)  // testing nothing
})
```

```ts
// Good: testing behavior against known values
test("calculates price with tax", () => {
  expect(calc(100)).toBe(121)
})
```

Test behavior, not implementation.

## Package-Level Tests

Prefer tests scoped to a single package over workspace-level integration tests.

If a test requires importing from multiple packages, it probably belongs in its own package. This keeps test scope small and helps the AI - less context to hold, better output.

```text
packages/
  auth/
    src/
    tests/          <-- tests for auth only
  billing/
    src/
    tests/          <-- tests for billing only
  integration/
    tests/          <-- tests that cross package boundaries
```

## Prompting for Good Tests

Add this to your AGENTS.md (see [Chapter 02](02-project-setup.md)):

```markdown
- Avoid mocks as much as possible
- Test actual implementation, do not duplicate logic into tests
- Prefer package-level tests over workspace-level tests
```

Then when asking for tests, be explicit:

```text
Write integration tests for the auth flow.
No mocks. Real SQLite database. Test signup -> verify -> login.
```

Without that instruction, the model defaults to what it's seen the most: mocked unit tests.
