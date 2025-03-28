# Code Style

In our effort to maintain a consistent code style across all our projects, we have integrated multiple tools that verify our code both in the Integrated Development Environment (IDE) and during Pull Request (PR) verification.

The tools we use are consistent across both Android-only and Kotlin Multiplatform (KMP) projects.

## Tools

### KtLint

[KtLint](https://github.com/pinterest/ktlint) is an anti-bikeshedding Kotlin linter with a built-in formatter. It enforces a consistent code style and can report or automatically fix deviations from this style.

We use this [Gradle plugin](https://github.com/JLLeitschuh/ktlint-gradle) for integration, which provides the following useful tasks:
- `ktLintCheck`: Scans the entire project for issues (used for PR checks).
- `ktLintFormat`: Works similarly to `ktLintCheck`, but also formats what is possible. Use this before making commits to ensure correct formatting.

KtLint also offers an IDEA plugin that can format code either automatically or when triggered by the format code command. The use of this plugin is optional.

In our projects, we use `.editorconfig` to unify code formatting between IDE and KtLint.

### .editorconfig

.editorconfig helps maintain consistent coding styles across different environments. Every project includes one in the root directory.

Link to [current config](https://github.com/futuredapp/kmp-futured-template/blob/develop/.editorconfig)

### Android Lint

Android Lint is a scanning tool provided by Android, designed to check source files for potential bugs and optimization improvements regarding correctness, security, performance, usability, accessibility, and internationalization.

All rules are specified in the `lint.xml` file in the root directory.

### Compose Lints

[Compose Lints](https://github.com/slackhq/compose-lints) is an experimental lint-checks tool for Jetpack Compose. It helps to adhere to best practices and avoid common pitfalls when using Compose.

Just like Android Lint, it includes additional rules in the `lint.xml` file.

Link to [current config](https://github.com/futuredapp/kmp-futured-template/blob/develop/lint.xml)

### Detekt

[Detekt](https://github.com/detekt/detekt) is a static code analysis tool for Kotlin. It operates on the abstract syntax tree provided by the Kotlin compiler and can detect issues like code complexity, duplications, and naming conventions, among others.

The config file is located at `config/detekt.yml`.

Link to [current config](https://github.com/futuredapp/kmp-futured-template/blob/develop/config/detekt.yml)

### Danger

[Danger](https://danger.systems/) provides an easier way to craft Continuous Integration scripts. It runs during your CI, and automates common code review chores, hence providing additional continuous integration and enforcement of specific standards before merging any code.

Each repository should have its own `Dangerfile`, adapted to specific needs. We also apply company-wide [danger rules](https://github.com/futuredapp/danger) across all our projects. The `Dangerfile` refers to these rules.

## How to be a good developer

1. Set your Android Studio code style to the default settings - let `.editorconfig` define your IDE's formatting style.
2. Either install the KtLint IDEA plugin or run the `ktLintFormat` gradle task before making a commit.
3. When you are ready to create a PR, ensure you run the `lintCheck` gradle task first and fix any reported issues to keep the codebase clean.
4. During a PR check, the `lintCheck` gradle task is run again to catch any remaining issues, which are then reported by Danger.
