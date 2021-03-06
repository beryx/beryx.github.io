---
layout: post
title:  "Add code snippets to your technical presentations"
date:   2018-08-01 00:14:42 +0200
categories: asciidoc asciidoctor boothub template deck.js
---

Most of my technical presentations contain code snippets, but putting nice looking code into my slides was always a tedious task.
After years of using PowerPoint, I started looking for alternatives.
There are many good ones, and I was able to find a solution that matches both my personal preferences and my typical use cases.

My source code is usually Java, Groovy, or Kotlin.
For each presentation, I set up a project containing the code examples I'm going to use in my slides.
I want the presentation files to integrate seamlessly into this project.
Below are a few aspects that I considered in order to tailor my solution.

**Version control**

Source code and presentation files should be managed using a version control system (VCS).
I use git, but any decent VCS will do.

With a VCS, you can use branches to manage several variants of the same presentation, each one refined for a different audience.

To be able to compare different versions of the presentation files, they need to be written in a text format.
I opted for [AsciiDoc][asciidoc].


**Build tool**

Besides building the code examples, the build tool should also convert the presentation files into a slide deck.
[Asciidoctor][asciidoctor] provides a [Gradle plugin][asciidoc-gradle],
a [Maven plugin][asciidoc-maven], and an [Ant task][asciidoc-ant] for this job.

There are also several [presentation backends][backends] to choose from: [deck.js][deckjs],
[reveal.js][revealjs], [DZSlides][dzslides], [Bespoke.js][bespokejs].
The build tool should also take care of downloading and configuring the chosen backend.

I settled on Gradle and deck.js.

**Keeping the slide content in sync with the codebase**

To ensure that the slides always contain valid and up-to-date code, the code snippets should be retrieved directly from your codebase.
Luckily, AsciiDoc lets you [include](https://asciidoctor.org/docs/user-manual/#include-directive)
whole source code files or [only portions](https://asciidoctor.org/docs/user-manual/#include-partial) of them in your presentation files.

The example below shows how to add a code snippet containing the _main_ method to your slides.

<span style="color: brown; font-style: italic;">src/main/java/org/example/Hello.java</span>
{% highlight java %}
package org.example;

public class Hello {
    // tag::main[]
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
    // end::main[]
}
{% endhighlight %}


<span style="color: brown; font-style: italic;">asciidoc/presentation.adoc</span>
{% highlight asciidoc %}
:sourcedir: ../src/main/java

[source,java]
----
include::{sourcedir}/org/example/Hello.java[tags=main,indent=0]
----
{% endhighlight %}

As you can see, the source code portion to be included (in our case, the _main_ method) is marked with user-defined tags appearing as comments in the code.
I would probably be annoyed if I saw these comments cluttering production code,
but I'm totally fine with them in a codebase used for presentation purposes only.

### Putting all together

To make your life easier, I created a [BootHub template][slide-deck] that sets up a customized presentation project for you.
It lets you configure things like project name, presentation title, author, programming language, or the base package of your project.

BootHub will generate a presentation file in accordance with your settings.
To give you a quick start, the content of this presentation file illustrates some of the AsciiDoc and deck.js features that allow you to create a great slide deck: tables, images, blockquotes, transitions, incremental reveal, source code inclusion.

To build the codebase and create the slides execute:
{% highlight bash %}
./gradlew build asciidoc
{% endhighlight %}
Then, open the _index.html_ file found in _build/asciidoc/html5_ in your browser.

Click on the image below to see the slide deck produced by a generated presentation file.

<span style="color: brown; font-style: italic;">Sample slide deck generated by BootHub</span><br>
[![Sample slide deck generated by BootHub](/images/slide-deck-sample.gif)](https://slide-deck.boothub.org/sample/)

If you prefer another build tool or another presentation backend, I encourage you to adapt my template to your tastes and publish your version to BooHub.
I'm sure a lot of people will thank you for that.
See [the documentation][boothub-template-doc] about writing BootHub templates.

And if you want to learn more about BootHub, i recommend you [this article][boothub-article].

[asciidoc]: http://www.methods.co.nz/asciidoc/
[asciidoctor]: https://asciidoctor.org/
[backends]: https://github.com/asciidoctor/asciidoctor-backends/blob/master/README.adoc
[deckjs]: http://imakewebthings.com/deck.js/
[revealjs]: https://revealjs.com
[dzslides]: http://paulrouget.com/dzslides/
[bespokejs]: http://markdalgleish.com/projects/bespoke.js/
[asciidoc-gradle]: https://asciidoctor.org/docs/asciidoctor-gradle-plugin/
[asciidoc-maven]: https://asciidoctor.org/docs/asciidoctor-maven-plugin/
[asciidoc-ant]: https://github.com/asciidoctor/asciidoctor-ant
[slide-deck]: https://boothub.org/goto/org.boothub.slide-deck
[boothub-template-doc]: https://doc.boothub.org/releases/latest/#Templates
[boothub-article]: https://dzone.com/articles/how-to-bootstrap-your-open-source-projects
