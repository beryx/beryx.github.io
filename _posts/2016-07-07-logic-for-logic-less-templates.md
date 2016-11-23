---
layout: post
title:  "Logic for logic-less templates"
date:   2016-07-07 20:36:12 +0200
categories: java templates Handlebars
---

There are many situations where content needs to be created automatically.
The most common case is probably the dynamic generation of web pages, for which template engines are a popular choice.
Some template engines take a logic-less approach, by denying or discouraging the use of logic in templates.
Examples of logic-less template systems are [Mustache][mustache] and [Handlebars][handlebars], for which Java implementations are also available: [Mustache.java][mustache.java] and [Handlebars.java][handlebars.java].

Logic-less templates seem to [polarize][logic-less-so] [opinions][logic-less-cult].
Proponents see them as the best way to achieve a clear separation of concerns in MVC architectures.
Opponents argue that forcing the controller to do data-massaging before sending the data to the UI is actually a bad thing, because it forces the controller to be aware of the presentation logic.

I will not take a side in this debate, because I have very little experience in developing web applications.
However, template engines are also useful for other purposes and I argue that there are cases, especially in non-MVC settings, where using logic in templates is legitimate. Nonetheless, putting too much logic in a template is still a bad idea.

I used recently Handlebars.java for generating source code and I needed to add some logic to my templates.
Handlebars.java allows you to do this through the use of helpers, therefore I wrote the **[Handlebars.java Helpers][hbj-helpers-gh]** library, which provides a series of helpers for common use cases.
Most of them are basic helpers that can be used as subexpressions in the built-in block helpers of Handlebars.java.
This lets you write the template logic in a fluent way.

**Example**

Given the following YAML model:
<pre>
birthYear: 1997
</pre>

and the following template:

<pre>{% raw %}
{{def 'fifteenYear' (math birthYear '+' 15)}}
{{#ifb (or
    (and
        (compare (math fifteenYear '%' 4) '==' 0)
        (compare (math fifteenYear '%' 100) '!=' 0)
    )
    (compare (math fifteenYear '%' 400) '==' 0)
   )
}}
Your fifteenth anniversary was in a leap year!
{{else}}
Your fifteenth anniversary was in a non-leap year!
{{/ifb}}
{% endraw %}</pre>

The resulting text will be:
<pre>
Your fifteenth anniversary was in a leap year!
</pre>

&nbsp;
<br/>
If you use Handlebars.java, you may find this library useful, so read its **[documentation][hbj-helpers-doc]** and give it a try!



[url-length]: http://stackoverflow.com/a/417184/2039982
[mustache]: http://mustache.github.io/
[handlebars]: http://handlebarsjs.com/
[mustache.java]: https://github.com/spullara/mustache.java
[handlebars.java]: http://jknack.github.io/handlebars.java/
[logic-less-so]: http://stackoverflow.com/questions/3896730/whats-the-advantage-of-logic-less-template-such-as-mustache
[logic-less-cult]: http://www.boronine.com/2012/09/07/Cult-Of-Logic-less-Templates/
[hbj-helpers-gh]: https://github.com/beryx/handlebars-java-helpers
[hbj-helpers-doc]: http://handlebars-java-helpers.beryx.org/
