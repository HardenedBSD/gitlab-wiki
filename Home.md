# About HardenedBSD

07 Oct 2018: Please note that this document is under active and heavy
construction. Not all sections are complete.

HardenedBSD is a fork of FreeBSD, founded in 2014, that implements
exploit mitigations and security hardening technologies. The primary
goal of HardenedBSD is to perform a clean-room re-implementation of
the grsecurity patchset for Linux to HardenedBSD.

Some of HardenedBSD's features can be toggled on a per-application and
per-jail basis using secadm or hbsdcontrol. Documentation for both
tools will be covered later.

## History

Work on HardenedBSD began in 2013 when Oliver Pinter and Shawn Webb
started working on an implementation of Address Space Layout
Randomization (ASLR), based on PaX's publicly-available documentation,
for FreeBSD. At that time, HardenedBSD was meant to be a staging area
for experimental development on the ASLR patch. Over time, as the
process of upstreaming ASLR to FreeBSD became more difficult,
HardenedBSD naturally became a fork.

HardenedBSD completed its ASLR implementation in 2015 with the
strongest form of ASLR in any of the BSDs. Since then, HardenedBSD has
moved on to implementing other exploit mitigations and hardening
technologies. OPNsense, an open source firewall based on FreeBSD,
incorporated HardenedBSD's ASLR implementation in 2016.

HardenedBSD exists today as a fork of FreeBSD that closely follow's
FreeBSD's source code. HardenedBSD syncs with FreeBSD every six hours.
Some of the branches, but not all, are listed below:

1. HEAD -> hardened/current/master
1. stable/11 -> hardened/stable/11
1. releng/11.2 -> hardened/releng/11.2

## Features

HardenedBSD has successfully implemented the following features:

1. PaX-inspired ASLR
1. PaX-inspired NOEXEC
1. PaX-inspired SEGVGUARD
1. Base compiled as Position Independent Executables (PIEs)
1. Base compiled with full RELRO (RELRO + BIND_NOW)
1. Hardening of certain sensitive sysctl nodes
1. Network stack hardening
1. Executable file integrity enforcement
1. Boot process hardening
1. procs/linprocfs hardening
1. LibreSSL as an optional crypto library in base
1. Trusted Path Execution (TPE)
1. Randomized PIDs
1. SafeStack in base
1. SafeStack available in ports
1. Non-Cross-DSO CFI in base
1. Non-Cross-DSO CFI available in ports

## Generic Kernel Options

All of HardenedBSD's features that rely on kernel code require the
following kernel option:

```
options	PAX
```

Additionally, the following kernel option is not required, but exposes
extra sysctl nodes:

```
options PAX_SYSCTLS
```

Generic system hardening can be enabled with the following kernel
option:

```
options	PAX_HARDENING
```

## Generic System Hardening

HardenedBSD implements generic system hardening with the
`PAX_HARDENING` kernel option. Many of these hardening features deal
with restricting what non-root users are permitted to do. When the
kernel is compiled with the `PAX_HARDENING` kernel option, certain
`sysctl(8)` nodes are modified from their defaults.

`procfs(5)` and `linprocfs(5)` are modified to prevent arbitrary
writes to a process's registers. This behavior is controlled by the
`hardening.procfs_harden` `sysctl(8)` node.

`kld(4)` related system calls are restricted to non-jailed, root-only
users. Attempting to list kernel modules using `modfind(2)`,
`kldfind(2)`, and other KLD-related system calls will result in
permission denied if used by a non-root or jailed user.

### Modified sysctl Nodes

These are the nodes that are modified from their original defaults
when `PAX_HARDENING` is enabled in the kernel:

|                  Node                 |                                   Description                                  |   Type  | Original Value |              Hardened Value             |
|:-------------------------------------:|:------------------------------------------------------------------------------:|:-------:|:--------------:|:---------------------------------------:|
| kern.msgbuf_show_timestamp            | Show timestamp in msgbuf                                                       | Integer | 0              | 1                                       |
| kern.randompid                        | Random PID Modulus                                                             | Integer | 0, read+write  | Randomly set at boot and made read-only |
| net.inet.ip.random_id                 | Assign random IP ID values                                                     | Integer | 0              | 1                                       |
| net.inet6.ip6.use_deprecated          | Allow the use of addresses whose preferred lifetimes have expired              | Integer | 1              |  0                                      |
| net.inet6.ip6.use_tempaddr            | Use IPv6 temporary addresses with SLAAC                                        | Integer | 0              | 1                                       |
| net.inet6.ip6.prefer_tempaddr         | Prefer IPv6 temporary address generated last                                   | Integer | 0              | 1                                       |
| security.bsd.see_other_gids           | Unprivileged processes may see subjects/objects with different real gid        | Integer | 1              |  0                                      |
| security.bsd.see_other_uids           | Unprivileged processes may see subjects/objects with different real uid        | Integer | 1              | 0                                       |
| security.bsd.hardlink_check_gid       | Unprivileged processes cannot create hard links to files owned by other groups | Integer | 0              | 1                                       |
| security.bsd.hardlink_check_uid       | Unprivileged processes cannot create hard links to files owned by other users  | Integer | 0              | 1                                       |
| security.bsd.stack_guard_page         | Insert stack guard page ahead of the growable segments                         | Integer | 0              | 1                                       |
| security.bsd.unprivileged_proc_debug  | Unprivileged processes may use process debugging and tracing facilities        | Integer | 1              | 0                                       |
| security.bsd.unprivileged_read_msgbuf | Unprivileged processes may read the kernel message buffer                      | Integer | 1              | 0                                       |


