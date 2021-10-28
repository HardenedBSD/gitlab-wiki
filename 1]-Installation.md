## Example for hardenedbsd-13-stable USB-stick
### List installers
https://ci-01.nyi.hardenedbsd.org/pub/hardenedbsd/13-stable/amd64/amd64/BUILD-LATEST/

### Install
`# pkg install -y ca_root_nss`

`$ fetch https://ci-01.nyi.hardenedbsd.org/pub/hardenedbsd/13-stable/amd64/amd64/BUILD-LATEST/CHECKSUMS.SHA512`

`$ fetch https://ci-01.nyi.hardenedbsd.org/pub/hardenedbsd/13-stable/amd64/amd64/BUILD-LATEST/memstick.img.asc`

`$ fetch https://ci-01.nyi.hardenedbsd.org/pub/hardenedbsd/13-stable/amd64/amd64/BUILD-LATEST/memstick.img`

`# pkg install -y pgpgpg`

`$ gpg --keyserver hkps://zimmermann.mayfirst.org --recv-key 6FFD188D`

`$ gpg --fingerprint 6FFD188D`

`$ gpg --verify memstick.img.asc memstick.img`

`$ if grep "^$(sha512 memstick.img)$" CHECKSUM.SHA512;then echo "OK"; else echo "WARNIG!!";fi`

`# dd if=memstick.img of=/dev/sda bs=64k`


### After install
`# pkg upgrade && pkg install -y git-lite beadm`

``# hbsd-update -V -b `date "+%Y%m%d%H%M%S"` ``

### Get ports
`# git clone https://github.com/HardenedBSD/hardenedbsd-ports.git /usr/ports`

And to maintain alignment of your ports tree with github

`# cd /usr/ports && git pull`

### Get src
`# git clone --single-branch --branch hardenedbsd/main https://git.hardenedbsd.org/hardenedbsd/ports.git /usr/ports/`

similarly to maintain your /usr/src (analogous to `svnlite update /usr/src`)

`# cd /usr/src && git pull`