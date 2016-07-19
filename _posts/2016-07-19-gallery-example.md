---
layout: post
title: Gallery example
author: Martin Rotter
date: 2016-07-19 09:00:00 +0200
category: Misc
language: en
imgfolder: /images/icons
images:
  - name: facebook.png
    text: The first image
  - name: github.png
    text: The second image
---

This is just a brief test of showing galleries with Jekyll:

* <a id="gall1" href="#">two pictures</a>,
* <a id="gall2" href="#">one picture</a>.

{% include gallery.html id="gall1" %}

{% include gallery.html image="facebook.png" id="gall2" %}