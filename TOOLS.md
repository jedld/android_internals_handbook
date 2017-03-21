Tools of the Trade
==================

A guide on the essential tools for building your own custom ROM or tinkering with your device. This guide will start from the most basic of tools to the most advanced depending on what you intend to do. First we start with your choice of operating system.

Operating System
================

Personally a popular distribution is linux is ideal for any form of android development, either for apps or for hacking and tinkering with your device. Most of the guides that deal with this usually contain instructions for linux distributions. I personally use ubuntu as it is a popular distribution and since many in the community also use this you can find a lot of resources tailored for it.

MacOS is also another system which may be good for android development as well though I personally don't have as much experience with this as much.

Windows can be used for tinkering at some level and android app development is supported on it, however if you intend to develop custom ROMs, this may be diffcult. You can however opt to install a linux environment inside windows using virtual box or by deploying a docker instance inside it.

Basic Tools
===========

1. ADB (Android debug bridge) - Also you to shell into your device, install/unistall apps, get logs, push/pull files etc.

For android development, adb is the most basic of the basic tools that you need. Note that this tool automatically comes with android studio once you setup the android adk manager. You can also install this separately, depending on your operating system. You can also compile this from source when you download the AOSP (Android Open Source) repo or even CyanogenMod/LineageOS repos.

