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

## Address Space Layout Randomization

ASLR randomizes the layout of the virtual address space of a process
through using randomized deltas. ASLR prevents attackers from knowing
where vulnerabilities lie in memory. Without ASLR, attackers can
easily craft and reuse exploits across all deployed systems. As is the
case with all exploit mitigation technologies, ASLR is meant to help
frustrate attackers, though ASLR alone is not sufficient to completely
stop attacks. ASLR simply provides a solid foundation in which to
implement further exploit mitigation technologies. A holistic approach
to security (aka, defense-in-depth) is the best way to secure a
system. Additionally, ASLR is intended and designed to help prevent
successful remote attacks, not local.

HardenedBSD's ASLR implementation is based off of PaX's design and
documentation. PaX's documentation can be found
[here](https://github.com/HardenedBSD/pax-docs-mirror/blob/master/aslr.txt).

On 13 July 2015, HardenedBSD's ASLR implementation was completed with
full stack and VDSO randomization. Since then, various improvements
have been made, like implementation shared library load order
randomization. HardenedBSD is the only BSD to support true stack
randomization. Meaning, the top of the stack is randomized in addition
to a random-sized gap between the top of the stack and the start of
the user stack.

ASLR is enabled by default in the `HARDENEDBSD` kernel configuration.
ASLR has been tested and is known to work on amd64, i386, arm, arm64,
and risc-v. The options for ASLR are:

```
options PAX
options PAX_ASLR
```

If the kernel has been compiled with `options PAX_SYSCTLS`, then the
sysctl node `hardening.pax.aslr.status` will be available. The
following values will determin the enforcement of ASLR:

1. 0 - Force disabled
1. 1 - Disabled by default. User must opt applications in.
1. 2 - Enabled by default. User must opt applications out (default.)
1. 3 - Force enabled

### Implementation

HardenedBSD's ASLR uses a set of four deltas on 32-bit systems and
five deltas on 64-bit systems. Additionally, on 64-bit systems, 32-bit
compatibility is supported by a set of different deltas. The deltas
are calculated at image activation (execve) time. The deltas are
provided as a hint to the virtual memor-y subsystem, which may further
modify the hint. Such may be the case if the application explicitly
requests superpage support or other alignment constraints.

The deltas are:

1. PIE execution base
1. mmap hint for non-fixed mappings
1. Stack top and gap
1. Virtual Dynamic Shared Object (VDSO)
1. On 64-bit systems, mmap hint for `MAP_32BIT` mappings

The calculation of each delta is controlled pby how many bits of
entropy the user wants to introduce into the delta. The amount of
entropy can be overridden in the kernel config and via boot-time
(`loader.conf(5)`) tunables. By default, HardenedBSD uses the
following amount of entropy:

| Delta         | 32-bit  | 64-bit  | Compat  | Tunable                         | Compat Tunable                      | Kernel Option                   | Compat Kernel Option                |
|---------------|---------|---------|---------|---------------------------------|-------------------------------------|---------------------------------|-------------------------------------|
| mmap          | 14 bits | 30 bits | 14 bits | hardening.pax.aslr.mmap_len     | hardening.pax.aslr.compat.mmap_len  | PAX_ASLR_DELTA_MMAP_DEF_LEN     | PAX_ASLR_COMPAT_DELTA_MMAP_DEF_LEN  |
| Stack         | 14 bits | 42 bits | 14 bits | hardening.pax.aslr.stack_len    | hardening.pax.aslr.compat.stack_len | PAX_ASLR_DELTA_STACK_DEF_LEN    | PAX_ASLR_COMPAT_DELTA_STACK_DEF_LEN |
| PIE exec base | 14 bits | 30 bits | 14 bits | hardening.pax.aslr.exec_len     | hardening.pax.aslr.compat.exec_len  | PAX_ASLR_DELTA_EXEC_DEF_LEN     | PAX_ASLR_COMPAT_DELTA_EXEC_DEF_LEN  |
| VDSO          | 8 bits  | 28 bits | 8 bits  | hardening.pax.aslr.vdso_len     | hardening.pax.aslr.compat.vdso_len  | PAX_ASLR_DELTA_VDSO_DEF_LEN     | PAX_ASLR_COMPAT_DELTA_VDSO_DEF_LEN  |
| MAP_32BIT     | N/A     | 18 bits | N/A     | hardening.pax.aslr.map32bit_len | N/A                                 | PAX_ASLR_DELTA_MAP32BIT_DEF_LEN | N/A                                 |

When a process forks, the child process inherits its parent's ASLR
settings, including deltas. Only at image activation (execve) time
does a process receive new deltas.

### Position-Independent Executables (PIEs)

In order to make full use of ASLR, applications must be compiled as
Position-Independent Executables (PIEs). If an application is not
compiled as a PIE, then ASLR will be applied to all but the execution
base. All of base is compiled as PIEs, with the exception of a few
applications that explicitly request to be statically compiled. Those
applications are:

1. All applications in /rescue
1. /sbin/devd
1. /sbin/init
1. /usr/sbin/nologin

Compiling all of base as PIEs can be turned off by setting
`WITHOUT_PIE` in `src.conf(5)`.

### Shared Library Load Order Randomization

Breaking ASLR remotely requires chaining multiple vulnerabilities,
including one or more information leakage vulnerabilities. Information
leakage vulnerabilities expose data an attacker can use to determine
the memory layout of the process. Code reuse attacks, like ROP and its
variants, exist to bypass exploit mitigations like PAGEEXEC/NOEXEC.
Over the years, a lot of tooling for automated ROP gadget generation
has been developed. The tools generally rely on gadgets found via
shared libraries and require that those sshared libraries by loaded in
the same order. By randomizing the order in which shared librariers
get load, ROP gadgets have a higher chance of failing. Shared library
load order randomization is disabled by default, but can be opted in
on a per-application basis using secadm or hbsdcontrol.

## PAGEEXEC and MPROTECT (aka, NOEXEC)

[PAGEEXEC](https://github.com/HardenedBSD/pax-docs-mirror/blob/master/pageexec.txt)
and
[MPROTECT](https://github.com/HardenedBSD/pax-docs-mirror/blob/master/mprotect.txt)
comprise what is more commonly called W^X (W xor X). The design and
implementation in HardenedBSD is inspred by PaX's. PAGEEXEC prevents
applications from creating memory mappings that are both Writable (W)
and Executable (X) at `mmap(2)` time. MPROTECT prevents applications
from toggling memory mappings between writable and executable with
`mprotect(2)`. Combining both PAGEEXEC and MPROTECT prevents attackers
from executing injected code, thus forcing attackers to utilize code
reuse techniques like ROP and its variants. Code reuse techniques are
very difficult to make reliable, especially when multiple exploit
mitigation technologies are present and active.

The PAGEEXEC and MPROTECT features can be enabled with the
`PAX_NOEXEC` kernel option and is enabled by default in the
`HARDENEDBSD` kernel. If the `PAX_SYSCTLS` option is also enabled, two
new sysctl nodes will be created, which follow the same symantics as
the `hardening.pax.aslr.status` sysctl:

1. `hardening.pax.pageexec.status` - Default 2
1. `hardening.pax.mprotect.status` - Default 2

### PAGEEXEC

If an application requests a memory mapping via `mmap(2)`, and the
application requests `PROT_WRITE` and `PROT_EXEC`, then `PROT_EXEC` is
dropped. The application will be able to write to the mapping, but
will not be able to execute what was written. When an application
requests W|X mappings, the application is more likely to write to the
mapping, but not execute it. Such is the case with some Python
scripts: the developer is simply asking for more permissions than is
truly needed.

The kernel keeps a concept of pax protection. HardenedBSD drops
`PROT_EXEC` from the max protection when `PROT_WRITE` is requested.
When `PROT_EXEC` is requested, `PROT_WRITE` is dropped from the max
protection. When both are requested, `PROT_WRITE` is given priority
and `PROT_EXEC` is dropped from both the request and the max
protection.

### MPROTECT

If an application requests that a writable mapping be changed to
executable via `mprotect(2)`, the request will fail and set `errno` to
`EPERM`. The same applies to an executable mapping being changed to
writable via `mprotect(2)`. Applications and shared objects that
utilize text relocations (TEXTRELs) have issues with the MPROTECT
feature. TEXTRELs require that executable code be relocated to
different locations in memory. During the reolcation process, the
newly allocated memory needs to be both writable and executable. Once
the reolcation process is finished, the mapping can be marked as
`PROT_READ|PROT_EXEC`.

Some applications with a JIT, most notably Firefox, opt to create
writable memory mappings that are non-executable, but upgrade the
mapping to executable when appropriate. This gets rid of the problem
of having active memory mappings that are both writable and
executable. This makes applications like Firefox work with PAGEEXEC,
but still have an issue with MPROTECT.

By combining both PAGEEXEC and MPROTECT, HardenedBSD enables a strict
form of W^X. Some applications may have issues with PAGEEXEC,
MPROTECT, or both. When issues arise, secadm or hbsdcontrol can be
used to disable PAGEEXEC, MPROTECT, or both for just that one
application.
