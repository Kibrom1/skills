# Java / Spring Boot Review Heuristics
*Applies to any Java/Kotlin Spring Boot projects in this workspace*

> This reference is a condensed version of the team's full checklist at:
> `AI/code-review-checklist.md` — read that file for the complete severity-tagged checklist.

## Correctness
- [ ] `@Transactional` not applied to `private` methods (Spring proxy limitation) 🔴
- [ ] Nullable fields handled safely — no unchecked `.get()` on `Optional`, no `!!` in Kotlin without justification
- [ ] Entity cascade types are intentional — avoid `CascadeType.ALL` without understanding the implications
- [ ] HTTP status codes semantically correct (`201` for POST creates, `404` for missing resources)
- [ ] DTOs validated with `@Valid` / `@NotNull` / `@NotBlank` on request bodies 🔴

## Security
- [ ] No secrets hardcoded — use `@Value` from `application.yml` or environment variables 🔴
- [ ] No string concatenation in JPQL/native queries — use parameterized queries 🔴
- [ ] New endpoints protected by appropriate `@PreAuthorize` / Spring Security rules 🔴
- [ ] No PII or tokens logged even at DEBUG level 🔴
- [ ] DTOs used to receive user input — not entities directly (mass assignment) 🟡

## Performance
- [ ] N+1 queries eliminated — lazy-loaded associations in loops use `JOIN FETCH` or eager loading 🔴
- [ ] Large result sets paginated — no unbounded `findAll()` on large tables 🔴
- [ ] Batch operations used for bulk inserts/updates — not a loop of `save()` 🟡
- [ ] Indexes exist for new columns used in `WHERE`, `ORDER BY`, or `JOIN` 🟡

## Testing
- [ ] Controller layer tested via `@WebMvcTest` or integration tests 🟡
- [ ] Repository queries tested with `@DataJpaTest` for non-trivial queries 🟡
- [ ] Tests are deterministic — no timing dependencies or shared mutable test state

## Maintainability
- [ ] Layered architecture respected: Controller → Service → Repository (no business logic in controllers)
- [ ] New config externalized to `application.yml` / environment variables
- [ ] Kotlin-idiomatic code used where applicable (`data class`, `sealed class`, `?.let`) 🔵

See `AI/code-review-checklist.md` for the complete checklist with all items and severity tags.
