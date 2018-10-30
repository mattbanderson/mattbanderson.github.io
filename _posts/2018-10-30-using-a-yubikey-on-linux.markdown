---

title: Using a YubiKey on Linux
date: '2018-10-30 19:53:00'
---

I recently got a [YubiKey 4](https://www.yubico.com/yubikey-4-overview/) to use for multi-factor authentication and have gradually been setting up my various accounts (GitHub, Dropbox, etc.).

At one of our client sites, I use a CentOS desktop. After finally getting their IT department to approve the YubiKey, I was excited to actually get to use it on that machine. But when I plugged it in to authenticate to GitHub, much to my disappointment, I got a message that simply said "Something when went" after touching the YubiKey.

After some searching, I discovered there is some additional setup and configuration that needs to be done to get it to work on Linux. The steps outlined in the Yubico support article [Using Your U2F YubiKey with Linux](https://support.yubico.com/support/solutions/articles/15000006449) solved my issue. After updating udev rules on my system, the YubiKey worked flawlessly!
