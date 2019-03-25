## Example for hardenedbsd-12-stable USB-stick
### List installers
https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/

### Install

`$ fetch https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/CHECKSUM.SHA512`

`$ fetch https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/CHECKSUM.SHA512.asc`

`$ fetch https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/HardenedBSD-12-STABLE-v1200058.4-amd64-memstick.img`

`# pkg install pgpgpg`

`$ gpg --keyserver hkps://zimmermann.mayfirst.org --recv-key 6FFD188D`

`$ gpg --fingerprint 6FFD188D`

`$ gpg --verify CHECKSUM.SHA512.asc`

`$ if grep "^$(sha512 HardenedBSD-12-STABLE-v1200058.4-amd64-memstick.img)$" CHECKSUM.SHA512;then echo "OK"; else echo "WARNIG!!";fi`

`# dd if=HardenedBSD-12-STABLE-v1200058.4-amd64-memstick.img of=/dev/sda bs=64k`


### After install
`# pkg upgrade && pkg install git-lite beadm`

``# hbsd-update -I -V -b `date "+%Y%m%d%H%M%S"` ``

### Get ports
`# git clone https://github.com/HardenedBSD/hardenedbsd-ports.git /usr/ports`
### Get src
`# git clone --single-branch --branch hardened/12-stable/master https://github.com/hardenedbsd/hardenedbsd-stable/ /usr/src`

### HardenedBSD Handbook
https://hardenedbsd.org/~shawn/hbsd_handbook/book.html