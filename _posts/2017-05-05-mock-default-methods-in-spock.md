---
layout: post
title:  "Partial mocks for Java 8 interfaces with default methods in Spock"
date:   2017-05-05 01:43:55 +0200
categories: java Spock
---

[Spock][Spock] 1.1 has been released today and includes a series of new features and improvements.
One of them, which is my contribution, is the ability to create partial mocks for Java 8 interfaces that contain default methods.

Let's see how it works:

{% gist 74c77618e4caceac94e9 %}

By creating a spy for the _ISquare_ interface and mocking the _getLength()_ method, we are able to test the default implementation of the _getArea()_ method.

Nothing surprising here.
It's probably exactly what you expected.
But it didn't work with Spock version 1.0 or earlier.


[Spock]: https://github.com/spockframework/spock
