If you come from a GNU/Linux distribution, some commands may seem to be missing, but you should know that they have a equivalence.

This table is intended to list some the equivalence:

| Ubuntu / Debian         | HardenedBSD |
|-----------------------|-----------------------|
| strace                | truss |
| chattr +i /tmp/test   | chflags schg /tmp/test |
| md5sum /tmp/test      | md5 /tmp/test |
| wget                  | fetch (wget is available but not included by default) |
| lspci -v              | pciconf -lv |
| apt install bpytop    | pkg install bpytop |
| dpkg -i bpytop.deb    | pkg install bpytop.pkg |
| apt search vlc        | pkg search vlc |
| apt show vlc          | pkg info vlc |
| paxctl -cm /usr/lib/firefox/firefox | hbsdcontrol pax disable mprotect /usr/local/lib/firefox/firefox |