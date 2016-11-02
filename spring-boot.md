Spring Boot 性能优化

https:\/\/yq.aliyun.com\/articles\/2360

https:\/\/dzone.com\/articles\/spring-boot-memory-performance?spm=5176.100239.blogcont2360.16.HDmhsU

http:\/\/www.alexecollins.com\/spring-boot-performance\/?spm=5176.100239.blogcont2360.15.HDmhsU



The [Spring Boot Maven plugin](https://github.com/spring-projects/spring-boot/tree/master/spring-boot-tools/spring-boot-maven-plugin) provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable "über-jar", which makes it more convenient to execute and transport your service.
* It searches for the public static void main\(\) method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match [Spring Boot dependencies](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-dependencies/pom.xml). You can override any version you wish, but it will default to Boot’s chosen set of versions.

