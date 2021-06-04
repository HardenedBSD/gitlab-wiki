Building the memstick and ISO of HardenedBSD
============================================

This article documents how to build USB boot media (memstick.img) and ISO files for HardenedBSD. Please note that you will also get the sources and the kernel in compressed archives.
This is useful for developers who want to test their modifications or system administrators who want to customize this files.

For users, know that there are already nightly build available at [this address](https://ci-01.nyi.hardenedbsd.org/pub/hardenedbsd/).


Requirements
------------

These are the requirements for our setup:

1. devel/git (or devel/git-lite)
1. the src tree checked out to /usr/src.

Base System Source
------------------

If you don't have a src tree, you will need to get it. In this example, we choose the 13-stable version with a 10 commit history (to avoid unnecessary downloads):

```
git clone --depth 10 --single-branch --branch hardened/13-stable/master https://git.hardenedbsd.org/hardenedbsd/HardenedBSD.git /usr/src
```

Compile the base and the kernel:

```
cd /usr/src
make -sj`sysctl -n hw.ncpu` buildworld buildkernel
```

Build startup media (memstick and ISO):

```
cd /usr/src/release
make real-release NOSRC=1 NOPORTS=1
```

You'll find the resulting images in:
```
/usr/obj/usr/src/amd64.amd64/release
```