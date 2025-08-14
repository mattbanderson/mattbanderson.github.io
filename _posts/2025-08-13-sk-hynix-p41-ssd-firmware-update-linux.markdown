---

title: Updating the SK Hynix P41 SSD Firmware on Linux
date: '2025-08-13 21:26:00'
---

# Framework Laptop

I have a [Framework 13 laptop](https://frame.work/laptop13) that I bought in August 2021.
I loved the idea of a [fully fixable/upgradable](https://frame.work/about) laptop. 
Now that I've had it for almost four years, I decided it was time to upgrade the mainboard, I went high a higher-end processor and got the [Ryzen AI 9 HX 370](https://frame.work/products/mainboard-amd-ai300?v=FRANTE0009).

Another nice Framework bonus is that they sell [cases](https://frame.work/marketplace?search=case) so you can re-purpose the old mainboard into standalone machine. 
Since I planned to turn the old mainboard into another machine that would need it's own RAM and storage, I decided to buy a new, larger SSD.

# Choosing an SSD

When I originally bought my Framework, I bought my RAM and hard drive separately so I could hunt for a good deal. 
I went with an SK Hynix P31 Gold 1TB SSD for my original purchase because of the good power management reviews in the Framework community forums and elsewhere.
I had no issues, so I went with the newer [P41 Platinum 2 TB](https://amzn.to/41FwIfJ) (referral link) version.

# Firmware Issues?

The main downside to these drives is various reports of write performance gradually slowing down.
Though I would be unlikely to notice the issue, SK Hynix has released new [firmware](https://ssd.skhynix.com/download/) that many people report resolves the issue.

My challenge was getting the new firmware onto the SSD.
SK Hynix provides GUI software to update the firmware, but it only runs on Windows.
I have an old Windows machine, but I could not get the software to recognize the drive.
It's likely I needed something better than a cheap SSD enclosure for it to work.
Not wanting to buy an expensive peripheral for a single task, I did some digging to see if I could figure out how to upgrade the firmware manually.

# Upgrade SSD Firmware on Linux

I found instructions for using the `nmve-cli` utility to upgrade SSD firmware on the [Arch Linux wiki](https://wiki.archlinux.org/title/Solid_state_drive/NVMe#Generic).
I installed the new 2 TB drive into the Framework and booted up.
Below are the commands I used.

Install nvme-cli:
```sh
$ sudo apt install -y nvme-cli
```

List devices to identify the SSD:
```sh
$ lsblk

NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
nvme0n1     259:0    0   1.8T  0 disk 
├─nvme0n1p1 259:1    0     1G  0 part /boot/efi
└─nvme0n1p2 259:2    0   1.8T  0 part /
```

Display firmware information:
```sh
$ sudo nvme fw-log /dev/nvme0n1
Firmware Log for device:nvme0n1
afi  : 0x1
frs1 : 0x3032413036303135 (51060A20)
```

Identify available firmware slots, in this case 1 (line 4), also note reset is not required for activation for this drive (line 2):
```sh
$ sudo nvme id-ctrl /dev/nvme0 -H | grep Firmware
  [9:9] : 0x1	Firmware Activation Notices Supported
  [4:4] : 0x1	Firmware Activate Without Reset Supported
  [3:1] : 0x3	Number of Firmware Slots
  [0:0] : 0	    Firmware Slot 1 Read/Write
```

Download the new firmware:
```sh
$ wget https://ssd.skhynix.com/download/firmware/Platinum_P41_firmware_update.ebin
```

The output of this command ends in `1A20` instead of `0A20` from the `fw-log` command, this should indicate a newer firmware version:

```sh
$ strings ~/Downloads/Platinum_P41_firmware_update.ebin | grep A20
51061A20
51061A20
51061A20
```

Load the firmware to the SSD by device ID:
```sh
$ sudo nvme fw-download -f Platinum_P41_firmware_update.ebin /dev/nvme0n1
Firmware download success
```

Commit the firmware, specifying the slot `-s 1` but do not activate it yet `-a 0`:
```sh
$ sudo nvme fw-commit -s 1 -a 0 /dev/nvme0n1
Success committing firmware action:0 slot:1
```

Commit the firmware to slot 1 `-s 1` and activate it `-a 0`:
```sh
$ sudo nvme fw-commit -s 1 -a 2 /dev/nvme0n1
Success committing firmware action:2 slot:1
```

Display firmware information, note it now ends in `1A20` instead of `0A20`:
```sh
$ sudo nvme fw-log /dev/nvme0n1
Firmware Log for device:nvme0n1
afi  : 0x1
frs1 : 0x3032413136303135 (51061A20)
```

If the output of the `sudo nvme id-ctrl` command did not indicate "Activate Without Reset Supported", reset the controller:
```sh
$ nvme reset /dev/nvme0n1
```


# That's It!

Everything should be good to go with the new firmware working.
This can be done with the SSD in use for the OS, no need to boot from a USB image.

That all there is to it--hope this helps someone out there!