# PlaywrightCucumberJunit6

A robust end-to-end test automation framework built with **Playwright**, **Cucumber BDD**, **JUnit 6**, and **Allure Reporting** — powered by Maven.

---

## 🧰 Tech Stack

| Tool | Version | Purpose |
|---|---------|---|
| [Playwright](https://playwright.dev/java/) | 1.55.0  | Browser automation |
| [Cucumber](https://cucumber.io/) | 7.34.2  | BDD feature testing |
| JUnit Jupiter | 6.0.3   | Test runner |
| Allure | 2.30.0  | Test reporting |
| AssertJ | 3.27.7  | Fluent assertions |
| AspectJ | 1.9.24  | AOP for Allure instrumentation |
| SLF4J Simple | 2.0.17  | Logging |

---

## 📁 Project Structure

```
src/
└── test/
    └── java/
        └── starter/
            ├── catalog/
            │   └── pageobjects/         # Page Object Model classes
            │       ├── CartLineItem
            │       ├── CheckoutCart
            │       ├── NavBar
            │       ├── ProductDetails
            │       ├── ProductList
            │       ├── ProductSummary
            │       └── SearchComponent
            ├── contact/
            │   └── pageobjects/
            │       └── ContactForm
            └── cucumber/
                ├── fixtures/            # Playwright lifecycle management
                │   ├── PlaywrightDriverFactory.java
                │   ├── PlaywrightTracing.java
                │   └── ScreenshotManager.java
                ├── stepdefinitions/
                │   └── ProductCatalogStepDefinitions.java
                └── CucumberTests.java   # JUnit Suite entry point
    └── resources/
        ├── features/
        │   └── product_catalog.feature  # Cucumber feature files
        ├── allure.properties
        └── junit-platform.properties
```

---

## ⚙️ Prerequisites

- Java 17+
- Maven 3.6+
- No separate browser installation required — Playwright manages browser binaries automatically

---

## 🚀 Running Tests

### Run all integration tests (default: Chromium, headless)

```bash
mvn verify
```

### Run with a specific browser

```bash
mvn verify -Dbrowser=firefox
mvn verify -Dbrowser=webkit
mvn verify -Dbrowser=chromium
```

### Run in headed (visible) mode

```bash
mvn verify -Dheadless=false
```

### Enable video recording

```bash
mvn verify -DrecordVideo=true
```
Videos are saved to `target/videos/`.

### Enable Playwright tracing

```bash
mvn verify -DenableTracing=true
```
Traces are saved to `target/traces/trace-<scenario-name>.zip` and can be viewed at [trace.playwright.dev](https://trace.playwright.dev).

---

## 🔄 Parallel Execution

Parallel test execution is enabled by default via `junit-platform.properties`:

```properties
cucumber.execution.parallel.enabled=true
cucumber.execution.parallel.config.strategy=fixed
cucumber.execution.parallel.config.fixed.parallelism=5
cucumber.execution.parallel.config.fixed.max-pool-size=5
```

The `PlaywrightDriverFactory` uses `ThreadLocal` storage to ensure each thread gets its own isolated `Playwright`, `Browser`, `BrowserContext`, and `Page` instance — making parallel execution fully thread-safe.

---

## 📸 Screenshots & Reporting

- **Automatic failure screenshots** — `ScreenshotManager` captures a full-page screenshot after any failed step and attaches it to the Allure report.
- **Allure reports** — Generated automatically after `mvn verify` in `target/allure-results/`.

### View the Allure Report

```bash
mvn allure:serve
```

Or generate a static report:

```bash
mvn allure:report
```

The report will be available at `target/site/allure-maven-plugin/index.html`.

---

## 📋 Cucumber Configuration

Tests are discovered and executed via the `CucumberTests` JUnit Suite:

```java
@Suite
@IncludeEngines("cucumber")
@SelectPackages("features")
@ConfigurationParameter(key = "cucumber.plugin", value = "io.qameta.allure.cucumber7jvm.AllureCucumber7Jvm,pretty,html:target/cucumber-reports/cucumber.html")
public class CucumberTests {}
```

HTML reports are also written to `target/cucumber-reports/cucumber.html`.

---

## 🏗️ Architecture Highlights

### PlaywrightDriverFactory
Manages the full Playwright lifecycle using `ThreadLocal` for thread safety. Browser type, headless mode, and video recording are all configurable at runtime via system properties or environment variables.

| System Property | Default | Description |
|---|---|---|
| `browser` | `chromium` | Browser to use (`chromium`, `firefox`, `webkit`) |
| `headless` | `true` | Run in headless mode |
| `recordVideo` | `false` | Record video of each scenario |
| `enableTracing` | `false` | Enable Playwright trace recording |

The `BROWSER` environment variable can also be used as a fallback for `browser`.

### Page Object Model
Page objects are organized by feature area under `catalog.pageobjects` and `contact`, keeping UI interaction logic separated from step definitions.

### Test ID Attribute
The framework is configured to use `data-test` as the Playwright test ID attribute:

```java
Playwright pw = Playwright.create().selectors().setTestIdAttribute("data-test");
```

---

## 📦 Build

The project uses **maven-failsafe-plugin** to run integration tests (surefire is skipped). Tests matching `**/cucumber/*Tests.java` are included.

```bash
# Skip tests entirely
mvn install -DskipTests

# Run only tests
mvn failsafe:integration-test failsafe:verify
```

---

## 📄 License

This project is for demonstration and learning purposes.
