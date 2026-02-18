<!-- .github/copilot-instructions.md - guidance for AI coding agents -->
# Copilot / AI agent instructions for Monolog

This file gives concise, repo-specific guidance so an AI coding agent can be productive immediately.

1. Big picture
- Purpose: Monolog is a PSR-3 compatible PHP logging library (library code lives under `src/Monolog`).
- Core runtime pieces: `Logger` (`src/Monolog/Logger.php`), `LogRecord` (`src/Monolog/LogRecord.php`), and `Level` (`src/Monolog/Level.php`).
- Major components: `Handler/` (write/deliver records), `Formatter/` (format record content), `Processor/` (mutate records), `ErrorHandler.php`, `Registry.php`.
- Handler flow: processors are applied lazily then the handler stack is iterated; see `Logger::addRecord()` for exact flow and bubbling semantics.

2. Useful files to read first
- `src/Monolog/Logger.php` — how records are created, processed and dispatched.
- `src/Monolog/Handler/AbstractProcessingHandler.php` — common handler pattern and `write()` contract.
- `src/Monolog/Formatter/LineFormatter.php` — canonical formatter example.
- `composer.json` — PHP requirement (`php >= 8.1`), dev dependencies and `scripts` (`composer test`, `composer phpstan`).
- `phpunit.xml.dist` and `phpstan.neon.dist` — test and static analysis configuration.

3. Development workflow (commands)
- Install deps: `composer install`
- Run unit tests: `composer test` (runs `php vendor/bin/phpunit`)
- Run a single test file: `php vendor/bin/phpunit tests/Monolog/Handler/YourHandlerTest.php`
- Static analysis: `composer phpstan` (runs `php vendor/bin/phpstan analyse`) — baseline files present (`phpstan-baseline*.neon`).

4. Project-specific conventions
- Minimum PHP: 8.1 — code uses typed properties, union types, Fibers, WeakMap and `declare(strict_types=1)`.
- Prefer `Level` objects/values over legacy integer constants (see `Level::VALUES` and usage in `Logger`).
- Handlers usually extend `AbstractHandler` or `AbstractProcessingHandler`. When adding a handler, implement `isHandling()` and `write()` (or `handle()` via the abstract base).
- Formatters are separate classes in `Formatter/` and are often attached to handlers via `FormattableHandlerInterface`/`FormattableHandlerTrait`.
- Tests mirror PSR-4 namespace under `tests/Monolog` and use the same class names as sources.

5. Editing guidance for agents
- When changing public API surface (classes, methods, constants) preserve backwards compatibility; the project exposes an `API` constant in `Logger` to signal breaking changes.
- Add unit tests for behavior changes in `tests/Monolog/...` and run `composer test` locally.
- If you modify or add code that references optional integrations (Elasticsearch, AWS, MongoDB, AMQP, etc.), update `require-dev` or mock the external client in tests — see `composer.json` for suggested integrations.

6. Static analysis and baselines
- `phpstan.neon.dist` is configured; baselines exist (`phpstan-baseline.neon`, `phpstan-baseline-8.2.neon`). Use `composer phpstan` to run checks. Only change baselines when intentionally updating analysis expectations.

7. Patterns & anti-patterns seen in repo (examples)
- Lazy record initialization: processors are only executed when a handler indicates it will handle the record — see the `if (!$handler->isHandling($record)) { continue; }` logic in `Logger::addRecord()`.
- Avoid changing low-level record shape (fields on `LogRecord`) — many formatters/handlers assume `datetime`, `channel`, `level`, `message`, `context`, `extra` exist.

8. Where to add work and tests
- Source: `src/Monolog/...`
- Tests: `tests/Monolog/...` (dev autoload maps `Monolog\` to `tests/Monolog` in `composer.json`)

9. When in doubt
- Read `Logger::addRecord()` and the relevant abstract handler/formatter to match existing behavior.
- Run `composer test` and `composer phpstan` before creating PRs.

10. Ask the repo owner if:
- You need to change public API behavior (tag breaking change) or update phpstan baseline intentionally.

---
If anything here is unclear or you want additional examples (e.g., a handler + test scaffolding template), tell me which area to expand.
