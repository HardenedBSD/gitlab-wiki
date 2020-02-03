## Example for hardenedbsd-12-stable USB-stick
### List installers
https://ci-01.nyi.hardenedbsd.org/pub/hardenedbsd/12-stable/


### Install
`# pkg install -y ca_root_nss`

`$ fetch https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/CHECKSUM.SHA512`

`$ fetch https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/CHECKSUM.SHA512.asc`

`$ fetch https://installer.hardenedbsd.org/hardened_12_stable_master-LAST/HardenedBSD-12-STABLE-v1200058.4-amd64-memstick.img`

`# pkg install -y pgpgpg`

`$ gpg --keyserver hkps://zimmermann.mayfirst.org --recv-key 6FFD188D`

`$ gpg --fingerprint 6FFD188D`

`$ gpg --verify CHECKSUM.SHA512.asc CHECKSUM.SHA512`

`$ if grep "^$(sha512 HardenedBSD-12-STABLE-v1200058.4-amd64-memstick.img)$" CHECKSUM.SHA512;then echo "OK"; else echo "WARNIG!!";fi`

`# dd if=HardenedBSD-12-STABLE-v1200058.4-amd64-memstick.img of=/dev/sda bs=64k`


### After install
`# pkg upgrade && pkg install -y git-lite beadm`

``# hbsd-update -V -b `date "+%Y%m%d%H%M%S"` ``

### Get ports
`# git clone https://github.com/HardenedBSD/hardenedbsd-ports.git /usr/ports`

And to maintain alignment of your ports tree with github

`# cd /usr/ports && git pull`

### Get src
`# git clone --single-branch --branch hardened/12-stable/master https://github.com/hardenedbsd/hardenedbsd-stable/ /usr/src`

similarly to maintain your /usr/src (analogous to `svnlite update /usr/src`)

`# cd /usr/src && git pull`

### HardenedBSD Handbook
https://hardenedbsd.org/~shawn/hbsd_handbook/book.html
