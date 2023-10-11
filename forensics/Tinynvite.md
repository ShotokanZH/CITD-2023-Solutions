# Tinynvite
Edit the VM and add some RAM, boot a linux image and launch `diskpart` on `/dev/sda`.\
Just launch the default "find lost partitions" option, it will detect a `/dev/sda1` partition.

Write it and reboot.


As wallpaper you will find the password for an archive, contained in `/mnt/sda1/flag.zip`
Open it, you will find the flag.txt.

## CAVEATS/Why is it hard?
When the user tries to boot the tinycore OS ISO he will find out that it won't, throwing a random error.

48MB of RAM it's not enough for the OS to boot in memory only, even if tinycore runs perfectly fine with that amount of RAM when booting from the disk.

TinyCore is a ram-based OS, that backups and restores certain folders at boot and shutdown time.

The wallpapers are stored in `/opt/backgrounds/` which is **RAM-based**.

A memory backup is created before shutdown, and restored after poweron, so the `background.png` file is actually not visible if a user tries to recover data from the disk directly, leaving him a sad encrypted "`flag.zip`" file.
