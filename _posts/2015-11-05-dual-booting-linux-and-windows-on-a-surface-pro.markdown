---

title: Dual-Booting Ubuntu and Windows on a Surface Pro
date: '2015-11-05 02:23:47'
---

In [my previous post](/running-portable-linux-on-a-surface-pro/), I said I didn't have room on my Surface Pro to install Ubuntu alongside Windows. Well, I decided I wanted to take the plunge, so I cleaned up some files and [removed the recovery partition](https://www.microsoft.com/surface/en-us/support/storage-files-and-folders/create-a-recovery-drive?os=windows-10) to free up space.

#Prerequisites

* [Download your favorite distro](/running-portable-linux-on-a-surface-pro/#step1downloadyourfavoritelinuxdistro) to a [bootable USB drive](/running-portable-linux-on-a-surface-pro/#step2createabootablelinuxusbdrive)
* [Disable Secure Boot](/running-portable-linux-on-a-surface-pro/#step3disablesecureboot)
* Make sure you have enough disk space (e.g. the Ubuntu installer requests ~30 GB by default and needs a minimum of ~10 GB)
* Download the following files from [version 3.16.7 of kernel](http://kernel.ubuntu.com/~kernel-ppa/mainline/v3.16.7-utopic/)
	* [linux-headers-3.16.7-031607-generic_3.16.7-031607.201410301735_amd64.deb](http://kernel.ubuntu.com/~kernel-ppa/mainline/v3.16.7-utopic/linux-headers-3.16.7-031607-generic_3.16.7-031607.201410301735_amd64.deb)
    * [linux-headers-3.16.7-031607_3.16.7-031607.201410301735_all.deb](http://kernel.ubuntu.com/~kernel-ppa/mainline/v3.16.7-utopic/linux-headers-3.16.7-031607_3.16.7-031607.201410301735_all.deb)
    * [linux-image-3.16.7-031607-generic_3.16.7-031607.201410301735_amd64.deb](http://kernel.ubuntu.com/~kernel-ppa/mainline/v3.16.7-utopic/linux-image-3.16.7-031607-generic_3.16.7-031607.201410301735_amd64.deb)
* Download the [Marvell wifi driver](http://git.marvell.com/?p=mwifiex-firmware.git;a=blob;f=mrvl/usb8797_uapsta.bin)

#Install Ubuntu

[Restart into Advanced Startup Options](/running-portable-linux-on-a-surface-pro/#step4restartintoadvancedstartupoptions) to boot from the USB drive and choose the option to install Ubuntu.

Follow the steps in the installer, but **DO NOT** connect to wifi. If you do, the installer will freeze randomly. It's OK to encrypt your home directory. Complete the installation and reboot.

After rebooting, still do not connect to wifi; otherwise, the Surface will freeze randomly when downloading data.

#Install Additional Kernel Packages

Open a terminal and navigate to the directory where you downloaded the kernel files above. Run the following command:

`sudo dpkg -i linux-*.deb`

Ubuntu 14.04 uses kernel version 3.19. Unfortunately, under this version of the kernel, the Surface will randomly freeze as described above. This issue does not occur with version 3.16.7 but starts occurring in version 3.17.x and up.

While this will install the packages, you will need to use the advanced settings option in the GRUB menu when booting to select them, as the default option is to boot with the newer version. Reboot again, making sure to select the 3.16 version of the kernel as described.

#Update the Marvell Wifi Driver

At this point, my Surface would no longer randomly freeze when connected to wifi, but I found that it would often disconnect or not connect to wifi at all.

To fix this, replace the existing Marvell wifi driver (usb8797_uapsta.bin) in /lib/firmware/mrvl with one downloaded from Marvel above.

Perform one last reboot. Wifi should now work!

#Expect Some Type Cover Wonkiness

Unfortunately, it's not all perfect. The biggest issue I noticed was occasionally the Type Cover keyboard or touchpad input were not recognized. Disconnecting it from the Surface, waiting a few seconds, and reconnecting it (wait a few seconds after reconnecting for the cursor to appear) always fixed the problem.

This seems to happen more often after having booted into Windows and then subsequently 
booting into Ubuntu. Multiple consecutive boots into Ubuntu typically work fine.

#Customize Your Install

The Surface should now be running Linux (mostly) smoothly. Go ahead and perform any updates, installations, and/or tweaks you want. 

A nice list can be found here: [http://howtoubuntu.org/things-to-do-after-installing-ubuntu-14-04-trusty-tahr](http://howtoubuntu.org/things-to-do-after-installing-ubuntu-14-04-trusty-tahr).

## Update GRUB boot default

By default, the Surface will boot into Ubuntu using the newer version of the Kernel (3.19). To avoid this, you can configure the GRUB bootloader to load version 3.16 automtically instead of having to manually select it each time. 

This can be done by editing the file /etc/default/grub. 

First, make a backup copy in case anything goes wrong:

`sudo cp /etc/default/grub /etc/default/grub.bak`

Next, open the file in a text editor: 

`sudo gedit /etc/default/grub`

There should be a line that says `GRUB_DEFAULT=0`. The "0" indicates that the first item in the GRUB bootloader list will be selected by default. 

Change this value to the exact text of an item in the list to change the default. For example: `GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 3.16.7-031607-generic"`.

The ">" indicates a sub-menu selection. Be sure to surround the text in quotation marks. It is also possible to use an ordinal value to change the default, but keep in mind that the order could possibly be changed by subsequent installs or updates.

Save your changes, and run the following command to update GRUB:

`sudo update-grub`

On the next reboot, the new option should be selected by default.

#References

That's it! You should be good to go to run Ubuntu on your Surface Pro.

Finally, I would like to credit several references that helped me through the process.

Ubuntu Surface Pro basics: [https://www.reddit.com/r/SurfaceLinux/comments/2b1hf6/running_ubuntu_1404_on_surface_pro_1_full_time/](https://www.reddit.com/r/SurfaceLinux/comments/2b1hf6/running_ubuntu_1404_on_surface_pro_1_full_time/)
Tip to replace wifi driver: [http://www.geek.com/microsoft/how-to-install-ubuntu-on-the-surface-pro-1539262/](http://www.geek.com/microsoft/how-to-install-ubuntu-on-the-surface-pro-1539262/)
GRUB defaults: [http://askubuntu.com/a/149494](http://askubuntu.com/a/149494)







