This presents a way to repair a damaged HardenedBSD system with the installation media (Live-CD). In the example, the **ZFS** file system is used.

1. Download the [HardenedBSD installation media](https://mirror.laylo.io/pub/hardenedbsd/).
1. Boot on this and select "Live CD" (the username is "root" without password).
1. If you want to choice other keybord, use `kbdmap` command.

1. You probably need to enable DHCP on your network card via the command: `dhclient em0`

1. You may need to know your partitions: `gpart show`

1. Run zpool import to get name of zpool (probably zroot): `zpool import`

1. Create a mountpoint for zpool: `mkdir -p /tmp/zroot`

1. Import zpool: `zpool import -fR /tmp/zroot zroot`

1. Create a mountpoint for zfs /: `mkdir /tmp/root`

1. Mount / (the directories will now be available in /tmp/root): `mount -t zfs zroot/ROOT/default /tmp/root`

1. Repair HardenedBSD ([hash link for hbsd-update](http://updates.hardenedbsd.org/pub/HardenedBSD/updates/hardened/)):
```
chroot /tmp/root /bin/sh
hbsd-update -d -V -v UPDATE-HASH
exit
```

Make changes or save your stuff as needed export zpool: `zpool export zroot`

Boot normally.

Source: [forums.freebsd.org](https://forums.freebsd.org/threads/how-to-mount-a-zfs-partition.61112/post-351941)