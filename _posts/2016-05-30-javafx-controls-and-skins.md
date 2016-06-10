---
layout: post
title:  "JavaFX controls and skins"
date:   2016-05-30 23:48:26 +0200
categories: javafx
---
[JavaFX controls follow the MVC pattern][controls-mvc], which allows multiple views for the same model.
Therefore, multiple [Skins][javafx-skin] can be provided for a [Control][javafx-control].
A good example is the [Medusa library][medusa], which offers a gauge control with about 30 different skins.

I wanted to play a bit with JavaFX controls and skins, so I decided to write my own gauge control.
My goal was to keep the code simple and easy to understand, underlining the separation between model (the gauge) and views (the skins).
The result is the [JFXGauge library][jfxgauge-github].

JFXGauge provides only two skins.
The first one is probably the most simple skin you can think of.
It consists only of a [Text][javafx-text] component.
However, using CSS styling you can customize even this skin in many interesting ways:  
![text skin][img-text-thumb]  
[(click for animated gif)][img-text]


The second skin is a thermometer skin and is more complex:  
![thermo skin][img-thermo-thumb]  
[(click for animated gif)][img-thermo]


For this skin I also defined a few [styleable properties][styleable-props] that allow configuring the thermometer's aspect ratio and the position of the range and threshold markings.
Implementing styleable properties involves writing boilerplate code, but Hendrik Ebbers' [CSS Helper][css-helper] reduces the amount needed.

JFXGauge is available in Maven Central and JCenter.

Try the demo application included in the [latest release][jfxgauge-release], read the [documentation][jfxgauge-doc] and use the gauge in your projects!


[controls-mvc]: https://wiki.openjdk.java.net/display/OpenJFX/UI+Controls+Architecture

[css-helper]: http://www.guigarage.com/2014/03/javafx-css-utilities

[javafx-control]: https://docs.oracle.com/javase/8/javafx/api/javafx/scene/control/Control.html

[javafx-skin]: https://docs.oracle.com/javase/8/javafx/api/javafx/scene/control/Skin.html

[javafx-text]: https://docs.oracle.com/javase/8/javafx/api/javafx/scene/text/Text.html

[jfxgauge-doc]: http://jfxgauge.beryx.org

[jfxgauge-github]: https://github.com/beryx/jfxgauge

[jfxgauge-release]: https://github.com/beryx/jfxgauge/releases/latest

[medusa]: https://github.com/HanSolo/Medusa

[styleable-props]: https://wiki.openjdk.java.net/display/OpenJFX/CSS+API+to+support+custom+UI+Controls

[img-text]: https://github.com/beryx/jfxgauge/raw/master/doc/img/textDemo.gif

[img-text-thumb]: https://github.com/beryx/jfxgauge/raw/master/doc/img/textDemo-thumbnail.gif

[img-thermo]: https://github.com/beryx/jfxgauge/raw/master/doc/img/thermoDemo.gif

[img-thermo-thumb]: https://github.com/beryx/jfxgauge/raw/master/doc/img/thermoDemo-thumbnail.gif
