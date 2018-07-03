---
layout: post
title:  "Create java.awt.Color from string representation"
date:   2018-07-03 21:37:26 +0200
categories: java awt javafx Text-IO
---

When using the [Text-IO library][text-io], you can configure the text terminals via properties files.
Colors can be specified using standard HTML names (e.g. `orange`, `chocolate`, `crimson`), hex strings (e.g. `#ee5533`, `#e53`, `0xEE5533`),
_rgb_ format strings (e.g. `rgb(217,33,119)`, `rgba(112,25%,50%,0.9)`),
or _hsl_ format strings (e.g. `hsl(240,90%,70%)`, `hsla(270,0%,100%,0.7)`).


How does Text-IO create colors from these string representations?
Until recently, the implementation used the static method [`web(String colorString)`][color-web-method] provided by `javafx.scene.Color`.
This method supports all the above mentioned formats and returns a `javafx.scene.Color` instance.
Text-IO needs a `java.awt.Color`, but creating one from the returned `javafx.scene.Color` is straightforward.

However, I was not happy with this solution.
It made my code depend on JavaFX, although Text-IO doesn't actually use JavaFX.
And starting with JDK 11, JavaFX is no longer part of the JDK.

So I decided to write a little library named [AWT Color Factory][awt-color-factory] for creating java.awt.Color objects from string representations.
The simplest way to do this is to copy the code of the `javafx.scene.Color.web(String colorString)` method and adapt it to create java.awt.Color instances.

But, what about licensing?
JavaFX is released under the GPL, so my library must use the same license.
Text-IO is instead released under the Apache-2.0 license. Is it then allowed to use my library?
Yes, it is. The license under which JavaFX is released is actually not GPL, but [GPL 2 with Classpath exception][gpl2-ce].
The same applies to my library.
This is very important, because the _Classpath exception_ clause gives you permission to include the _AWT Color Factory_ library in your executable code, regardless of the license under which your software is distributed.


**Example usage**
{% highlight java %}
Color c1 = ColorFactory.valueOf("firebrick");
Color c2 = ColorFactory.valueOf("#aa38e0");
Color c3 = ColorFactory.valueOf("0x40A8CC");
Color c4 = ColorFactory.valueOf("rgba(112,36,228,0.9)");
Color c5 = ColorFactory.web("forestgreen", 0.7);
Color c6 = ColorFactory.web("hsl(270,90%,70%)", 0.8);
{% endhighlight %}


_AWT Color Factory_ is available in JCenter and Maven Central.

**Maven**
{% highlight xml %}
<dependency>
    <groupId>org.beryx</groupId>
    <artifactId>awt-color-factory</artifactId>
    <version>1.0.0</version>
</dependency>
{% endhighlight %}

**Gradle**
{% highlight groovy %}
compile 'org.beryx:awt-color-factory:1.0.0'
{% endhighlight %}



The library requires Java 7 or newer. The jar artifact is modularized under the name `org.beryx.awt.color`.

Starting with version 3.2.0, Text-IO uses the _AWT Color Factory_ library and no longer depends on JavaFX.


[awt-color-factory]: https://github.com/beryx/awt-color-factory/blob/master/README.md
[text-io]: https://github.com/beryx/text-io/blob/master/README.md
[color-web-method]: https://docs.oracle.com/javase/10/docs/api/javafx/scene/paint/Color.html#web(java.lang.String)
[gpl2-ce]: http://openjdk.java.net/legal/gplv2+ce.html
