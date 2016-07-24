---
layout: post
title: Amazing three months
author: Martin Rotter
date: 2014-08-19 08:00:00 +0200
category: gsoc-2014
language: en
imgfolder: /assets/2014/08/gsoc-summary
images:
  - name: toolkit-1.png
    text: 
  - name: toolkit-2.png
    text: 
  - name: toolkit-3.png
    text: 
---

Four (maybe five) months ago, I learnt from friend of mine, Jan Grulich,  that GSoC is really program for me.
<!--more-->

I was so excited when I decided to give it a try. I must say that I was already part of OSS world, because I developed some free software before, including:

* RSS Guard,
* Qonverter,
* and other stuff.

So I have some knowledge about OSS mechanisms. GSoC was another huge experience to enhance my OSS understanding and skills.

### Searching the right project

Well, one could say it's easy, but it is not. Tens of organizations were selected as objects of  and each organization does different stuff, including web browsers, mathematical software, educational applications or desktop environments. Problem is that there is too many of possible tasks you can do. Each organization usually publishes a list of recommended tasks for students to do during GSoC but everybody can come with his/her own idea or proposal.

That was not my case. I went through a list of participating organizations and I saw one with interesting name, BuildmLearn. "What does that word mean?" I said to me. I was curoius and excited when I found out that this particular organization creates software for education purposes as I have always found teaching interesting job.

I went throught tasks their offered and I saw task "Porting BuildmLearn Toolkit". I always liked to fix written code. I took a look at description of the task and said "WOW, this is it." The original software which was required to port is written in Qt, the toolkit I love. I contacted BuildmLearn people by e-mail, specifically Pankaj Nathani (who eventually became my mentor), and started to think about the task. It is always important to come with some new ideas and expect constructive brainstorming.

BuildmLearn Toolkit is simple application which allows its users to create mobile applications of predefined types without programming knowledge. It is some kind of WYSIWIG application designer. User provides contents of an application and Toolkit takes care of the rest of the process.

So I started to be active in BuildmLearn communication channels, primarily Google Groups mailing list, where I described my intentions, what I planned to do during my GSoC task, how would I port the BuildmLearn Toolkit and so on. Here is complete text of my GSoC proposal (the original one, even with already dead links).

```text
Who am I? What are my hobbies, education, projects?

My name is Martin Rotter and I come from Czech Republic, the heart of Europe. I am 24 years old, studying at VŠB-TUO Ostrava. It is technical university. My previous education includes secondary electrotechnical school and UPOL Olomouc, where I received Bc. Degree in field Applied Computer Science. So I have been studying technical things for many years so far.

My professional interests are not broad, but rather more specific. I focus on desktop applications development including:
 - Windows/Linux applications development, 
 - focus on application workflow and user-friendliness,
 - development of HIG-compliant applications.

I consider GUI development to be a real science as it demands quite a research. Beside that, I focus on algorithm construction (primarily array/list based algorithms or tree algorithms). I use Archlinux Linux distribution and Windows for my work. Generally, I love everything Qt-related. I am simply Qt enthusiast. For my programming and design, I have used Qt for more than 5 years. More complete information about technologies and languages I master can be found in my CV.

My personal e-mail address is rotter.martinos@gmail.com. My personal website is [http://www.martin-rotter.8u.cz](http://www.martin-rotter.8u.cz) . Previously I worked on some projects, including QtDesktop. I am leader of QGothic, Qonverter and RSS Guard projects. RSS Guard is my last piece of work. You can find broader list of my past and active projects in my CV.

What are the basic ideas and intentions?

We must take the size of current toolkit. It is rather young project and code base is small. That is good, because at this point we can make changes with big positive impact quite easily and fast. That impacts the goals of my idea:
 - The project uses QMake as build system at the moment. Let's replace it with CMake. What it can bring us? Among other things, it brings us: 
 - Better control of project files when project gets larger.
 - Ability to use CMake and custom variables throughout the project to define basic variable which describe the project, including application name, version, url, file paths, conditional compilation. This can hugely benefit the code base which will be better structured and easier-to-understand. All constants are defined just ONCE and can be used in C++ code as well as in other parts of the project.
 - Good support for auto-creation of installers (NSIS) on Windows platform, which are supported in CMake.
 - Components-based compilation, user can specify which parts of software he/she wants to compile and which not.
 - Project uses QRC embedded binary files to reference external resources. Let's use all external resources directly from file stored on disk so that they can be replaced without the need of application recompilation. This is the matter of good design.
 - Restructure the overall layout of sources within the project, clearly separate the application logic core from user interface, separate the templates:
 - Separate templates into logical units (we could use file-based modules - DLL files on Windows, SO files on Linux, other file formats on Mac OS X) but here I will prefer to separate the templates on source-code level by creating the interface they will use. This will make writing of new templates more straightforward.
 - Tweak code style and conventions we use to match general Qt code style.
 - Implement custom application-wide settings mechanism which is needed for every serious applications and allows us to have “portable” application which can be run for example from USB stick.
 - Implement basic "check for updates" feature (user is notified about existince of newer stable version than he/she currently uses). This will stabilize the community of user of our application.
 - Implement custom application icons handling mechanism (the one provided by Qt is sometimes faulty).
 - Separate all inline Qt stylesheets in separate mechanism and implement XML based “skin” mechanism.
 - Some previous points are essential to application of this type and they consists of a way to make this application to look and behave standardly.
 - Refactor the code base. Remove deprecated API usage, use fixed-size modal dialogs on Windows platform and fix some QLayout-related memory leaks.
 - Enforce correct QObject-parent usage throughout the code base.
 - Improve simulator look & feel, improve its resolution handling and solve button overlaying issues.
 - Define API (set of interfaces) for templates.
 - Ensure that application runs with Qt5 as well as with Qt4 and make this option changeable via CMake - better suportability for the future.
 - Implement reasonable localization handling mechanism.
 - Add custom “tutorial” screen to application which will describe the whole concept (or its important parts) of the application to the user.
 - Use Doxygen to comment important blocks of source code.
 - Port whole code base and build-specific things to Linux with testing on Archlinux.
 - Ensure that application is compilable and runs reasonably on Mac OS X and OS2 operating systems and perform some testing on those platforms if possible.
 - Implement supporting mechanisms of the project:
 - Create working scripts for openSUSE Build Service which will allow as to build packages for most used Linux distributions. Example can be seen here. Implement that for “stable” and “development” release of the toolkit if desired.
 - Establish mechanisms for semi-automatic creation of installation files on Windows (via CMake and NSIS). 
 - Implement localization files auto-refresh into CMake build script.
 - Support semi-automatic binary tarball creation via Cmake.
 - Ensure application behaves correctly on all ported platforms, primarily Windows, Linux.

What is the schedule of this?

This could be my first GsoC participation ever. Basically, I have three months to code this away and it is perfectly doable. I could separate the work points I summarized into TWO logic parts:
1. Establishing needed CMake build facilities (including tranlations regeneration, components-based build, global variables handling etc.) and porting the project to Linux and OS2. Implement openSUSE Build Service scripts and possibly implement NSIS script for creating Windows installators (ZIP archives could be enough for phase 1 possibly). After that refactor the whole code base. This phase is important as it reworks the application to new build system, clears the memory leaks, removes unnecessary code and makes it running on Linux and OS2. This is absolutely crucial before we make any further arrangements.
2. Build on phase 1 and implement the new application logic blocks – settings, skin/icon handling, interfaces for templates, enhance existing templates to match application layout and implement rest of features.

From my point of view, phase 1 is pretty-much perfect set of points to be done for mid-term evaluation as it offers us some room for changes and compromises. Second phase will be more inovative part which is good for final evaluation. Remember, that we need to stabilize the source code base in phase 1 before we do any innovative work in phase 2!

Do I have time for doing this?

Let me put the facts on the table. I already have full-time job and I study (part-time) university. I can spend around 22 hours a week on this project and I honestly believe it is enough to pass the task points. I believe that the changes I propose will move the toolkit into safe, maintainable state. It is important to make crucial adjustments and changes now. It is easier to change and improve young project than rewriting it when it has 100 000 lines or more.

Do I have to say any other facts?

Yes, I do. Your organization is the only one I want to make my proposal for. This is my first GSoC experience ever. I generally like the ideas behind this application as it can be very useful for example for teachers. Moreover, it is written using Qt and that is the fact I love about this project.
```

Quite long proposal, right? When writting proposal, alway provide as much relevant information as possible.

I also started to play with BuildmLearn Toolkit source code to actually see what I should improve and change. Good thing was that the code was still quite small, so it was easier to rewrite and refactor it.

I had to wait if BuildmLearn organization will pick me but guess what, they did. I was selected from more than 250 applicants! Exciting journey was started.

### Daily routine and mid-term evaluation

It is incredible important to communicate with your mentor. You must take time offset into account. I am located in Czech Republic, Europe while my mentor is located in India, Asia. Note that time difference betweene these two locations is (more or less) 5 hours. So If you start working on your GSoC stuff on late afternoon, your mentor might be already sleeping!

It is good to communicate with your mentor frequently. You must have courage to ask right questions and request advices but you have to be self-reliant and self-confident in the same time.

I preferably like to use some IM and I did this with my mentor too. I concentrated my GSoC job time for forenoons or early afternoons so that I can catch my mentor online.

Anyway, during the first phase of my GSoC task, we made clear goal to do basic rewritting and porting of BuildmLearn Toolkit. Specified goals were met. New Toolkit had essentially the same features as original one but the source code was much better - readable and maintainable.

I was little bit nervous about mid-term evaluation, but my nerouvseness proved to be unnecessary. I passed the evaluation and I was happy.

### Phase two - adding fancy features

Phase two of my task was to add one more template to the Toolkit. Template for learning to spell words. I must say that some disagreements appeared, but you have to know one thing. If misunderstanding or disagreement appears, then solve it with your mentor via constructive discussion. Both parties must be happy about the result, you and your organization.

So we added one extra template and Toolkit now has 4 of them. The next feature was quite unplanned (I was not informed about it before GSoC and it does not appear in my GSoC proposal too.) but I decided to include it because we were much ahead of our schedule. Always (at least roughly) plan your next week, plan what to code, what to test and what to document. Share your ideas and plans with your mentor. It is important for him/her to know, what you are doing and planning to do.

So I added feature to upload mobile applications to special cloud storage called BuildmLearn Store. The Store will be used in the future.

I was very happy, that all stuff was implemented and rougly tested before the deadline. If you know, you will not catch some deadline, inform your mentor. You must deliver both good and bad messages to him/her.

We ported BuildmLearn Toolkit to Windows, Linux, OS/2 and Mac OS X, which is great achievement, which I am proud of.

###  internals

Let me write few things about the GSoC organization itself. Important part of the GSoC is money and the way they are delivered to you.  If you are successfull, then you are paid for your GSoC effort. In previous years, payments were usually made via special prepaid "VISA" bank card which was sent to every participant. I must say I do not like this way so I was hoping that this year will be different. And it was. There was now option to get your reward via ordinary bank account money transfer. SUPERB! This way is much more comfortable for anyone. All relevant information is always delivered to your mailbox which you use for your GSoC registration. It is important to read all GSoC-related e-mails.

### Links

You can see pictures for this blog post <a id="gal" href="#">here</a>.

{% include gallery.html id="gal" %}

See all related links to my GSoC work and other stuff:

* www.github.com/BuildmLearn/BuildmLearn-Toolkit  ~BuildmLearn Toolkit source code,
* www.buildmlearn.org  ~BuildmLearn homepage,
* www.buildmlearn.github.io/BuildmLearn-Toolkit/docs  ~BuildmLearn Toolkit reference documentation.

### Conclusions

I got married during GSoC. That fact along with GSoC participation made my summer perfect.