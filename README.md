# Reproducer: Nested JAR JSP Loading Fails with Tomcat Embed 11.0.25

Sample reproducer demonstrating an `IllegalStateException: Zip file closed` when resolving JSP files located in nested JARs under Spring Boot 4.1.1 and Tomcat Embed 11.0.25.

## Description

Spring Boot 4.1.1 defaults to Tomcat Embed 11.0.24, which does not exhibit this issue. However, when upgrading to 11.0.25+, compiling JSPs packaged within nested JARs fails.

* **Expected behavior (Tomcat Embed 11.0.24):** The JSP renders successfully and outputs `"It works."` (HTTP 200).
* **Actual behavior (Tomcat Embed 11.0.25):** The request fails with HTTP 500 and throws an `IllegalStateException: Zip file closed`.

### Stack Trace

```
java.lang.IllegalStateException: Zip file closed
        at org.springframework.boot.loader.jar.NestedJarFile.ensureOpen(NestedJarFile.java:414) ~[reproducer-1.0.0-SNAPSHOT.war:1.0.0-SNAPSHOT]
        at org.springframework.boot.loader.jar.NestedJarFile.getInputStream(NestedJarFile.java:358) ~[reproducer-1.0.0-SNAPSHOT.war:1.0.0-SNAPSHOT]
        at org.springframework.boot.loader.jar.NestedJarFile.getInputStream(NestedJarFile.java:347) ~[reproducer-1.0.0-SNAPSHOT.war:1.0.0-SNAPSHOT]
        at org.apache.catalina.webresources.AbstractSingleArchiveResource.getJarInputStreamWrapper(AbstractSingleArchiveResource.java:76) ~[tomcat-embed-core-11.0.25.jar!/:na]
        at org.apache.catalina.webresources.AbstractArchiveResource.getContent(AbstractArchiveResource.java:237) ~[tomcat-embed-core-11.0.25.jar!/:na]
        at org.apache.catalina.webresources.CachedResource.getContent(CachedResource.java:363) ~[tomcat-embed-core-11.0.25.jar!/:na]
        at org.apache.catalina.webresources.CachedResource.getInputStream(CachedResource.java:349) ~[tomcat-embed-core-11.0.25.jar!/:na]
        at org.apache.catalina.core.ApplicationContext.getResourceAsStream(ApplicationContext.java:477) ~[tomcat-embed-core-11.0.25.jar!/:na]
        at org.apache.catalina.core.ApplicationContextFacade.getResourceAsStream(ApplicationContextFacade.java:113) ~[tomcat-embed-core-11.0.25.jar!/:na]
        at org.apache.jasper.JspCompilationContext.getResourceAsStream(JspCompilationContext.java:321) ~[tomcat-embed-jasper-11.0.25.jar!/:na]
        at org.apache.jasper.compiler.JspUtil.getInputStream(JspUtil.java:717) ~[tomcat-embed-jasper-11.0.25.jar!/:na]
        at org.apache.jasper.compiler.ParserController.determineSyntaxAndEncoding(ParserController.java:318) ~[tomcat-embed-jasper-11.0.25.jar!/:na]
        at org.apache.jasper.compiler.ParserController.doParse(ParserController.java:215) ~[tomcat-embed-jasper-11.0.25.jar!/:na]
        ...
```

### Steps to reproduce

The tool defaults to the managed Tomcat-embed version 11.0.24 which does not show the problem. To run a working version, build it with the following command:

```
mvn clean install
```

To show the problem when using the more recent tomcat-embed version 11.0.25, build with the command:

```
mvn clean install -Dtomcat.version=11.0.25
```

To start the tool, switch to the reproducer/target directory and run:

```
java -jar reproducer-1.0.0-SNAPSHOT.war
```

Open the URL:

```
http://localhost:8080/
```


## Authors

Hendrik Kammeyer (hendrik@hengames.de)

## License

This project is licensed under the Apache License 2.0 - see the LICENSE.md file for details
