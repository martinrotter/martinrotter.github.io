---
layout: post
title: How to install Chromium for all users on Windows - the clean way
author: Martin Rotter
date: 2016-07-17 09:00:00 +0200
category: IT
language: en
---

This tutorial shows you how to install Chromium (NOT Chrome) on Windows machine for all users.
<!--more-->

This way, Chromium gets installed into `C:\Program Files\Chromium` folder:

1. Download latest [mini installer](http://chromium.woolyss.com/) of Chromium. I recommend using 32 bit version even on 64 bit systems to avoid problems with external plugins such as Flash Player.
2. Open command line and navigate to folder which contains downloaded mini installer and run `mini-installer.exe --system-level`. This will install Chromium properly and make it available for all users.