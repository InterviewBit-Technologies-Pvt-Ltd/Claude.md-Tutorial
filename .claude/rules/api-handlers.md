---
paths:
  - "src/main/java/co/scaler/demo/api/**/*.java"
---

# API handler rules

Loaded only when Claude touches files under `src/main/java/co/scaler/demo/api/`. Keeps the rules close to where they apply without bloating the always-loaded `AGENTS.md`.

## When writing or editing a controller

- Validate at the controller method using `@Valid` and `@RequestBody`. Don't push validation into the service layer — the service trusts its inputs.
- Map exceptions in `ApiExceptionHandler` (`@ControllerAdvice`), not inline with `try/catch` in the controller method. The exception → response mapping should be one place, not N.
- Response DTOs are records. Never expose JPA entities directly — eager-fetch lazy fields and you create a `LazyInitializationException`; expose them and you leak schema.
- Status code semantics:
    - `GET` → `200 OK`
    - `POST` (create) → `201 Created` with `Location` header pointing to the new resource
    - `PUT` (update) → `200 OK`
    - `DELETE` → `204 No Content`
- `@PreAuthorize` goes on the controller method, not the service. Auth is a boundary concern.

## When proposing a new endpoint

Before writing the implementation, sketch:

1. The controller method signature (path, verb, request body type, response type).
2. The request and response records.
3. The service method it will call.

Show the sketch first; implement after the shape is agreed.

## Naming

- Path uses kebab-case nouns: `/api/v1/courses/{id}/enrollments`.
- Methods named by intent: `listCourses`, `getCourse`, `createCourse`, `updateCourse`, `archiveCourse`. Not `getCoursesAll`, not `doCourse`.
- Request DTOs end in `Request`. Responses end in `Response`. Never `Dto`.
