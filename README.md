# assertlog
Unit-tests for your logging

[![Maven Central](https://img.shields.io/maven-central/v/com.codeborne/assertlog.svg)](https://central.sonatype.com/search?q=assertlog&namespace=com.codeborne)
[![MIT License](http://img.shields.io/badge/license-MIT-green.svg)](https://github.com/selenide/selenide/blob/main/LICENSE)
![Free](https://img.shields.io/badge/free-open--source-green.svg)
![Build Status](https://github.com/java-testkit/assertlog/actions/workflows/test.yml/badge.svg)


## What is AssertLog?

AssertLog is a library for unit-testing logging in your application.

Sometimes logging is very important, especially if it contains sensitive information, security incidents, money transfers etc.

Here is how you can use AssertLog:

```java
import org.assertlog.junit4.log4j.MockLogging;
import org.junit.Rule;
import org.junit.Test;

public class PetClinicTest {
  @Rule
  public MockLogging mockLogging = new MockLogging();

  @Test
  public void payment() {
      // make a payment
      mockLogging.assertLogged("INFO", "Buying 2 Cows for 99.66 eur");
  }
  
  @After
  public void tearDown() {
    mockLogging.assertNoMoreLogs();
  }
}
```

## What logging frameworks are supported?

We plan to support all major Java logging frameworks. Currently only Log4j 1 and JUnit 4 are supported. 

## Dependency

You need to add this dependency to you `pom.xml`:

```xml
<dependency>
  <groupId>com.codeborne.assertlog</groupId>
  <artifactId>assertlog-log4j-junit4</artifactId>
  <version>0.1</version>
  <scope>test</scope>
</dependency>
```

or `build.gradle`:
```groovy
testImplementation('com.codeborne.assertlog:assertlog-log4j-junit4:0.1')
```

## License

AssertLog is open-source project, and distributed under the [MIT](http://choosealicense.com/licenses/mit/) license.
