---

title: Breathing New Life Into an Old Laptop with an SSD
date: '2015-03-24 02:10:48'
---

# $50 for an SSD, Why Not?
Recently, I caught a sale where Crucial was unloading refurbished SSD drives. I have a couple of old laptops (circa 2007 or so), one of which still gets a fair amount of use but whose speed had slowed considerably. Seeing an opportunity to do some tinkering, I grabbed a 256 GB SSD for $50.

# Make (and Check!) Your Backups
Once the SSD arrived, I backed up all the files on the old hard drive to an external drive. Naturally, I gave it a quick glance to verify the expected directory structure was there and moved on.

**Don't do this!** Dig deeper into the file system and verify the files you expect to be there have actually been copied. For whatever reason, I copied the directory structure, but not the files themselves. I was prompted about a file that was in use that could not be copied&mdash;which I told it to skip&mdash;but apparently it did not proceed with the copy.

Since I wasn't wiping the old drive, no harm ensued, but it did cost me some time later on when I had to put the old drive back in and copy the files.

# Swapping the Drives
Check the manual that came with the laptop to see where the hard drive is located. If you don't have the manual, there are probably only a couple of possibilities, so pick a door and see what's behind it. [You can always switch to a different door](http://en.wikipedia.org/wiki/Monty_Hall_problem). The only tool I needed was a small screwdriver. Slide the old drive out and pop the new one in.

# Installing Windows on the New Drive
Since the laptop had already been running slowly and it did not have a lot of installed software, I decided to start with a fresh Windows install. I did not have a Windows install DVD handy, but since I have an MSDN subscription, I downloaded a Windows 7 ISO from there.

If you have a product key but have lost your install CD or DVD, you should be able to use [Microsoft Software Recovery](http://www.microsoft.com/en-us/software-recovery) to download it.

If you have a DVD, pop it in and if necessary, change your BIOS settings to boot from the CD/DVD drive, and follow the prompts. Changing the BIOS settings varies from computer to computer, but [this](https://www.google.com/search?q=change+bios+settings) should get you pointed in the right direction.

Rather than burn a DVD, I decided to create a bootable USB drive from the ISO file I downloaded using the [Windows 7 USB/DVD Download Tool](http://wudt.codeplex.com/). This requires formatting the USB, so **do not use the drive you backed up your files to!** Use a different USB drive that you can afford to wipe completely.

Run the tool and follow the prompts. After the process finishes, you may receive the following error message:

*"Files copied successfully. However, we were unable to run bootsect to make the USB device bootable. If you need assistance with bootsect, please click the "Online Help" link above for more information."*

If you receive this error, try booting from the USB drive--it [might just work](http://superuser.com/a/416727)! If not, perform the steps described in [this Superuser answer](http://superuser.com/a/464616):

	Launch a command prompt with admin rights and run the diskpart tool:

	diskpart
	list disk
	select disk #
	clean
	create partition primary
	select partition 1
	active
	format quick fs=fat32
	assign
	exit

Run the tool again. I still received the same error message, but this time I was able to successfully boot from the USB drive and begin installing Windows.

# Smooth Sailing
At this point, I just sat back, relaxed, and waited for Windows to do its thing. Surprisingly, the initial install did not take nearly as long as I expected (thanks SSD!).

Nevertheless, once Windows is installed it will most likely have many, many updates to download and install. I got Windows Update started and then headed outside for a walk.

# Some Time Later...
Once the updates finished, I copied the files from the backup I made to the new hard drive.

This was the point where I realized I had not actually copied any files to my external drive and had to swap the old hard drive back in again to get the files (*sigh*).

I would highly recommend purchasing an external hard drive enclosure prior to undertaking a hard drive swap. Not only will it make it easy to back up files and copy them to the new drive (and you'll inevitably forget *something*), you can then keep the old drive in the enclosure and use it as a backup.

I was actually prepared with an external hard drive enclosure and thought I could avoid having to re-do the swap. Of course, I got the enclosure so long ago it was IDE instead of SATA. I recently bought [this one](http://www.newegg.com/Product/Product.aspx?Item=N82E16817182313) from Newegg.

After verifying the copy was successful (see, I learned my lesson!) and installing a handful of applications, everything was good to go!

# Back of the Envelope Benchmarking
I was curious how much faster the new (old) laptop would be now that it had an SSD. For what it's worth, it cut boot time roughly in half (from more than a minute to about 30 seconds) and normal application usage (web browsing/email/etc.) was definitely snappier.

My only regret was that in my haste to snag one of the drives before they sold out, I didn't think to buy another one for my other old laptop!

If you have an old laptop but aren't quite ready to buy an new one, upgrading to an SSD is definitely a nice middle ground.
