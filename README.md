# Monolog

Monolog is a PSR-3 compatible PHP logging library that sends log records to files, sockets, inboxes, databases and many web services.

This README contains a concise developer-focused quickstart (English) followed by a Spanish translation.

---

## What this project does

- Implements PSR-3 logging with a flexible pipeline of handlers, formatters and processors.
- Supports many integrations (files, syslog, Elasticsearch, Graylog, AWS, MongoDB, AMQP, etc.) via handlers in `src/Monolog/Handler`.
- Designed for performance, composability and backwards-compatibility across major versions.

Key runtime pieces: `Logger` (`src/Monolog/Logger.php`), `LogRecord` (`src/Monolog/LogRecord.php`) and `Level` (`src/Monolog/Level.php`).

---

## Why Monolog is useful

# Monolog

Monolog is a PSR-3 compatible PHP logging library that sends log records to files, sockets, inboxes, databases and many web services.

This README is developer-focused and includes a concise quickstart, usage patterns, and contribution checklist (English followed by Spanish summary).

Badges

- (Add CI / Packagist badges here if desired.)

---

## What this project does

- Implements PSR-3 logging with a flexible pipeline of handlers, formatters and processors.
- Ships many integrations via `src/Monolog/Handler` and formatters in `src/Monolog/Formatter`.
- Maintains backward compatibility across major releases; API surface changes are gated by `Logger::API`.

Key runtime pieces: `Logger` (`src/Monolog/Logger.php`), `LogRecord` (`src/Monolog/LogRecord.php`) and `Level` (`src/Monolog/Level.php`).

---

## Why Monolog is useful

- Centralizes logging concerns behind a consistent PSR-3 API.
- Lazy processor execution — processors run only when a handler will handle the record, saving work for high-volume apps.
- Extensible: add custom handlers, formatters or processors to adapt to new backends.

---

## Quickstart (Developer)

Prerequisites:

- PHP >= 8.1
- Composer

Install dependencies locally:

```bash
composer install
```

Install the package into an application:

```bash
composer require monolog/monolog
```

Run the full test suite:

```bash
composer test
```

Run static analysis (phpstan):

```bash
composer phpstan
```

Run a single test file:

```bash
php vendor/bin/phpunit tests/Monolog/Handler/YourHandlerTest.php
```

Minimal usage example:

```php
<?php
use Monolog\Logger;
use Monolog\Handler\StreamHandler;

$log = new Logger('app');
$log->pushHandler(new StreamHandler('php://stdout', Logger::DEBUG));
$log->info('Hello world');
```

Example: adding a processor and a formatter

```php
use Monolog\Formatter\LineFormatter;
use Monolog\Processor\MemoryUsageProcessor;

$handler = new StreamHandler('php://stdout', Logger::INFO);
$handler->setFormatter(new LineFormatter(null, null, true, true));
$log->pushHandler($handler);
$log->pushProcessor(new MemoryUsageProcessor());
```

Where to look next in the source:

- `src/Monolog/Logger.php` — record creation, lazy processor execution and handler bubbling
- `src/Monolog/Handler/` — concrete handlers and `AbstractProcessingHandler`
- `src/Monolog/Formatter/` — formatters (see `LineFormatter` for canonical example)

---

## Development conventions & patterns

- Minimum PHP 8.1; code uses typed properties, union types, Fibers and `WeakMap`.
- Prefer `Level` objects/values over legacy integer constants (see `Level::VALUES`).
- Handlers typically extend `AbstractHandler` or `AbstractProcessingHandler` and implement `isHandling()` and `write()`.
- Processors are callables or `ProcessorInterface` implementations; they are applied lazily only when a handler will handle the record.
- Tests mirror PSR-4 under `tests/Monolog` and the dev autoload maps `Monolog\` to `tests/Monolog`.

Best practices when editing the codebase:

- Preserve public API behavior unless intentionally bumping `Logger::API`.
- Add unit tests in `tests/Monolog` for behavioral changes.
- If you touch optional integrations (Elasticsearch, AWS, MongoDB, AMQP), mock external clients in tests or add them to `require-dev`.

---

## Examples: custom handler skeleton

Create a handler by extending `AbstractProcessingHandler` and implementing `write()`:

```php
namespace Monolog\Handler;

use Monolog\LogRecord;

class MyHandler extends AbstractProcessingHandler
{
	protected function write(LogRecord $record): void
	{
		// send $record->message and $record->context to your backend
	}
}
```

---

## Testing & static analysis

- Run tests: `composer test` (uses `phpunit`).
- Run a single test file: `php vendor/bin/phpunit tests/Monolog/Path/ToTest.php`.
- Static analysis: `composer phpstan` (configuration in `phpstan.neon.dist` and baselines in `phpstan-baseline*.neon`).

CI note: this repository includes a `composer test` and `composer phpstan` script — ensure CI runs both before merging PRs.

---

## Contributing checklist (PRs)

- Write or update unit tests for new behavior under `tests/Monolog`.
- Run `composer test` and `composer phpstan` locally.
- Keep changes focused and preserve public API unless releasing a breaking change (bump `Logger::API`).
- If adding optional integrations, document new suggested packages in `composer.json`'s `suggest` block.

---

## License

Monolog is licensed under the MIT license. See the `LICENSE` file.

---

## Where to get help & documentation

- Usage docs: [doc/01-usage.md](doc/01-usage.md)
- Handlers/formatters/processors guide: [doc/02-handlers-formatters-processors.md](doc/02-handlers-formatters-processors.md)
- Message structure: [message-structure.md](message-structure.md)

Open an issue if you need help or want to propose a change.

---

## Who maintains & contributing

- Maintainer listed in `composer.json` (see `authors`).
- Please follow repository contribution guidelines (look for `CONTRIBUTING.md` or open an issue to discuss larger changes).

Before submitting PRs:

- Run `composer test` and `composer phpstan`.
- Add unit tests under `tests/Monolog` for behavioral changes.

---

## README — Español (resumen)

Monolog es una biblioteca de logging compatible con PSR-3 para PHP que envía registros a ficheros, sockets, bases de datos y servicios.

Requisitos: PHP >= 8.1, Composer.

Instalación y pruebas:

```bash
composer install
composer test
composer phpstan
```

Ejemplo mínimo:

```php
use Monolog\Logger;
use Monolog\Handler\StreamHandler;

$log = new Logger('app');
$log->pushHandler(new StreamHandler('php://stdout', Logger::DEBUG));
$log->info('Hola mundo');
```

Documentación adicional: [doc/01-usage.md](doc/01-usage.md)

Para contribuir, ejecute `composer test` y agregue tests en `tests/Monolog`.
