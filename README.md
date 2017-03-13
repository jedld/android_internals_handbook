# The Android Internals Handbook

This is a handbook aimed towards Android System level development whicih focuses on "behind the scenes" operation like filesystem, bootup, partitions and porting, as opposed to android application level development which focuses on Applications, NDK and the android APIs. 

There seems to be a complete lack of an integrated document describing how android is configured, and set up on various devices. Google provides various documentation at a high level for AOSP, however, it is naive to assume you know exactly how the device on your hand right now is configured to operate.

As various devices get older, most manufacturers won't even bother to update the version of android on those devices. It is mostly up to enthusiasts like those in xda developers to carry on the torch as you might say. However those who do which to carry that torch have a lot of reverse engineering and research that they need to do to get started.

This document is intended for developers that want to tweak their system even more than what the manufactures and google might allow. 

Note that this is based on my experience on porting cyanogenmod to the Samsung Galaxy Tab A 2017 (SM-T285). I've experienced everything from rooting this device up to building the first custom ROM for it. So I hope this will be useful.

I have arranged the contents of this guide, according to the appropriate order required for you to be able to root up to building your first custom ROM. I went into this knowing nothing so I am confident even those with no experience with porting Android to be able to do this.

Table of Contents
-----------------
