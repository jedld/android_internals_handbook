ALL ABOUT ANDROID KERNELS
=========================

This section gives an overview about the linux kernel as used for Android in the context of tweakers and custom ROM porters.

Android and Linux
-----------------

The linux kernel used by android is usually starts out from an LTS mainline Linux version and then android specific changes are built out from there. Some changes are eventually merged into mainline linux. The version of linux that comes with your device depends on how old your device is and what version is used by aosp at that time. For 5.1.1 and 6.0 for example, it is based on Linux 3.10.y, Nougat devices usually go with 3.18, and 4.4.

There are a couple of android specific changes, the most important ones consist of the binder service, wakelocks and graphics services like ion. There are also certainly features that are disabled that are usually used in popular linux distributions, systemv IPC is an example from the top of my head. The android/configs/android-base.cfg and android/configs/android-recommended.cfg provides a list of flags needed for a kernel to support android. The actual configs that are used are found in the arch/cpu architecture/configs/device name_defconfig file. For the device I am porting it is in the arch/arm/configs/gtexstle_defconfig file. The kernel compilation process reads this file and uses it to enable/disable linux features approriately. Tinkerers that want to add features like supported filesystems usually just need to tweak this file after merging code from other kernels.

Aside from that, your manufacturer will also add device drivers and hardware specific modules needed by your hardware. As a rule you should obtain your kernel sources from the manufacturer of your device as those are already configured, at the very minimum, be able to boot your stock device.

As a device porter, you should first be comfortable with compiling an unmodified stock kernel and then flashing it to your device before making changes that can break things.

When google releases new iterations of Android, they can sometimes change feature that are required in the kernel and may require patches so be sure to watch out for those.
