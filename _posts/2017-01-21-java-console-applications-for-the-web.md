---
layout: post
title:  "Java console applications for the Web"
date:   2017-01-09 23:07:44 +0200
categories: java console Text-IO
---

A few weeks ago I released the first version of [TextIO][TextIO], which is a library for creating Java console applications that read interactive input from the user. I encourage you to read this [DZone Article][dzone-article] and the [TextIO documentation][textio-doc] in order to learn more about this library.

Today I released the version 1.7, which adds a new interesting feature: the ability to access your console application via a browser, by using a [WebTextTerminal][WebTextTerminal].
See the image below for an example:

![A console application in a Web Terminal](/images/web-terminal-animated.gif)

Unlike the other text terminals provided by TextIO, the WebTextTerminal works only in conjunction with a web server supporting the
[DataApi][DataApi] (such as the [SparkDataServer][SparkDataServer])
and a web page that contains code for accessing this API.
This is typically accomplished via [textterm.js][textterm.js],
as shown in the code snippet below.

```
<div id="textterm">
    <p class="textterm-pair">
        <span class="textterm-prompt"> </span>
        <span contenteditable="true" class="textterm-input"> </span>
    </p>
</div>

<script src="textterm.js"></script>
<script>
    var textTerm = TextTerm.init(document.getElementById("textterm"));
</script>
```

Look at the source code of [WebTextIoExecutor.java][WebTextIoExecutor.java]
and [web-demo.html][web-demo.html] for more usage details.

Currently, only WebKit-based browsers (such as Chrome, Opera or Safari) are able to mask input strings.
Keep this in mind when working with sensitive data.


[TextIO]: https://github.com/beryx/text-io
[dzone-article]: https://dzone.com/articles/interactive-console-applications-in-java
[textio-doc]: http://text-io.beryx.org
[WebTextTerminal]: http://text-io.beryx.org/releases/1.7.1/javadoc/index.html?org/beryx/textio/web/WebTextTerminal.html
[DataApi]: http://text-io.beryx.org/releases/1.7.1/javadoc/index.html?org/beryx/textio/web/DataApi.html
[SparkDataServer]: http://text-io.beryx.org/releases/1.7.1/javadoc/index.html?org/beryx/textio/web/SparkDataServer.html
[textterm.js]: https://github.com/beryx/text-io/blob/8c32a37ec542d7cbfa2f458914ab3c863fb2f06f/text-io-web/src/main/resources/public-html/textterm.js
[WebTextIoExecutor.java]: https://github.com/beryx/text-io/blob/8c32a37ec542d7cbfa2f458914ab3c863fb2f06f/text-io-demo/src/main/java/org/beryx/textio/demo/WebTextIoExecutor.java
[web-demo.html]: https://github.com/beryx/text-io/blob/8c32a37ec542d7cbfa2f458914ab3c863fb2f06f/text-io-demo/src/main/resources/public-html/web-demo.html
