---
layout: post
title:  "In-memory URL content"
date:   2016-06-02 23:27:32 +0200
categories: groovy howto
---

A few days ago I had to write a suite of integration tests, where each test case required a URL as input data.
In my first implementation, I stored the URL content of each test case in a separate file and used the corresponding file URL as input data.
However, I was not happy with this solution, because in order to understand what a test case does, one had to open the associated file containing the URL content.
For my test cases, each URL content consisted of only a few lines of text, so I wanted to keep the URL content in the source code of the test case, as a string.

One idea was that each test case implementation will create a temporary file containing its associated content string.
But this was boring, so I started thinking about providing a URL with a custom protocol, which will retrieve its content from memory.
One way to do this is to escape the content and store it as the URL's query part.
However, many browsers and servers have [a limit of about 2000 characters][url-length] for the length of a URL.
Because I did not want to restrict the number of characters allowed for the URL content, I decided to keep the URL contents as values in a static map with integer indexes.
You can see the implementation below:

{% gist 6586ace06fdb4f56f374daa0e3f1e1b1 %}

The above implementation is in Groovy, but it can be easily ported to Java.

Of course, this solution works only if the module that retrieves the URL content runs in the same JVM as the code that created the URL.

In my integration test suite, I used a relatively small number of URLs with a small content size, therefore the growing size of the inMemoryMap was of no concern.
But if your use case involves a huge number of URLs and large content sizes, you should add housekeeping capabilities to the InMemoryProtocol.

[url-length]: http://stackoverflow.com/a/417184/2039982
