---
layout: post
title: Goals and targets
author: Martin Rotter
date: 2014-04-26 09:00:00 +0200
category: gsoc-2014
language: en
---

Well, let me sum up all goals which are given to me as part of my GSoC job. My GSoC project has fancy name, it is "Porting and reworking the BuildmLearn Toolkit for Linux, OS2 and Mac OS X". The "reworking" part will be probably tougher, as the majority of the application will be rewritten to better supportability and maintainability.
<!--more-->

I split goals into several categories. OK, let's see it. [TBD] means "To Be Done".

### openSUSE Build Service

* <span class="label label-success">DONE</span> Create OBS scripts and make semi-automatic package creation working for major Linux distributions.
* <span class="label label-success">DONE</span> Write short tutorial about OBS to explain its functionality to BuildmLearn Toolkit community.

### CMake

* <span class="label label-success">DONE</span> Add support for NSIS semi-automatic installators creation.
* <span class="label label-success">DONE</span> Make CMake script working with Qt4 and Qt5 and support Windows, Linux and Mac OS X.
* <span class="label label-success">DONE</span> Implement "make lupdate" and "make lrelease" targets for refreshing of localization files.

### Miscellaneous

* <span class="label label-success">DONE</span> Setup Transifex service for localization of the application.

### Source code

* <span class="label label-success">DONE</span> Use Doxygen to document the source code.
* <span class="label label-success">DONE</span> Remove QRC approach and use external files only.
* <span class="label label-success">DONE</span> Restructure the overall layout of sources within the project, clearly separate the application logic core from user interface, separate the templates.
* <span class="label label-success">DONE</span> Tweak code style and conventions we use to match general Qt code style.
* <span class="label label-success">DONE</span> Implement custom application-wide settings mechanism which is needed for every serious applications and allows us to have “portable” application which can be run for example from USB stick.
* <span class="label label-success">DONE</span> Implement basic "check for updates" feature (user is notified about existince of newer stable version than he/she currently uses).
* <span class="label label-success">DONE</span> Implement custom application icons handling mechanism (the one provided by Qt is sometimes faulty).
* <span class="label label-success">DONE</span> Separate all inline Qt stylesheets in separate mechanism and implement XML based “skin” mechanism.
* <span class="label label-success">DONE</span> Refactor the code base. Remove deprecated API usage, use fixed-size modal dialogs on Windows platform and fix some QLayout-related memory leaks. Enforce correct QObject-parent usage throughout the code base.
* <span class="label label-success">DONE</span> Rewrite simulator look & feel, improve its resolution handling if possible and solve button overlaying issues.
* <span class="label label-success">DONE</span> Implement reasonable localization handling mechanism.
* <span class="label label-success">DONE</span> Add custom “tutorial” screen to application which will describe the whole concept (or its important parts) of the application to the user.
* <span class="label label-success">DONE</span> Port whole code base and build-specific things to Linux with testing on Archlinux.
* <span class="label label-warning">PARTIALLY DONE</span> Ensure that application is compilable and runs reasonably on Mac OS X and OS/2 operating systems and perform some testing on those platforms if possible.