SELINUX IN ANDROID (AND HOW IT WORKS WITH BUILDING A CUSTOM ROM)
----------------------------------------------------------------

Selinux or security enhanced linux was introduced in android 4.4 but fully implemented (and required) in Android 5.0+.
Selinux is a required feature in your kernel. Even if you just plan to use a permissive kernel, binaries depend
on it existing even if you don't plan to use it.

INTRODUCTION
============

Selinux defines permissions on system resources like files and sockets as well as abilities (e.g. execute) on a 
per process level using selinux labels. A list of permissions allowed is defined in an selinux
a policy file. In android the selinux policy file is located in the root filesystem /sepolicy and is bundled as part of the ramdisk. The recovery partition can also have these files. There are also other files
like file_contexts, property_contexts, service_contexts etc. In most manufacturer stock versions of android
these files are signed, which means even if you have write access to those files you can't change it without
causing your phone to fail to boot. For android 8.0 these files are further subdivied into vendor and platform policy files.

When building a ROM you provide .te policy definition files which is used by the android build system to generate those policy files.
The scripts and package that does this can be found under the system/policy folder in your AOSP or LOS source tree (android 7.0)

Under your BoardConfig.mk file your specify these files using the BOARD_SEPOLICY_DIRS build variable. Note that there is 
a heirarchy of these policy files, the first is the one provided under AOSP, Lineage and then ultimately the files that
you provide for your device. In theory the only policies you need to define are those specific to your device like those
dealing with drivers and manufacturer specific binaries.

Building SEPOLICY notes
=======================

When first building your kernel, it is best to start with a permissive one, so that it is easier to diagnose boot issues. When you are sure that your device works, you can then start to fix selinux errors one by one. For most kernels you can set the kernel boot selinux parameters. If you have access to the kernel source, you can also set it there. For some samsung kernels, there is a flag that force enables SELINUX to enforcing, so you may need to change that during kernel compilation.

COMPILING SEPOLICY FILES
========================

The sepolicy files is part of the main build process. But you can build them separately, in your LOS/aosp work environment you can go to ~/android-work/system/sepolicy and execute mm
