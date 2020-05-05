# system-as-root
Android system-as-root
There are a lot of confusion regarding system-as-root (SAR) in general. SAR is introduced in Android 7.1 with Pixel 1st gen, mostly for A/B partitioning, but also for Project Treble. Let's call this "legacy SAR", or "LSAR" and see how it works.
The kernel will decide which root directory to use based on the cmdline sent from the bootloader. When booting into system, the kernel will mount system as "/" (hence system-as-root). When booting into recovery, it will boot with initramfs (ramdisk, included with the boot image).
LSAR is obviously specifically designed for A/B devices since they don't have recovery. As it turned out, other than Pixels, very few OEMs adopted A/B, and thus SAR is not widely used throughout the whole ecosystem. However, SAR is required for Project Treble to work properly.
For each Android version X, there are 2 sets of requirements.
1. All devices running X (let's call it U-X)
2. New devices launching with X (let's call it L-X)

This means old devices upgrading to X only need to follow U-X, not L-X.
Google made SAR part of L-P, which is why you see TONs of new devices (including S10) using SAR. For A/B devices, not much has changed, however for A-only, boot images no longer contain ramdisk, and the kernel will just directly mount system as root and boot from that.
In full Google fashion, they created yet another SAR implementation in Android 10!  \(^_^)/
It is called ***`"2-Stage-Init"`***, or let's shorten as 2SI. 2SI is required to support logical partitions as the kernel cannot mount the super blocks directly without userspace support.
The way 2SI works is that the kernel always boots with initramfs. Init in ramdisk will then decide how to boot the device. For A/B it can either mount system as root or start recovery based on a cmdline flag; for A-only, it will directly mount system as root and boot into system.
Google added SAR to U-Q, and 2SI to L-Q. 2SI is just one of the SAR impls, others for example are LSAR and Huawei's own BS. For existing devices using LSAR, they can still use it for Q. However for all non-SAR devices out there, the easiest way is to simply port 2SI for SAR.
So what is the current state of Android 10? For all existing SAR devices, you will still be using LSAR, with the exception of Pixel 3 as Google retrofitted it to use 2SI. All other devices (including custom ROMs) will be using 2SI as it is much easier to be ported over.
The current state of Magisk: it supports non-SAR (good old initramfs), LSAR (both A/B and A-only), and 2SI for A/B (currently Pixel 3 is the only one using this AFAIC). 2SI for A-only will come with the next release.

P.S. 2SI is a NIGHTMARE to work with, so yeah....
If you want to see the detail specifications of system-as-root, please check the official documentation from Google:
https://source.android.com/devices/bootloader/system-as-root
