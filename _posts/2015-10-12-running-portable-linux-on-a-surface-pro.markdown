---

title: Running Portable Linux on a Surface Pro
date: '2015-10-12 02:43:32'
---

I have an original Surface Pro that I got a couple years ago. I thought it would be fun to install Linux on it, but I've filled up most of the available storage. Rather than trying to create space for a dual-boot setup, I decided to configure it to run Linux from a bootable USB drive. 

The steps for installing Linux on a Surface Pro should be the same, as the bootable USB should offer an option to actually perform an installation in addition to booting into Linux.

###Step 1 - Download your favorite Linux distro

Download an .iso of your desired Linux distribution (I used [Ubuntu](http://www.ubuntu.com/download/desktop)). Make sure you choose a 64-bit version. The first time I tried this, I used a 32-bit version and the Surface did not recognize the bootable USB drive. Kick off your download and proceed to Step 2.

###Step 2 - Create a bootable Linux USB drive

The easiest way to do this is head over to [pendrivelinux.com](http://www.pendrivelinux.com) and download the Universal USB Installer (UUI). Once download completes, run the .exe and follow the instructions. There should be three steps: pick the type of Linux distro, specify the location of the .iso file that you downloaded in Step 1, and choose the USB drive where it should be installed. Kick off the installer and in a few minutes, you will have a bootable Linux USB drive. 

###Step 3 - Disable Secure Boot

Now it's time to tweak the Surface configuration. First, shut it down. With the Surface powered off, press and hold the volume up button while pressing the power button. Keep both buttons pressed until you see a black screen with with text. Use the arrow keys to select the Secure Boot option and press enter to toggle this option from "Enabled" to "Disabled". 

**WARNING:** If you have BitLocker Drive Encryption set up, make sure you have your Recovery Key! Once you disable Secure Boot you may be prompted to enter the key on a subsequent reboot. If you do this and don't have the Recovery Key, I assume your data will be unrecoverable. I keep mine in a safe place so I did not have to find out :)

Save and exit this screen, and the Surface will reboot. Don't be alarmed if the startup screen is now a different color (mine is a bright orange-red). 

###Step 4 - Restart into Advanced Startup Options

Log into Windows and go the Settings --> Advanced Startup screen. Make sure your bootable USB drive is plugged in, and click the button to restart in Advanced Startup options. You should restart into a light blue screen with an option to boot from the USB drive. On my first attempt, I did not have the boot from USB option, which I am assuming was the result of installing a 32-bit version of Linux on the USB drive. Click the boot from USB option.

###Step 5 - Boot into Linux

The Surface should now boot into your chosen flavor of Linux. I used Ubuntu and had several options: Try Ubuntu, install Ubuntu, etc. On Ubuntu, choosing the "try" option boots straight into Linux. You should now be running a fully-functional portable version of Linux on your Surface. Enjoy!






