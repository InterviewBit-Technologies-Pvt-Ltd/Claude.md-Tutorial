---
name: spring-test-slice-chooser
description: Picks the smallest Spring Boot test annotation that exercises what you're trying to test. Invoke when adding or revising a test and you're unsure whether @WebMvcTest, @DataJpaTest, @SpringBootTest, or plain JUnit is the right scope.
---

# spring-test-slice-chooser

Lazy expertise: most sessions don't need this. When they do — when someone is about to write a test and reaches for `@SpringBootTest` reflexively — they should consult this skill instead.

## The decision tree

**What are you testing?**

- **A controller's HTTP layer** (status codes, JSON shape, validation, exception mapping)
  → `@WebMvcTest(YourController.class)` + `MockMvc` + `@MockBean` for the service.
  → ~1.5s boot. No DB, no JPA.

- **A Spring Data JPA repository** (custom queries, native SQL, derived method names)
  → `@DataJpaTest` + `TestEntityManager` + Testcontainers PostgreSQL (extend `AbstractRepositoryTest`).
  → ~3s boot. No web layer.

- **A service that touches multiple repositories** (transactional flows, cross-aggregate logic)
  → `@SpringBootTest(classes = ServiceTestConfig.class)` with a focused inner `@Configuration` — not the full app context.
  → ~4s boot. Customize what you load.

- **A full request → DB cycle** (true integration: HTTP in, side effects out)
  → `@SpringBootTest(webEnvironment = RANDOM_PORT)` + `TestRestTemplate` or `WebTestClient`.
  → ~8s boot. The slowest. Use sparingly — one happy path + one critical edge case per resource is usually enough.

- **Pure logic** (a value object, a calculator, a domain method that doesn't touch Spring)
  → Plain JUnit 5. No annotation. No Spring at all.
  → <0.1s. Always the right choice when it applies.

## What not to do

- **Don't use `@SpringBootTest` for a single controller test.** That's `LESSONS.md` entry L01 — the test suite ballooned from 18s to 47s because someone defaulted to the full context.
- **Don't use `@WebMvcTest` if you need actual JPA.** It doesn't load the persistence layer. The test will compile and fail at runtime.
- **Don't mix slices in one test class.** If you find yourself wanting `@WebMvcTest` *and* `@DataJpaTest` on the same class, you're really writing an integration test — use `@SpringBootTest`.

## The rule

When in doubt, start with the smallest annotation that compiles. Add scope only when a test actually fails for lack of context. Slow tests are a tax the whole team pays every CI run; choose the slice with that cost in mind.
