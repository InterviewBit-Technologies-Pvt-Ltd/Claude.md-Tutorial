# AGENTS.md

Tool-agnostic guidance for AI coding agents on the Scaler Demo Project. This is the spine; Claude Code reads it via `CLAUDE.md`, Cursor/Codex/Aider read it directly.

## Commands

- **Build**: `./gradlew build -x test` (build without running tests)
- **Test (all)**: `./gradlew test` (full suite is ~90s — prefer the targeted forms while iterating)
- **Test (single class)**: `./gradlew test --tests CourseControllerTest`
- **Test (single method)**: `./gradlew test --tests "CourseControllerTest.shouldReturn404WhenCourseMissing"`
- **Format**: `./gradlew spotlessApply`
- **Lint check**: `./gradlew spotlessCheck`
- **Dev server**: `./gradlew bootRun --args='--spring.profiles.active=dev'` (port 8080)
- **DB migrate**: `./gradlew flywayMigrate -Dflyway.configFiles=flyway-local.conf`

## Codebase structure

- `src/main/java/co/scaler/demo/api/` — REST controllers. Thin: validate input, delegate to service, map response.
- `src/main/java/co/scaler/demo/service/` — business logic. Spring stereotypes (`@Service`) plus `@Transactional` only.
- `src/main/java/co/scaler/demo/repository/` — Spring Data JPA interfaces. Query-method names preferred over `@Query` strings.
- `src/main/java/co/scaler/demo/domain/` — JPA entities, value objects, domain events.
- `src/main/java/co/scaler/demo/config/` — `@Configuration` classes only.
- `src/test/java/co/scaler/demo/` — mirrors `main/`. Slice tests sit next to the slice they cover.
- `src/main/resources/db/migration/` — Flyway SQL migrations (`V<seq>__<snake_case>.sql`).

## Architecture notes

- Hexagonal-lite: controllers depend on service interfaces; services depend on repository interfaces. No service-to-service direct calls — use Spring application events for cross-aggregate work.
- `spring.jpa.open-in-view=false` is enforced. Lazy fields accessed outside `@Transactional` will throw `LazyInitializationException`. Fetch eagerly or join inside the transaction.
- Flyway owns the schema. Hibernate `ddl-auto=validate`. Never `update` or `create`.

## Conventions

- Java 21 records for DTOs. Entities are classes (Hibernate needs them), records where the aggregate boundary allows.
- Validation lives at the controller boundary with `@Valid` + Bean Validation. Services assume inputs are pre-validated.
- One controller per resource. Methods named by intent: `createCourse`, `getCourse`, `listCourses`.
- Constructor injection only. No `@Autowired` on fields.
- Test slice annotations (`@WebMvcTest`, `@DataJpaTest`) preferred over full `@SpringBootTest`. Full context only for true end-to-end.
- 4-space indentation. `final` on parameters where it tightens intent.
- No Lombok `@Builder`. Records or static factory methods.

## Rules

Each line below traces to a specific failure in `LESSONS.md`. Don't add to this section without naming the failure.

- Use `@WebMvcTest(CourseController.class)` for controller tests, not `@SpringBootTest`. (L01)
- Query services are annotated `@Transactional(readOnly = true)`. (L02)
- Never call a repository from a controller. Always go through a service. (L03)
- Bean Validation messages live in `messages.properties`. Don't hardcode them in `@NotNull(message=...)`. (L04)
- Use `BigDecimal` for money. Never `double` or `float`. (L05)
- Repository methods return `Optional<T>`, never `null`. (L06)
- Flyway migrations are append-only. Once merged, never edit. Add a new `V<seq+1>__...sql` to correct. (L07)
- Use `application-test.yml` for test config. Don't override properties inline with `@TestPropertySource`. (L08)

If you hit a new failure that isn't here, propose a one-line rule + a corresponding `LESSONS.md` entry in the same PR. Don't add stylistic preferences — only failure-traceable rules.

## What's externalised

- **Path-scoped rules**: `.claude/rules/api-handlers.md` (scoped to `src/main/java/**/api/**`).
- **Skills**: `.claude/skills/spring-test-slice-chooser/` (when to use which test annotation).
- **Hooks**: `.claude/hooks/` (currently empty). Promote a rule here when it must hold every time and a deterministic check is feasible.
- **Subagents**: `.claude/agents/` (currently empty). Use for tasks that read many files (codebase-wide refactors, large log analysis) so the main session stays clean.
