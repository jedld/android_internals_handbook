An overview of Android Paritions
================================

This section describes how the Android directory structure and partitions is laid out. 
There are some notable changes across android versions. Those differences will be pointed out appropriately.

Note that the partitioning standard in an android device is usually manufacturer specific 
and how data is stored in flash isn't exactly standard.
As far as the main/internal storage device is concerned there is no standard partioning scheme unlike in PCs and other
devices. The external storage is a different matter as you can use standard tools to partition them.

A typical android device mostly contains the following *standard* partitions:

BOOT - Contains the linux kernel and ramdisk, as well as the the init files and selinux policies
RECOVERY - Like the boot partition it also contains a copy of the kernel and the recovery ramdisk. This is equivalent to some sort of "safe mode".
SYSTEM - A readonly partition. Contains the manufacturer and android system executables, shipped applications and platform libraries as well as various media assets.
DATA - A read/write partition. This is where your persistent user data is stored as well as downloaded and installed apps and their data.
CACHE - A scratchpad partition used by some apps. 
EFS - A read/write partition used by some hardware services.
