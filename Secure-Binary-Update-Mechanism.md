## Note

This document is under active development. The topics and ideas presented here may change drastically without notice.

## Goals:

1. Provide binary updates for base and kernel
1. Cryptographically sign binary updates
1. Support mirrors and a massively scalable design
1. Easy maintenance

## Goal 1 - Provide binary updates for base and kernel

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