## Note

This document is under active development. The topics and ideas presented here may change drastically without notice. The work on this binary update mechanism for HardenedBSD will be superseded and replaced by pkg base, whenever FreeBSD finishes that.

## Goals:

1. Provide binary updates for base and kernel
1. Cryptographically sign binary updates
1. Support mirrors and a massively scalable design
1. Easy maintenance
1. Supports jails
1. Support ZFS BEs

### Goal 1 - Provide binary updates for base and kernel

This can be achieved with base.txz and kernel.txz. We would combine both of these tarballs into a new tarball that contains metadata about files to skip (like `/etc/master.passwd`), obsoleted files, etc.

Example tarball contents (in no particular order):
* base.txz
* kernel-HARDENEDBSD.txz
* obsolete.txt
* skip.txt
* ChangeLog.txt (from git)
* UPDATING-HardenedBSD.txt
* base.txz.sig (Cryptographic signature for base.txz) (to be discussed later)
* kernel-HARDENEDBSD.txz.sig (cryptographic signature for kernel-HARDENEDBSD.txz) (to be discussed later)
* obsolete.txt.sig (Cryptographic signature for base.txz) (to be discussed later)
* skip.txt.sig (Cryptographic signature for base.txz) (to be discussed later)
* pubkey.asc (X.509 public key used to sign the artifacts) (to be discussed later)

### Goal 2 - Cryptographically sign binary updates

In order to provide assurance of data integrity and authenticity, all artifacts that alter the filesystem need to be signed. The digital signatures will be detached from the files they pertain to. As OpenSSL is included in base, rely on that to perform the signature procedures.

Unsigned updates will be supported by a command-line argument. Cryptographic signing will be enforced by default.

In order to support third-party signatures along with certificate chaining, HardenedBSD will use a standard that allows for a form of PKI. That standard will be RSA keys embedded within X.509 certificates. A trusted keystore will need to be maintained by the sysadmin/user of the system. This is no different in concept than what exists for existing FreeBSD utilities like pkg(7).

By default, the trusted root store will be `/usr/share/keys/hbsd-update`. OpenSSL will be instructed to load the trusted root certificate and validate public keys based on the loaded root certificates.

Upon downloading the update tarball, the tarball will be unpacked and the public cert extracted. Any file that ends in .sig will be validated with that public cert that matches against a certificate in the trusted root certificate store. If the public cert does not validate or if the signature doesn't validate, an error will be thrown and the user will be forced to diagnose.

Distributing the public cert used for signing the artifacts along with the artifacts allows a distributor to immediately and efficiently update key material in case of private key compromise or expiration. Compromise or expiration of the trusted root certificate is a much more serious issue that should be resolved by human intervention.

### Goal 3 - Scalability

This goal is relatively easy to achieve. Given that only a single tarball is produced, the tarball can be distributed across many nodes.

DNS TXT records will be used to distribute version information. The TXT record will be pipe-delimited. Different TXT records will exist for -CURRENT, 10-STABLE, etc. Example TXT record names: amd64.current.updater.hardenedbsd.org, amd64.10stable.updater.hardenedbsd.org.

Current TXT record fields:

1. Timestamp
1. Build version number
1. Git object hash

### Goal 4 - Easy Maintenance

There will be one script for building the update tarball along with its artifacts. It will only rely on /bin/sh, OpenSSL, and git. Everything will be automated with the exception of DNS updates. However, the tarball build script will provide output on stdout that will lend itself to DNS automation.

### Goal 5 - Jail support

Follow the example of pkg(7) and support updating jails from the host.

### Goal 6 - ZFS Boot Environment support

Optionally install updates into a newly-created Boot Environment (BE). This feature will require the beadm tool, available in our package repo. Since this requires a third-party tool, make this feature optional via a command-line flag.