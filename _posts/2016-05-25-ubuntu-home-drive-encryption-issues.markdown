---

title: Ubuntu Home Drive Encryption Issues
date: '2016-05-25 02:44:10'
---

I run Ubuntu 16.04 on my Dell XPS 13 9350 (not the Developer Edition). Everything mostly just works, but I have run into some annoying problems relating to home drive encryption. I first experienced them in Ubuntu 15.10; unfortunately, they were not (yet) fixed in 16.04 either.

# Disk Encryption Password Prompt

When I would boot, I would be greeted with the following message:

    "Please enter passphrase for disk (cryptswap1) on none"

Being relatively new to Ubuntu, I thought this was normal behavior and I needed to enter my password to decrypt my home drive (I had elected to encrypt my home drive during installation).

I dutifully entered my password each time I booted; however, once I accidentally pressed the enter key before I entered my password. To my surprise, Ubuntu accepted this and booted normally. After some research on the topic, it turns out this is a [known issue](https://bugs.launchpad.net/ubuntu/+source/ecryptfs-utils/+bug/1447282), which will hopefully be fixed soon. There is a [workaround](http://askubuntu.com/a/616717), but with a fix on the way, I decided to live with the extra keystroke.

# Shutdown/reboot Problems

While the password prompt was mildly annoying, it was pretty easy to just skip past it. The second problem (or really set of problems) I discovered was more frustrating as it affected a fairly typical workflow: shutdown and reboot.

I began to notice the Ubuntu UI options to reboot or shutdown my laptop would stop working; i.e. nothing would happen when selected. Eventually I realized this would only occur if I had previously suspended my laptop. If I booted from a powered off state and rebooted or shut down with no suspend in between, everything worked fine.

My first attempt at a solution was to shutdown using the terminal; however issuing the command

    sudo poweroff

would result in the following error message:

    Failed to start poweroff.target: Transaction is destructive.

That didn't sound good. And now I couldn't power off, either. The only way I could actually power off my laptop (other than holding down the power button) was to run

    sudo poweroff -f

Turns out this is another [known issue](https://bugs.launchpad.net/ubuntu/+source/systemd/+bug/1441253); the steps to reproduce match mine almost exactly. But it gets worse: the issue has a status of "Won't Fix".

# The Workaround

For a while, I accepted my lot in life and issued the force shutdown command from the terminal whenever I wanted to power off.

But I started to wonder, "What if the two problems are somehow related?" I decided to take a stab at implementing the [workaround](http://askubuntu.com/a/616717) mentioned above. Yes, it does make the system less secure overall. But it is reversible, and if it fixed my shutdown/power off problems, I was willing to accept that until (hopefully) a permanent fix is released.

Since the workaround involves modifying partitions, I first backed up my home drive to an external hard drive. Next, I followed all the steps as described in the link above:

1) Find the partition that has the swap

    sudo fdisk -l
    Disk /dev/sda: 233,8 GiB, 251000193024 bytes, 490234752 sectors
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disklabel type: gpt
    Disk identifier: B59D4149-AC8E-4B71-B51F-8926B2E82C14

    Device         Start       End   Sectors   Size Type
    /dev/sda1       2048    409599    407552   199M EFI System
    /dev/sda2     409640 253319775 252910136 120,6G Apple HFS/HFS+
    /dev/sda3  253321216 269320191  15998976   7,6G Linux swap
    /dev/sda4  269320192 490233855 220913664 105,3G Linux filesystem


2) Turn off swap

    sudo swapoff -a

3) Disable cryptswap (it's possible this may not exist, that is fine)

    sudo cryptsetup remove /dev/mapper/cryptswap1

4) Remove the corresponding line in /etc/crypttab

    sudo vim /etc/crypttab

5) Setup new swap area (use swap partition from fdisk output above)

    sudo /sbin/mkswap /dev/sda3
    sudo swapon /dev/sda3

6) Replace /dev/mapper/cryptswap1 with /dev/sda3 in fstab

    sudo vim /etc/fstab

# Everything Works!

After rebooting, I was no longer prompted to enter a cryptswap password. But even better, I could now reboot and shutdown normally! Apparently, the two issues are related in some way. I'm not smart enough to figure out how, but I'll take it!
