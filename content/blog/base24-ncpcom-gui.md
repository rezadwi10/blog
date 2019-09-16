+++
title = "Base24 NCPCOM GUI"
date = 2019-09-11T17:34:30+07:00
draft = false
showonlyimage = false
type = "post"
categories = ["Base24"]
featured = "/posts/base24.png"
author = "Reza Dwi"
+++

Starting this year on 2019, I've been working on switching system development environment for almost 9 months.<!--more-->
It was pretty new to me, all the syntaxes, utilities and development strategies. One of the most common utitilites
we usualy use for development is NCPCOM (XPNET).

XPNET is the central nervous system of all BASE24 payment solutions running on HPE NonStop. 
XPNET managing all driving data communications, controlling stations, lines and processes defined to it.
It also routing transactions, managing transactions message queues, running system traces, generating and writing
event messages to EMS (EMSPERUS), and so on.

And to operate all of them, we use NCPCOM as the command interface to XPNET. NCPCOM stands for Network Control Point
Communications, used to configure, operate, and monitor a XPNET environment.

As long as I use this util for developement, it contains several repetitive commands we usually do, such as to start and stop stations,
lines or processes, changing parameter and tracing the event messages. 
So, I developed a dotnet winforms application to interpreted all those commands to GUI using telnet program.
Here's a preview:

![NCPCOM][1]

It contains basic commands I mentioned eariler.

> based on what i usualy do

It has lot of works to do, if you interested to try or maybe has an idea to new feature, 
reach me out through email for access to the repo.

[1]: /img/posts/ncpcom-gui.png