# Learned Rules and Preferences

This file contains learned workflows, preferences, and standard practices that have been documented during development. These rules are always kept in context and should be referenced when relevant.

---

## Pytest Test Marker Strategy for CI Pipelines

**Context:** When using pytest with multi-stage CI pipelines, test categorization through markers is critical for proper test distribution. Tests without explicit markers may rely on fixture-based auto-marking, which can be unpredictable.

**Best Practices:**

1. **Explicit Test Markers Required:**
   - All test files should have explicit markers (`@pytest.mark.unit`, `@pytest.mark.api`, `@pytest.mark.integration`, `@pytest.mark.database`, etc.)
   - Avoid relying solely on auto-marking from `conftest.py` based on fixture usage
   - Benefits: Makes test categorization explicit and predictable, easier to understand which CI stage runs which tests

2. **Test Marker Precedence:**
   - Integration/database markers typically take precedence over unit/api markers
   - Tests with conflicting markers (e.g., `@pytest.mark.api` + `@pytest.mark.database`) should be categorized by their most specific requirement
   - Tests with `@pytest.mark.unit` + `@pytest.mark.integration` should use only `integration` marker

3. **CI Test Distribution Strategy:**
   - **Unit Tests Stage:** Run `pytest -m 'not (integration or database)'` - Fast tests without external dependencies
   - **API Tests Stage:** Run `pytest -m 'api and not database'` - API endpoint tests without database access
   - **Integration Tests Stage:** Run `pytest -m '(integration or database)'` - Tests requiring database or external services
   - API tests with database requirements should be split across stages (non-database in API stage, database in integration stage)

4. **Slow Tests Management:**
   - Tests marked `@pytest.mark.slow` should be reviewed for CI compatibility
   - If too slow for CI, mark with `@pytest.mark.skip_ci` to exclude from CI pipeline
   - Ensure CI has appropriate timeout settings for slow tests that are included

5. **Marker Validation:**
   - When creating new tests, verify they have appropriate markers
   - Avoid conflicting marker combinations (e.g., `unit` + `integration` should be just `integration`)
   - Ensure all test files are covered by at least one CI stage

6. **Auto-Marking Considerations:**
   - If using auto-marking in `conftest.py` based on fixture usage, document the rules clearly
   - Common auto-marking patterns:
     - Tests using database fixtures → Auto-marked `integration`
     - Tests using client fixtures → Auto-marked `integration`
   - While auto-marking works, explicit markers are preferred for clarity and predictability

**General Guidelines:**
- Review all test files for explicit markers regularly
- Document marker strategy and precedence rules in project documentation
- Consider adding CI validation to ensure all tests have appropriate markers
- Keep marker usage consistent across the codebase

**CRITICAL RULE - Migration and Integration Tests:**
- **ALL migration tests MUST be run with `-m integration` marker**
- **ALL integration tests MUST be run with `-m integration` marker**
- Migration tests: `pytest tests/test_migration_comprehensive.py -v -m integration`
- Integration tests: `pytest tests/integration/test_migrations.py -v -m integration`
- Database names:
  - Local dev DB: `egregor_v3`
  - Test DB: `egregor_v3_test`
- **NEVER run migration or integration tests without the `-m integration` marker**

**CRITICAL RULE - Using Poe Commands for Tests:**
- **ALL tests MUST be run through poe commands, NOT direct pytest**
- **NEVER run `pytest` directly - always use `poe` commands**
- Poe commands support filtering with `-k` argument (pytest keyword filtering)
- Examples:
  - Run specific test: `poe test -k "test_clean_database_migration"`
  - Run migration tests: `poe test-migrations -k "branding"`
  - Run integration tests: `poe test-integration -k "test_migration_history"`
- Available poe test commands:
  - `poe test` - Fast tests (unit + API, no DB)
  - `poe test-unit` - Unit tests only
  - `poe test-api` - API tests only
  - `poe test-integration` - Integration tests (requires DB)
  - `poe test-migrations` - Migration tests (requires DB)
  - `poe test-all` - All tests in two stages
- **Use `-k` argument for test filtering, NOT direct file paths or markers**
- **Benefits:** Consistent environment, proper database setup, correct markers applied

---

## Hybrid Testing Strategy: Mocked vs Integration Tests

**Context:** When writing tests for features that interact with databases, use a hybrid approach that balances speed and thoroughness by separating mocked unit tests from integration tests.

**Philosophy:** Mock what can be mocked, test what needs real infrastructure.

**Test Structure:**

1. **Mocked Tests (No DB Required)**
   - Use `@pytest.mark.asyncio` but NOT `@pytest.mark.integration`
   - Use `AsyncMock` and `MagicMock` for database sessions
   - Test business logic and service methods
   - Fast execution (~0.25s), no database setup
   - Can run in CI/CD without database
   - Example: `TestAuditServiceMocked` class

2. **Integration Tests (DB Required)**
   - Use `@pytest.mark.asyncio` AND `@pytest.mark.integration`
   - Use real `db_session` fixture (real AsyncSession)
   - Test DB-specific scenarios only:
     - Actual database writes/reads
     - Tenant isolation at DB query level
     - Integrity hash chains with real DB queries
     - Database constraints (CHECK, foreign keys)
   - Slower execution (~7-12s), requires Docker Compose
   - Must be explicitly run with `--integration` flag
   - Example: `TestAuditServiceIntegration` class

3. **Unit Tests (Pure Logic)**
   - No async, no DB fixtures
   - Test pure Python logic (hash calculations, enum values)
   - Fastest tests (~0.01s each)
   - Example: `TestAuditModelMethods` class

**Test Naming Conventions:**
- Mocked: `test_create_audit_log_basic_mocked`
- Integration: `test_create_and_retrieve_audit_log_db`
- Unit: `test_integrity_hash_calculation_consistency`

**Running Tests:**
- Default: `pytest tests/test_file.py` - Runs mocked + unit tests only (integration skipped)
- Integration: `pytest tests/test_file.py --integration` - Runs integration tests only
- All: Run separately or use `-k` filter

**Best Practices:**
- Mark integration tests explicitly with `@pytest.mark.integration`
- Use descriptive test names indicating test type
- Document what each test verifies (especially integration tests)
- Keep integration tests minimal - only test DB-specific scenarios
- Don't duplicate mocked test coverage in integration tests
- Use appropriate fixtures: `mock_db_session` for mocked, `db_session` for integration

**When to Use Each:**
- **Mocked Tests For:** Business logic, service methods, model methods, API auth checks, fast feedback
- **Integration Tests For:** DB constraints, tenant isolation, SQL correctness, integrity chains, multi-tenant isolation
- **Unit Tests For:** Pure Python logic, enum values, hash calculations, data transformations

**Benefits:**
- Fast development cycle with mocked tests
- CI/CD flexibility (can run without database)
- Thorough coverage with integration tests
- Clear separation of concerns
- Easy to understand what needs DB vs what doesn't

**Documentation:** See `docs/TESTING_APPROACH.md` for detailed examples and guidelines.
