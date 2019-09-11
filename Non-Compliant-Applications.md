The following applications need special handling with respect to exploit mitigation features in HardenedBSD. Sample rules these applications, and more, can be found  [here](https://github.com/HardenedBSD/secadm-rules).

| Port | Path | Incompatibility |
| ---- | ---- | --------------- |
| www/chromium | /usr/local/share/chromium/chrome | mprotect, pageexec |
| www/firefox | /usr/local/lib/firefox/firefox | mprotect |
| www/firefox | /usr/local/lib/firefox/plugin-container | mprotect, pageexec |
| www/kdepim | /usr/local/bin/kmail | mprotect, pageexec |
| java/openjdk7 | /usr/local/openjdk7/bin/* | mprotect, pageexec |
| java/openjdk8 | /usr/local/openjdk8/bin/* | mprotect, pageexec |
| php-fpm | /usr/local/sbin/php-fpm | mprotect, pageexec |
| python36 | /usr/local/bin/python3.6| mprotect, pageexec |
| sysutils/polkit | /usr/local/lib/polkit-1/polkitd | mprotect, pageexec |
| editors/libreoffice | /usr/local/lib/libreoffice/program/soffice.bin | mprotect, pageexec |
| grub2-bhyve | | pageexec, mprotect, disable_map32bit |

# Building Applications

Lots of applications will not build very well under all the hardening, but might run OK.  HardenedBSD during builds of the ports tree, disables lots of hardening for build purposes.
something like:

```
sysctl hardening.pax.pageexec.status=1 hardening.pax.mprotect.status=1 hardening.pax.disallow_map32bit.status=1 hardening.pax.aslr.status=1
```

This disables these hardening options globally, which you probably don't want in production, so best is to do it in a jail, you can see [here](https://gist.github.com/lattera/22e4f9d2c056b7fbf62adcdf82cd4a50) towards the end how to do that.  Otherwise be sure to re-enable the hardening when you are done:

```
sysctl hardening.pax.pageexec.status=2 hardening.pax.mprotect.status=2 hardening.pax.disallow_map32bit.status=2 hardening.pax.aslr.status=2
```

will put them back to their default settings.

If you get stuck, reach out!