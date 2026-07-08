# LESSONS.md

Each rule in `AGENTS.md` traces to a specific failure here. When a new failure happens twice in a week, propose a rule and add the lesson to this file in the same PR.

This file isn't loaded into every agent session — it's reference material the team consults when triaging or onboarding. Keep it honest: name the date, the failure, the cost, the rule that prevents the next occurrence.

---

## L01 — Use slice tests for controllers

**Date:** 2026-02-14
**Failure:** A new engineer wrote `@SpringBootTest` for a single-controller test that needed only `MockMvc` + a mocked service. Test class boot time went from 1.8s to 12.4s. The whole suite went from 18s to 47s; CI bill nearly doubled before anyone noticed.
**Rule:** Use `@WebMvcTest(SomeController.class)` for controller tests. Full `@SpringBootTest` is reserved for true end-to-end.
**Where:** `AGENTS.md` Rules.

## L02 — readOnly on query services

**Date:** 2026-02-21
**Failure:** A query-only service method ran inside a default `@Transactional` (read-write, dirty-checking enabled). JPA flushed managed entities mid-read, hitting an unexpected unique constraint on a partially-updated row. Took 90 minutes to reproduce locally because the test didn't have the writer racing.
**Rule:** Annotate query services and methods with `@Transactional(readOnly = true)`. Save the writeable transaction for actual mutations.
**Where:** `AGENTS.md` Rules.

## L03 — Never repo from controller

**Date:** 2026-03-04
**Failure:** A `@RestController` autowired `CourseRepository` and called it directly "to save one line." Transaction scope was lost; a downstream eager-fetch threw `LazyInitializationException` two weeks later in production. The bug only surfaced under specific session timing.
**Rule:** Controllers depend on services. Services depend on repositories. No shortcuts.
**Where:** `AGENTS.md` Rules + `.claude/rules/api-handlers.md`.

## L04 — Bean Validation messages in messages.properties

**Date:** 2026-03-11
**Failure:** A reviewer translated user-facing error messages by editing `@NotNull(message = "..."")` inline across 14 annotations. PR reverted; the i18n team has owned `messages.properties` for this purpose since 2024.
**Rule:** Validation message keys go in annotations (`message = "{course.title.required}"`), localized strings in `messages.properties`.
**Where:** `AGENTS.md` Rules.

## L05 — BigDecimal for money

**Date:** 2026-03-19
**Failure:** A discount calculation used `double` arithmetic. ₹2999 × 0.85 returned ₹2549.149999... and the rendered receipt showed ₹2549.14 — a one-paisa undercharge across 11,000 orders before anyone caught it. Refund accounting took two weeks.
**Rule:** `BigDecimal` for any monetary field. Setters take `String`, not `double`.
**Where:** `AGENTS.md` Rules.

## L06 — Optional, not null

**Date:** 2026-04-02
**Failure:** A repository method returned `null` for "no row found." Six callers forgot to check. Two of them shipped NPEs to production before the linter caught it elsewhere.
**Rule:** Repository methods that may not find a row return `Optional<T>`. Never `null`.
**Where:** `AGENTS.md` Rules.

## L07 — Migrations are append-only

**Date:** 2026-04-15
**Failure:** An engineer edited a Flyway migration that was already merged to main to "fix a typo in the column name." Two devs who'd run the original locally got Flyway checksum mismatches on the next `bootRun`; one nuked their dev DB to recover.
**Rule:** Once a migration is merged, never edit it. Add a new migration to correct.
**Where:** `AGENTS.md` Rules.

## L08 — Use application-test.yml

**Date:** 2026-04-22
**Failure:** A test class used `@TestPropertySource(properties = {"spring.datasource.url=..."})` to override config inline. Three other test classes had the same boilerplate. When the canonical test config changed in `application-test.yml`, the inline overrides silently ignored the update.
**Rule:** Test config lives in `application-test.yml`. Don't override inline; if a test needs a unique property, add a profile in `application-test.yml` and activate it.
**Where:** `AGENTS.md` Rules.

---

## How to add a lesson

When the same failure happens twice in a week:

1. Open this file. Add an `L<seq+1>` entry with date, failure description, cost, and the rule that would have prevented it.
2. Open `AGENTS.md` Rules section. Add the one-line rule, ending with `(L<seq+1>)`.
3. If the rule has a deterministic check (lint rule, hook, test), open `.claude/hooks/` and add it there as well. Belt and suspenders.
4. Same PR for all three. No partial commits.
