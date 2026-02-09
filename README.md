# Starter-Aware Dependency Analyzer

## Overview

This project provides a custom `ProjectDependencyAnalyzer` implementation for
Maven, designed to improve the accuracy of `mvn dependency:analyze` for
projects using Spring Boot.

A common pattern in Spring Boot projects is to pull in a bunch of related
dependencies through so-called "starter" dependencies. Standard Maven
dependency analysis often produces false positives when used with Spring
Boot starters. This occurs because starter dependencies implicitly bring in
many other transitive dependencies, and the `dependency:analyze` goal may
incorrectly flag these as "Used undeclared dependencies" or the starters
themselves as "Unused declared dependencies."

This custom analyzer addresses these issues by understanding the nature of
Spring Boot starters, providing a more relevant and accurate dependency
analysis.

## Problem Statement

The `maven-dependency-analyzer` plugin is a powerful tool for identifying
declared but unused dependencies, and used but undeclared dependencies.
However, in Spring Boot applications, where a single "starter" dependency
(e.g., `spring-boot-starter-web`) can pull in a multitude of related
libraries, the default analyzer often yields misleading results:

*   **Used undeclared dependencies:** Dependencies transitively brought in by
    a starter are frequently flagged as "used but undeclared," even though
    they are intentionally managed by the starter.
*   **Unused declared dependencies:** The starter dependencies themselves
    might be flagged as "unused" if their direct classes aren't explicitly
    referenced, despite their crucial role in configuring the application and
    bringing in necessary transitive dependencies.

## Solution

This project implements a custom `ProjectDependencyAnalyzer` that integrates
with the `maven-dependency-analyzer` plugin. By providing a `starter-aware`
analysis, it aims to:

1.  **Eliminate false positives for transitive dependencies:** Dependencies
    implicitly included by Spring Boot starters will no longer be reported as
    "Used undeclared dependencies."
2.  **Correctly identify starter usage:** Spring Boot starter dependencies
    will not be flagged as "Unused declared dependencies" when they are
    actively contributing to the project's classpath and functionality.

## Features

*   Seamless integration with `maven-dependency-analyzer`.
*   Reduces noise in dependency analysis reports for Spring Boot projects.
*   Provides more accurate insights into actual project dependencies.

## How to Use

To use this custom analyzer, you need to configure the
`maven-dependency-analyzer` plugin in your project's `pom.xml` to use
`io.github.shitikanth.dependencyanalyzer.StarterAwareDependencyAnalyzer`.

First, you'll need to install this artifact into your local Maven repository:

```bash
mvn install
```

Then, add the following configuration to your `pom.xml` (within the `<build><plugins>` section):

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>3.6.1</version> <!-- Use an appropriate version -->
    <configuration>
        <analyzer>io.github.shitikanth.dependencyanalyzer.StarterAwareDependencyAnalyzer</analyzer>
    </configuration>
    <dependencies>
        <dependency>
            <groupId>io.github.shitikanth</groupId>
            <artifactId>starter-aware-dependency-analyzer</artifactId>
            <version>1.0-SNAPSHOT</version> <!-- Match the version of this project -->
        </dependency>
    </dependencies>
</plugin>
```

After configuring, run the dependency analysis as usual:

```bash
mvn dependency:analyze
```

## Building the Project

To build this project, navigate to the root directory and execute:

```bash
mvn clean install
```

This will compile the source code, run tests, and package the analyzer into a
JAR file, installing it into your local Maven repository.

## Running Tests

This project includes unit and integration tests.

To run all tests:

```bash
mvn test
```

To run only the integration tests (which use `maven-invoker-plugin`):

```bash
mvn verify
```

## License

This project is licensed under the Apache 2.0 License. See the `LICENSE` file for more details.
