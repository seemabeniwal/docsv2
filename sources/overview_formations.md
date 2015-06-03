page_title: Learn About What Makes Shippable Great
page_description: Code examples, FAQs, language & platform support
page_keywords: containers, lxc, docker, Continuous Integration, Continuous Deployment, CI/CD, testing, automation

# Overview

## What is Shippable?

Shippable is a SaaS platform that lets you easily add Continuous
Integration/Deployment to your Github and Bitbucket(Git) repositories.
It is lightweight, super simple to setup, and runs your builds and tests
faster than any other service. After building and testing your code, you
can deploy it to any PaaS provider like Heroku & OpenShift and also to
VMs, bare metal, OpenStack clusters, or any major infrastructure
provider.

Shippable uses **Build Minions** - i.e. docker based containers - to run
your workloads. Docker is the fastest growing linux container solution
and this will light up some cool scenarios like portability and
versioning in the coming weeks.

Common use cases for Shippable include:

-  Automating the packaging and deployment of web applications
-  Automated testing and continuous integration/deployment

> **Note**
>
> Shippable is 100% free to use, even for private
> repositories.

## What is supported?

### Languages

We currently support the following programming languages:

-  Ruby
-  Python
-  Node.js
-  Java
-  Scala
-  PHP
-  GO
-  Clojure

### Platforms

Your Minions can test your repos on:

-  Ubuntu 12.04 LTS
-  Ubuntu 13.10
-  Ubuntu 14.04

### Services

We support the following services natively:

-  MySQL
-  Postgres
-  Elastic Search
-  Redis
-  MongoDB
-  RabbitMQ
-  Neo4j
-  Cassandra
-  RethinkDB
-  CouchDB
-  Selenium

### Tools

A set of common tools are available on all minions. The following is a
list of available tools -

-   Latest release of Git repository
-   apt installer
-   Networking tools
    -   curl
    -   wget
    -   OpenSSL
-   Headless browser testing tools
    -   xfvb
    -   PhantomJS
-   Libraries
    -   ImageMagik
    -   OpenSSL
-   JDK Versions
    -   Oracle JDK 7u6 (oraclejdk7)
    -   OpenJDK 7 (alias: openjdk7)
    -   OpenJDK 6 (openjdk6)
    -   Oracle JDK 8 EA (oraclejdk8)
-   Build Tools
    -   Maven 3
    -   Gradle 1.9
    -   Make
    -   SBT 0.12.1
-   Preinstalled PIP Packages
    -   mock
    -   nosetests
    -   py.test
-   Gems
    -   Bundler
    -   Rake
-   Addons
    -   Firefox
    -   Custom hostname
    -   PostgreSQL

Enough reading! [Get Started!](start.md)
