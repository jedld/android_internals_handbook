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

## Getting to know your device

The first step in hacking your device is getting to know it. But what exactly do you need to know? Here is a quick rundown and then we will go through techniques in order to figure those out:

### CPU Architecture and Chipset

Knowing what CPU and chipset is essential as this is one of the parameters that you will need when making a BoardConfig file (The BoardConfig file is a type of makefile and is one of the file used to tailor an android compilation environment to a specific device).

Ideally, you can get this from your devices specsheet, there are only two architectures popular right now for mobile namely arm and Intel with arm being the vast majority of it. In fact, most likely you would have an arm variant. For arm variants we can further subdivide into arm and arm64. arm64 (or arm v8) is used in the newer devices, while arm (32-bit) is used in older devices and even newer entry-level devices. It is important to know the CPU brand as well so that it is easier to hunt for drivers and looking up device trees of devices with similar hardware. Examples to look for are sc8830 for spreadtrum, Snapdragon 821, Exynos, Intel Atom for example.

Next would be the chipset. The chipset is basically the motherboard where your cpu is soldered on and contains other hardware chips like the accelerometer, modem, wifi and camera chips for example. The chipset may not even refer to the motherboard at all as we now have System on a chip solutions which combines all other hardware features into one chip. Examples of chipsets are the sc9830 from spreadtrum, Qualcomm MSM8996.

You can extract most of this information from build.prop, if the kernel source code of your device is available, you can look at the kernels defconfig file.

Detailed overview of the android bootup process
-----------------------------------------------

How exactly does your phone go from off to your homescreen? Seems so simple but there is actually a lot of things going on. In fact there is so many things going on that a vast majority of Cyanogenmod and custom ROM porters spend a majority of their time getting this work right on their supported devices. Note that there are slight variations between different hardware depending on how manufacturers tweak it, but most of the concept is the same.

1. Hardware Initialization and startup
2. The init process

Hardware initialization and startup
==================================

When you turn your device on and your devices firmware starts its bootup sequence via a bootloader (Note that firmware refers to the actual manufacturer firmware and not the android related code). What happens here is device specific, on nexus devices this is sometimes called the fastboot sequence. But after it does it's thing, it usually loads code in the boot partition. The boot partition is not a normal partition that you can mount, it is actually composed of two files, a compressed linux kernel and the ramdisk. The ramdisk is a compressed set of files that will form the root filesystem of your device. Note that since the content of the root filesystem is unzipped in ram and mounted as a ramdisk, you can't technically change its content, in order to change it you need to unzip the ramdisk files and then zip it again and reflash the boot partition. When a mod in xda requires changes to the kernel and files in the bootloader, they all have to go through this process somehow.




