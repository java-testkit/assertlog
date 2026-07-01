# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

AssertLog is a Java library for unit-testing logging. It intercepts log output during tests and provides assertion methods to verify what was (or was not) logged.

Group ID: `com.codeborne.assertlog` | Current version: `0.2-SNAPSHOT`

## Commands

```bash
./gradlew test                             # run all tests (default task also runs clean)
./gradlew clean test                       # clean build then test (what CI runs)
./gradlew :assertlog-log4j-junit4:test --tests "org.assertlog.PetClinicTest#info"  # single test
./gradlew build                            # compile + test + jar
```

Tests only run classes matching `org/assertlog/**/*` (configured in root `build.gradle`).

## Module Structure

Two submodules declared in `settings.gradle`:

- **`assertlog-core`** — shared types only; currently just `OriginalLogsPolicy` enum (`SHOW`/`HIDE`)
- **`assertlog-log4j-junit4`** — Log4j 1.x + JUnit 4 integration; depends on `assertlog-core`

Each submodule has its own `build.gradle` for dependencies. Root `build.gradle` applies shared config (Java 8 source/target, UTF-8 encoding, test logging). `gradle/deploy.gradle` handles Maven Central publishing via `uploadArchives` (requires `signing.keyId` property).

## Key Class: MockLogging

`MockLogging` (`assertlog-log4j-junit4`) is a JUnit 4 `ExternalResource` rule that:

1. **`before()`** — saves the root Log4j logger's state, sets the configured log level, removes existing appenders if `policy == HIDE`, then adds a capturing appender that feeds into an internal `ArrayDeque<LoggingEvent>`
2. **`after()`** — removes the capturing appender and restores the original level and appenders

Assertion methods consume events from the queue (ordered, dequeue semantics) except:
- `assertLoggedInAnyOrder` / `assertLogged(Pattern)` / `assertNotLogged(Pattern)` — search without requiring order
- `assertNoMoreLogs()` — fails if the queue is not empty (typically called in `@After`)

`assertNotLogged` and `assertLogged(Pattern)` (the non-consuming overload) **must be called before** the dequeue-based `assertLogged` methods, as the queue is mutated.

## Adding New Integrations

The pattern for a new logging framework or test runner is:
1. Create a new submodule (e.g., `assertlog-logback-junit5`)
2. Add it to `settings.gradle`
3. Implement an equivalent of `MockLogging` using the target framework's extension mechanism
4. Depend on `assertlog-core` for shared types
