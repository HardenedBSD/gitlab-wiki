As root;

1. make sure running latest 11.2 release:

```
hbsd-update
pkg update && pkg upgrade
```

2. replace 11 with 12 (s/11/12/g)

`vi /etc/hbsd-update.conf`

3. FreeBSD has modified ntp to run without root privileges (a good thing), and requires a non-root UID.  FreeBSD and HardenedBSD use username ntpd with UID 123, so add the ntpd user:

`adduser -u 123 -D -L daemon`

4. upgrade:

```
hbsd-update
reboot
pkg-static install -f pkg
pkg update && pkg upgrade
```

5. Reboot one more time for good measure, to ensure everything is fabulous.

```
$ freebsd-version
12.0--HBSD
$ hbsd-update
hbsd-v1200058-7b693e5bb0ecd02d3a905c6dd6299289bae1ef0e
[*] This system is already on the latest version.
```