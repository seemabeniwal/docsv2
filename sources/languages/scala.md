page_title: Scala | Documentation | Shippable
page_description: configuring yml file for scala
page_keywords: scala,SBT,OracleJDK6,OracleJDK7,OpenJDK6,OpenJDK7

# Scala

This section helps you specify the build environment and other
configuration specific to Scala projects.

- Set the appropriate language and version number. You can test
  against multiple versions for a single push by adding more entries.
  Scala minions use `scala` by default to set the version.
- We support SBT, oraclejdk8, oraclejdk7, openjdk6 and openjdk7. Specify Scala versions like 2.8.x, 2.9.x and 2.10.x in the shippable.yml file as shown below.

         language: scala
         scala:
           - 2.8.2
           - 2.9.2

-   Scala builder assumes dependency management based on projects like
    Maven, Gradle or SBT and it will pull down project dependencies
    automatically before running tests.
-   To test against multiple JDKs, use jdk tags. For example, to test against the oraclejdk8, oraclejdk7, openjdk6 and openjdk7

         jdk:
           - oraclejdk8
           - oraclejdk7
           - openjdk6
           - openjdk7

## SBT projects

-   If your repository root has **Project** directory or build.sbt file, then our scala builder will run the test suite using

         sbt ++$SHIPPABLE_SCALA_VERSION test

## Build Examples

This sample will help you get started with Shippable. Testing framework
used here is [ScalaTest](http://scalatest.org/).

[Scala Sample](https://github.com/shippableSamples/sample_scala)

[Scala Sample with MongoDB](https://github.com/shippableSamples/sample_scala_mongo)

[Scala Sample with PostgreSQL](https://github.com/shippableSamples/sample_scala_postgres)

Keep the test and code coverage output in the special folders
Shippable/testresults and Shippable/codecoverage to get the reports
parsed. The test report must be in Junit format.

We need the yml file to analyze your project details . So add the
shippable.yml file to the root of your repo by specifying :

**language :** Scala is used in this sample project

**version number :** 2.10.2 is the version used in this sample project.

**notification alerts:** Email notifications are added to get alerts
about the build status.

Here is the complete yml file for sample_scala project

```yaml
language: scala
scala:
  - 2.10.2
```

Enable the repo sample_scala and run it using an Ubuntu minion. Once
the build finishes execution, you can check for the console output, test
and codecoverage results on the respective build's page.
