# The Android Internals Handbook

This is a handbook aimed towards Android System level development which focuses on "behind the scenes" operation like filesystem, bootup, partitions and porting, as opposed to android application level development which focuses on Applications, NDK and the android APIs. 

There seems to be a complete lack of an integrated document describing how android is configured, and set up on various devices. Google provides various documentation at a high level for AOSP, however, it is naive to assume you know exactly how the device on your hand right now is configured to operate.

As various devices get older, most manufacturers won't even bother to update the version of android on those devices. It is mostly up to enthusiasts like those in xda developers to carry on the torch as you might say. However those who do which to carry that torch have a lot of reverse engineering and research that they need to do to get started.

This document is intended for developers that want to tweak their system even more than what the manufactures and google might allow. 

Note that this is based on my experience on porting cyanogenmod to the Samsung Galaxy Tab A 2017 (SM-T285). I've experienced everything from rooting this device up to building the first custom ROM for it. So I hope this will be useful.

I have arranged the contents of this guide, according to the appropriate order required for you to be able to root up to building your first custom ROM. I went into this knowing nothing so I am confident even those with no experience with porting Android to be able to do this.

Most of my experience may be unique to Samsung and the Spreadtrum chipset, though I have deveoped and compiled code for the Nexus series and the OnePlus 3T, I will try to call out if it something manufacturer specific. As to the specific android version, I only have experience with android 5.1 - 6.0 and 7.1 based ROMs, but I will try to callout changes introduced if I am aware of it.

# Versions

1.0 - Initial Version

# Table of contents

1. [Introduction](#introduction)
2. [Getting to know your device](#Getting to know your device)
  1. [CPU Architecutre and Chipset](#CPU Architecture and Chipset)
3. [Detailed overview of the android bootup process](#bootup)
  1. [Partitions!](#Partitions)
4. [Flashing your device](#flashing)
  1. [Samsung](#samsung_flash)
  2. [Nexuses](#nexus_flash)
  
## Before you start

Make sure you also read [Tools of the trade](https://github.com/jedld/android_internals_handbook/blob/master/TOOLS.md)

## Getting to know your device

The first step in hacking your device is getting to know it. But what exactly do you need to know? Here is a quick rundown and then we will go through techniques in order to figure those out:

### CPU Architecture and Chipset

Knowing what CPU and chipset is essential as this is one of the parameters that you will need when making a BoardConfig file (The BoardConfig file is a type of makefile and is one of the file used to tailor an android compilation environment to a specific device).

Ideally, you can get this from your devices specsheet, there are only two architectures popular right now for mobile namely arm and Intel with arm being the vast majority of it. In fact, most likely you would have an arm variant. For arm variants we can further subdivide into arm and arm64. arm64 (or arm v8) is used in the newer devices, while arm (32-bit) is used in older devices and even newer entry-level devices. It is important to know the CPU brand as well so that it is easier to hunt for drivers and looking up device trees of devices with similar hardware. Examples to look for are sc8830 for spreadtrum, Snapdragon 821, Exynos, Intel Atom for example.

Next would be the chipset. The chipset is basically the motherboard where your cpu is soldered on and contains other hardware chips like the accelerometer, modem, wifi and camera chips for example. The chipset may not even refer to the motherboard at all as we now have System on a chip solutions which combines all other hardware features into one chip. Examples of chipsets are the sc9830 from spreadtrum, Qualcomm MSM8996.

You can extract most of this information from build.prop, if the kernel source code of your device is available, you can look at the kernels defconfig file.

Detailed overview of the android bootup process
===============================================

How exactly does your phone go from off to your homescreen? Seems so simple but there is actually a lot of things going on. In fact there is so many things going on that a vast majority of Cyanogenmod and custom ROM porters spend a majority of their time getting this work right on their supported devices. Note that there are slight variations between different hardware depending on how manufacturers tweak it, but most of the concept is the same.

1. Hardware Initialization and startup
2. The init process

Hardware initialization and startup
-----------------------------------

When you turn your device on and your devices firmware starts its bootup sequence via a bootloader (Note that firmware refers to the actual manufacturer firmware and not the android related code). What happens here is device specific, on nexus devices this is sometimes called the fastboot sequence. But after it does it's thing, it usually loads code in the boot partition. The boot partition is not a normal partition that you can mount, it is actually composed of two files, a compressed linux kernel and the ramdisk. The ramdisk is a compressed set of files that will form the root filesystem of your device. Note that since the content of the root filesystem is unzipped in ram and mounted as a ramdisk, you can't technically change its content, in order to change it you need to unzip the ramdisk files and then zip it again and reflash the boot partition. When a mod in xda requires changes to the kernel and files in the bootloader, they all have to go through this process somehow.

The aosp tools mkbootimg are used to build the boot.img and recovery.img in most AOSP based ROMs, though there are certain devices that have their own boot.img formats **cough** samsung.

Do note that the recovery partition has exactly the same format as the boot partition with both having the kernel and ramdisk files. The only difference is in the way servcies are started. In fact, the recovery image actually has almost the same copy of the kernel that the boot partition has and in some cases they are the same, the only difference is that the recovery partition only boots the recovery program e.g. CM Recovery, TWRP, ClockworkMod etc. Since the recovery partition has a simpler startup process than the boot partition most custom ROM porters usually start making custom recovery work first.

The init process
----------------

After the bootloader loads the linux kernel and ramdisk into memory. Two things happen, the linux startup sequence gets called and then the init app gets called (yes init is an app at /init). If your device bootloops or freezes without even emitting a single line of logcat, then any of these two might be the culprit. Please note that the init used by android is nowhere related to the init.d daemon used by other linux distributions, it is a completely new thing developed by google specifically for use with android. There may be some custom ROMs that attempt to port over init.d but this is beyond the scope of this document. 

Note: Also keep in mind that the linux kernel used by android is currently not mainline, meaning it has modifications that you would not otherwise find on your vanilla linux distribution, there have been attempts to "upstream" this, though I'm not sure what has happened to it at this point in time.

The init application is actually quite simple if you think about it, what it does is it loads all the *.rc files and attempt to start services and execute commands that are defined in those files. The source code for init can be found in system/core/init in the aosp sources. The init process can be thought of as having 3 primary goals:

1. Prepare the filesystem (permissions, setup directores etc.)
2. Start the android core services and proprietary manufacturer daemons (gps, cellular etc.)
3. Start the android appliation framework and load the System UI

Note: If a problem happens during the startup sequence you can get a bootloop or your boot animation just hangs. In the succeeding chapters we will go over those services one by one and see how they impact custom ROM porting. Some of the services just works across all devices while some need tweaks for it to work.

Note: SELinux - On newer android releases, selinux gets initialized somewhere between the linux startup and before init gets called. Selinux is a security enhancement (initially developed by the NSA I believe) that works on top of the standard linux permissions and allows you to define allowed operations on a resource on a per app/service basis. Literature on how this works on android is depressingly lacking and I will tackle this on a separate section. When you are just starting to port over a ROM you usually need to disable SELinx first as it will prevent incorrectly configured services from working properly since it will reject access to resources a service does not have access to.

Overview of the *.rc files
--------------------------

A working knowledge of the *.rc files is required if you are into porting ROMs, unfortunately unless you are into reading source code, literature on how .rc work is sorely lacking. Though I will provide a gist of how they work. As the rc files are in the root filesystem (the ramdisk), modifying this requires a reflash of the boot partition. Note that Android Nougat has made some small changes on how these files are organized though the working mechanism still stays the same.

In a nutshell the init.rc file is the first file that gets loaded and parsed. Note that at this stage the init.rc file and its included files are simply loaded and parsed in memory before execution. The init.rc files and its included files define scripts that are executed based on hooks that are triggered by various system events. The system events can be thought of as various states that the system is currently in the boot process. Once certain criteria is met, the boot process moves on to the next state and starts up services and executes commands that are defined in the that state and so on until it reaches the last state. The init script allows for various simple shell-like commands like copy, move, softlink, chmod etc., however do note that these do not actually call out to a shell command and is implemented inline by the init process itself. You will also notice some commands that deal with the property service like setprop and getprop, these "properties" are used extensively by various system services as well as the android framework itself, in fact if you follow some xda mods you will notice that a lot of them deal with modifying these properties either through build.prop or the command line equivalent setprop.

Core System Services Guide
==========================

There may be other important manufacturer specific services, but the android part is described below, you should see one or all of this being started by the init process:

* rild - daemon that interfaces with the manufacturer specific radio hardware
* vold - daemon that handles mounting of storage devices
* zygote - aka app_process32/64 - The android runtime, that gets forked per app process. This usually runs last. You're custom ROM is usually good to go if it reaches this part.
* bootanimation - A service that gets started to display the boot animation. If this works in your custom ROM you at least got the most important parts of the display framework working.
* surfaceflinger - A service that handles sending of buffers to a virtual or physical display. These can be opengl buffers or software buffers.
