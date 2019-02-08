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
