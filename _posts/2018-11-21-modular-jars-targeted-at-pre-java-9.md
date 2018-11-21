---
layout: post
title:  "How to create modular jars that target a Java release before 9"
date:   2018-11-21 17:54:07 +0100
categories: jpms jigsaw jar gradle
---


If you're a library author, you probably want to see your library used in a large number of applications.
One way to increase the number of applications that can include your library is to make it compatible with older Java versions.
At the same time, you may consider modularizing your library in order to make it attractive
for applications that take full advantage of the Java Platform Module System (JPMS).

However, the JPMS is implemented only by Java 9 and newer versions.
So, how can you build a library that is both modularized and compatible with Java versions before 9?

A modular jar is just a normal jar that includes a `module-info.class` file, which represents the module descriptor.
This module descriptor is typically produced by compiling a corresponding `module-info.java` file.
The module descriptor is ignored when running on Java 8 and older versions, because `module-info` is not a legal Java identifier.
This means that a modular jar compatible with a pre-Java 9 release can be produced by creating a normal jar
for the target version and inserting a module descriptor into it.

The standard way to do this implies invoking the java compiler twice:
- compile all sources except `module-info.java` using the appropriate `javac` flags to
provide compatibility with the target pre-Java 9 version.
- compile `module-info.java` using a Java 9+ compiler.


### Maven ###

Maven [describes][maven-build] a build method along the lines of the above mentioned strategy..

An alternative solution is offered by [ModiTect][moditect], a Maven plugin for working with the Java 9 module system.
One of the goals provided by ModiTect is `add-module-info`, which allows you to add a module descriptor to the JAR produced by the current Maven project:

{% highlight xml %}
...
<plugin>
    <groupId>org.moditect</groupId>
    <artifactId>moditect-maven-plugin</artifactId>
    <version>1.0.0.Beta1</version>
    <executions>
        <execution>
            <id>add-module-infos</id>
            <phase>package</phase>
            <goals>
                <goal>add-module-info</goal>
            </goals>
            <configuration>
                <jvmVersion>11</jvmVersion>
                <module>
                    <moduleInfoFile>
                        ${basedir}/src/main/java/module-info.java
                    </moduleInfoFile>                
                </module>
            </configuration>
        </execution>
    </executions>
</plugin>
...
{% endhighlight %}

ModiTect uses a clever technique to create the module descriptor without the need of a Java compiler.
It analyzes the `module-info.java` file with the [JavaParser][javaparser] and uses the
[ASM][asm] bytecode manipulation framework to generate the corresponding module descriptor.
This way, it allows you to create a modular jar even if you use a pre-Java 9 compiler.


### Gradle ###

Taking inspiration from ModiTect's approach, I implemented a [Gradle plugin][badass-jar]
for creating modular jars that target Java 8 or older versions.

Using this plugin is straightforward: in `build.gradle` you just need to add the plugin
and specify the Java version targeted by your library:

{% highlight groovy %}
plugins {
    id 'java'
    id 'org.beryx.jar' version '1.0.0'
}
sourceCompatibility = 1.8
targetCompatibility = 1.8
{% endhighlight %}

After placing the `module-info.java` file in the `src/main/java` directory of your project, you can build the modular jar with:
{% highlight sh %}
./gradlew jar
{% endhighlight %}


You don’t need a Java 9+ compiler to build the modular jar, because the plugin uses the same technique as ModiTect.
However, keep in mind that this technique cannot check the validity of your module descriptor.
References to nonexistent packages, modules, services, or service implementations go undetected.

Therefore, it is strongly advised to validate your module descriptor by additionally building your project using a Java 9+ compiler with the `sourceCompatibility` and `targetCompatibility` set accordingly.
The good news is that you don’t need to change your build script in order to do this.
The plugin lets you override the `sourceCompatibility` and `targetCompatibility` values by setting the project property `javaCompatibility`:

{% highlight sh %}
./gradlew -PjavaCompatibility=9 jar
{% endhighlight %}

Note that this project property overrides both `sourceCompatibility` and `targetCompatibility` with the same value.

If the plugin detects that at least one of sourceCompatibility and targetCompatibility has an effective value >= 9, it automatically applies the [Chainsaw][chainsaw] plugin, which adds support for the JPMS.
That’s why no changes are required to your build script when running in Java 9+ compatibility mode.

In the discussion above we considered that `sourceCompatibility` and `targetCompatibility` are configured with pre-Java 9 values in `build.gradle` and you override them with 9+ values using the `javaCompatibility` project property.
You can also work the other way around: set `sourceCompatibility` and `targetCompatibility` with 9+ values in `build.gradle` and override them with a pre-Java 9 value (via `javaCompatibility`) in order to generate a modular jar targeted to Java releases before 9.

Both approaches have their pros and cons.
Choose the one that suits you the most.
The important thing is to test your library constantly on both the class-path and the module-path.
The best way to do this is to configure your continuous integration server to execute the Gradle build twice:
once with the `javaCompatibility` property set and once without it.

Typically, a [modular jar][modular-jar] includes the `module-info.class` file in its root directory.
This may cause problems for some older tools, which incorrectly process the module descriptor as if it were a normal Java class.
To provide the best backward compatibility, the plugin creates by default a [modular multi-release jar][multi-release]
with the `module-info.class` file located in `META-INF/versions/9`, as shown below.

<img src="/images/multi-release-jar.png" alt="Normal and multi-release jar" width="548"/>

If, for some reason, you don't want to create a multi-release jar, set the `multiRelease` property to false
in the `jar` section of your build script:

{% highlight groovy %}
jar {
    ...
    multiRelease = false
    ...
}
{% endhighlight %}


### Examples ###

To illustrate how to use this plugin, I implemented a [Java library][nqueens-java]
and a [Kotlin library][nqueens-kotlin] for solving the [N-Queens problem][nqueens].

Note how [Travis][travis] is configured in these projects to test the library on
both the module-path and the class-path:

{% highlight groovy %}
./gradlew -PjavaCompatibility=11 --no-daemon -i -s build
./gradlew --no-daemon -i -s build
{% endhighlight %}


[badass-jar]: https://github.com/beryx/badass-jar-plugin/blob/master/README.adoc
[maven-build]: https://maven.apache.org/plugins/maven-compiler-plugin/examples/module-info.html
[moditect]: https://github.com/moditect/moditect/blob/master/README.md#adding-a-module-descriptor-to-the-project-jar
[javaparser]: https://github.com/javaparser/javaparser
[asm]: https://asm.ow2.io/
[chainsaw]: https://github.com/zyxist/chainsaw
[modular-jar]: https://openjdk.java.net/projects/jigsaw/spec/sotms/#module-artifacts
[multi-release]: https://openjdk.java.net/jeps/238#Modular-multi-release-JAR-files
[nqueens-java]: https://github.com/beryx-gist/badass-jar-example-nqueens
[nqueens-kotlin]: https://github.com/beryx-gist/badass-jar-example-nqueens-kotlin
[nqueens]: https://en.wikipedia.org/wiki/Eight_queens_puzzle
[travis]: https://github.com/beryx-gist/badass-jar-example-nqueens/blob/master/travis-build.sh
