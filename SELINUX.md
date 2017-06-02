SELINUX IN ANDROID (AND HOW IT WORKS WITH BUILDING A CUSTOM ROM)
----------------------------------------------------------------

Selinux or security enhanced linux was introduced in android 4.4 but fully implemented in Android 5.0+.
Selinux is a required feature in your kernel. if you are building one as android libraries and binaries depend
on it existing even if you don't plan to use it.

INTRODUCTION
============

Selinux defines permissions on system resources like files and sockets as well as abilities (e.g. execute) on a 
per process level using selinux labels. A list of permissions allowed is defined in an selinux
a policy file. In android the selinux policy file is located in the /sepolicy file or which means it
is part of the boot partition. The recovery partition can also have these files. There are also other files
like file_contexts, property_contexts, service_contexts etc. In most manufacturer stock versions of android
these files are signed, which means even if you have write access to those files you can't change it without
causing your phone to fail to boot.

When building a ROM you provide .te policy definition files which is used by the android build system to generate those policy files.
The scripts and package that does this can be found under the system/policy folder in your AOSP or LOS source tree (android 7.0)
Under your BoardConfig.mk file your specify these files using the BOARD_SEPOLICY_DIRS build variable. Note that there is 
a heirarchy of these policy files, the first is the one provided under AOSP, Lineage and then ultimately the files that
you provided for your device. In theory the only policies you need to define are those specific to your device like those
dealing with drivers and manufacturer specific binaries.
