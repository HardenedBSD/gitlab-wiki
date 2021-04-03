[[_TOC__]]

# About HardenedBSD

HardenedBSD is a fork of FreeBSD, founded in 2014, that implements
exploit mitigations and security hardening technologies. The primary
goal of HardenedBSD is to perform a clean-room re-implementation of
the grsecurity patchset for Linux to HardenedBSD.

Some of HardenedBSD's features can be toggled on a per-application and
per-jail basis using secadm or hbsdcontrol. Documentation for both
tools will be covered later.

# Translations

* [Espanol](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/Home_es)

# History

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
incorporated HardenedBSD's ASLR implementation in 2016. OPNsense
completed their migration to HardenedBSD on 31 January 2019.

HardenedBSD exists today as a fork of FreeBSD that closely follow's
FreeBSD's source code. HardenedBSD syncs with FreeBSD every six hours.
Some of the branches, but not all, are listed below:

1. HEAD -> hardened/current/master
1. stable/13 -> hardened/13-stable/master
1. stable/12 -> hardened/12-stable/master

# Features

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
1. Retpoline applied to base and ports

# Generic Kernel Options

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

# Generic System Hardening

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

## Modified sysctl Nodes

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

# Address Space Layout Randomization (ASLR)

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
[here](https://git.hardenedbsd.org/HardenedBSD/pax-docs-mirror/blob/master/aslr.txt).

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

## Implementation

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

## Position-Independent Executables (PIEs)

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

## Shared Library Load Order Randomization

Breaking ASLR remotely requires chaining multiple vulnerabilities,
including one or more information leakage vulnerabilities. Information
leakage vulnerabilities expose data an attacker can use to determine
the memory layout of the process. Code reuse attacks, like ROP and its
variants, exist to bypass exploit mitigations like PAGEEXEC/NOEXEC.
Over the years, a lot of tooling for automated ROP gadget generation
has been developed. The tools generally rely on gadgets found via
shared libraries and require that those shared libraries be loaded in
the same deterministic order. By randomizing the order in which shared
librariers get load, ROP gadgets have a higher chance of failing.
Shared library load order randomization is disabled by default, but
can be opted in on a per-application basis using secadm or
hbsdcontrol.

# PaX SEGVGUARD

ASLR has known weaknesses. If an information leak is present,
attackers can use the leak to determine the memory layout and, given
time, successfully exploit the application.

Some applications, like daemons, can optionally be set to
automatically restart after a crash. Automatically restarting
applications can pose a security risk by allowing attackers to repeat
failed attacks, modifying the attack until successful.

PaX SEGVGUARD provides a mitigation for such cases. SEGVGUARD keeps
track of how many times a given application has crashed within a
configurable window and will suspend further execution of the
application for a configurable time once the crash limit has been
reached.

The kernel option for PaX SEGVGUARD is:

```
options PAX_SEGVGUARD
```

Due to performance concerns, SEGVGUARD is set to opt-in by default.
SEGVGUARD can be set to opt-out by setting the
`hardening.pax.segvguard.status` sysctl node to 2.

# PAGEEXEC and MPROTECT (aka, NOEXEC)

[PAGEEXEC](https://git.hardenedbsd.org/HardenedBSD/pax-docs-mirror/blob/master/pageexec.txt)
and
[MPROTECT](https://git.hardenedbsd.org/HardenedBSD/pax-docs-mirror/blob/master/mprotect.txt)
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

## PAGEEXEC

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

## MPROTECT

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

# SafeStack

SafeStack is an epxloit mitigation that creates two stacks: one for
data that needs to be kep safe, such as return addresses and function
pointers; and an unsafe stack for everything else. SafeStack promises
a low performance penalty (typically around 0.1%).

SafeStack requires both ASLR and W^X in order to be effective. With
HardenedBSD satisfying both of those prerequsites, SafeStack was
deemed to be an excellent candidate for default inclusion in
HardenedBSD. Starting with HardenedBSD 11-STABLE, it is enabled by
default for amd64. SafeStack can be disabled by setting
`WITHOUT_SAFESTACK` in `src.conf(5)`.

As of 08 Oct 2018, SafeStack only supports being applied to
applications and not shared libraries. Multiple patches have been
submitted to clang by third parties to add the missing shared library
support. As such, SafeStack is still undergoing active development.

SafeStack has been made available in the HardenedBSD ports tree as
well. Unlike PIE and RELRO and BIND_NOW, it is not enabled globally
for the ports tree. Some ports known to work well with SafeStack have
it enabled by default. Users are able to toggle SafeStack by using the
`config` make target. Additionally, the SafeStack option is only
applicable to the amd64 architecture. Attempting to enable SafeStack
for a non-amd64 port build will result in a NO-OP. SafeStack simply
will not be applied.

# Variable Auto-Initialization

In HardenedBSD 13, we enabled a feature from llvm called (automatic
variable initialization)[https://reviews.llvm.org/D54604]. Variables
that would normally be uninitialized are zero-initialized. This helps
prevent information leaks and abuse of code with undefined behavior.

From llvm's documentation:

This feature aims to make undefined behavior hurt less, which
security-minded people will be very happy about. Notably, this means
that there's no inadvertent information leak when:

* The compiler re-uses stack slots, and a value is used uninitialized.
* The compiler re-uses a register, and a value is used uninitialized.
* Stack structs / arrays / unions with padding are copied.

For more complete documentation, take a look at the link in the first
paragraph in this section.

# Control-Flow Integrity (CFI)

Control-Flow Integrity (CFI) is an exploit mitigation technique that
prevents unwanted transfer of control from branch instructions to
arbitrary valid memory locations. The CFI implementation from
clang/llvm comes in two forms: Cross-DSO CFI and non-Cross-DSO CFI.
HardenedBSD 12 enables non-Cross-DSO CFI by default on amd64 and arm64
for base.

CFI requires a linker that supports Link Time Optimization (LTO).
Starting with 12, HardenedBSD ships with ld.lld as the default
linker. ld.lld supports LTO.

Non-Cross-DSO CFI adds checks both before and after every branch
instruction in the application itself. If an application loads
libraries via `dlopen(3)` and resolves functions via `dlsym(3)` and
calls those functions, the application will abort. Some applications,
like `bhyveload(8)` do this and thus have the cfi-icall scheme
disabled, allowing it to call functions resolved via `dlsym(3)`. Thus,
if a user finds that an application crashes in HardenedBSD 12, the
user should file a bug report. The cfi-icall scheme can be disabled
when building world by adding a CFI override in that application's
Makefile.

Note that Non-Cross-DSO CFI does not require ASLR and strict W^X.
Given that Cross-DSO CFI keeps metadata and state information,
Cross-DSO CFI does require ASLR and W^X in order to be effective.

Non-Cross-DSO CFI support has been added to HardenedBSD's ports
framework. However, it is not enabled by default. Support for CFI in
ports is still very premature and is only available for those brave
users who want to experiment.

As of 20 May 2017, Cross-DSO CFI is being actively researched.
However, support for Cross-DSO CFI is not available in HardenedBSD,
yet. Cross-DSO CFI would allow functions resolved through
`dlopen(3)`/`dlsym(3)` to work since CFI would be able to be applied
between Dynamic Shared Object (DSO) boundaries. Significant progress
has been made in the first half of 2018 with regards to Cross-DSO CFI.
The base operating system can be fully compiled with Cross-DSO CFI. On
16 Jul 2018, a pre-alpha
[Call For
Testing](https://hardenedbsd.org/article/shawn-webb/2018-07-16/preliminary-call-testing-cross-dso-cfi)
was released for wider initial testing. The HardenedBSD core
development team hopes to launch Cross-DSO CFI in base within the
latter half of 2019.

# hbsdcontrol

`hbsdcontrol(8)` is a tool, included in base, that allows users to
toggle exploit mitigations on a per-application basis. Users will
typically use hbsdcontrol to disable PAGEEXEC and/or MPROTECT
restrictions. hbsdcontrol is similar in scope as secadm and is
preferred over secadm for filesystems that support extended
attributes.

Unlike secadm, hbsdcontrol does not use a configuration file. Instead,
it stores metadata in the filesystem extended attributes. Both UFS
and ZFS support extended attributes. Network-based filesystems, like
NFS or SMB/CIFS do not. secadm is the preferred method for exploit
mitigation toggling only in cases where extended attributes are not
supported.

Example usage of hbsdcontrol to disable MPROTECT for Firefox:

```
# hbsdcontrol pax disable mprotect /usr/local/lib/firefox/firefox
# hbsdcontrol pax disable mprotect /usr/local/lib/firefox/plugin-container
```

As of 09 Jul 2020, various ports have exploit mitigations pre-toggled
such that users do not have to worry about setting the togles
themselves.

| Port           	| Path					| Exploit Mitigation	|
|-----------------------|---------------------------------------|-----------------------|
| editors/libreoffice	| lib/libreoffice/program/soffice.bin	| PaX MPROTECT		|
| editors/libreoffice	| lib/libreoffice/program/soffice.bin	| PaX PAGEEXEC		|
| lang/python37		| bin/python3.7				| PaX MPROTECT		|
| lang/python37		| bin/python3.7				| PaX PAGEEXEC		|
| lang/python38		| bin/python3.8				| PaX MPROTECT		|
| lang/python38		| bin/python3.8				| PaX PAGEEXEC		|
| security/keepassxc	| bin/keepassxc				| PaX MPROTECT		|
| security/keepassxc	| bin/keepassxc				| PaX MPROTECT		|
| security/keepassxc	| bin/keepassxc				| PaX PAGEEXEC		|
| sysutils/polkit	| lib/polkit-1/polkitd			| PaX MPROTECT		|
| sysutils/polkit	| lib/polkit-1/polkitd			| PaX PAGEEXEC		|
| www/chromium		| share/chromium/chrome			| PaX MPROTECT		|
| www/chromium		| share/chromium/chrome			| PaX PAGEEXEC		|
| www/firefox		| lib/firefox/firefox			| Pax MPROTECT		|
| www/firefox		| lib/firefox/plugin-container		| PaX MPROTECT		|

# Security Administration (secadm)

secadm is a tool, distributed via ports, that allows users to toggle
exploit mitigations on a per-application and per-jail basis. Users will
typically use secadm to disable PAGEEXEC and/or MPROTECT restrictions.

secadm also includes a feature known as Integriforce. Integriforce is
an implementation of verified execution. It enforces hash-based
signatures for binaries and their dependent shared objects.
Integriforce can be set in whitelisting mode. When there is at least
one Integriforce rule enabled, all desired applications and their
dependent shared objects must also have rules. If an application and
its shared objects are not included in the ruleset, execution of that
application will be disallowed. This also affects shared objects loaded
via [dlopen(3)](https://www.freebsd.org/cgi/man.cgi?query=dlopen&sektion=3&manpath=freebsd-release-ports).

When a file is added to secadm's ruleset, secadm will disallow
modifications to that file. This includes deleting, appending,
truncating, or otherwise modifying the file. This is because secadm
tracks files under its control by using the inode. Modifying the file
might change the inode, or freeing it in case of deletion, thereby
implicitly modifying the secadm ruleset. To protect the integrity of
the loaded ruleset, secadm also protects the files it controls.

Thus, when updating installed ports or packages, care must be taken.
Flush the ruleset prior to installing updates. The ruleset can be
reloaded after updating.

## Downloading and Installing secadm

secadm is not currently part of base, though that is planned in the
near future. secadm can be installed either through the package repo:

```
# pkg install secadm-kmod secadm
```

or by using HardenedBSD's ports tree:

```
# cd /usr/ports/hardenedbsd/secadm
# make install clean
# cd /usr/ports/hardenedbsd/secadm-kmod
# make install clean
```

## Configuring secadm

By default, secadm looks for a config file at
`/usr/local/etc/secadm.rules`. For purposes of this documentation,
that file will be simply referenced as `secadm.rules`. secadm does not
install or manage the `secadm.rules` file. It simply reads the file,
if it exists, passing the parsed data to the kernel module. secadm can
be configured either via the command-line or `secadm.rules`. Both
secadm and `secadm.rules` contain manual pages. Once installed, users
can look at the secadm manpage in section 8 and secadm.rules in
section 5.

`secadm.rules` should be in a format that libucl can parse as secadm
uses libucl to parse `secadm.rules`.

An example `secadm.rules` would look like this:

```
secadm {
	pax {
		path: "/usr/local/lib/firefox/firefox",
		mprotect: false,
	},
	pax {
		path: "/usr/local/lib/firefox/plugin-container",
		mprotect: false,
	},
}
```

Once secadm is configured, it can be started via the
[rc(8)](https://www.freebsd.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports)
system:

```
# sysrc secadm_enable=YES
# service secadm start
```

## All secadm configuration options

These are the available pax options:

| Option           	| Requirement	| Type		| Description									|
|-----------------------|---------------|---------------|-------------------------------------------------------------------------------|
| path			| Required	| String	| Fully-qualified path of the executable					|
| aslr			| Optional	| Boolean	| Toggle ASLR									|
| disallow_map32bit	| Optional	| Boolean	| Toggle the ability to use the MAP_32BIT [mmap(2)](https://www.freebsd.org/cgi/man.cgi?query=mmap&sektion=2&manpath=freebsd-release-ports) flag on 64-bit systems	|
| mprotect		| Optional	| Boolean	| Toggle mprotect restrictions							|
| pageexec		| Optional	| Boolean	| Toggle pageexec restrictions							|
| segvguard		| Optional	| Boolean	| Toggle SEGVGUARD								|
| shlibrandom		| Optional	| Boolean	| Toggle shared library load order randomization				|

Example pax configuration:

```
secadm {
	pax {
		path: "/usr/local/lib/firefox/firefox",
		mprotect: false,
	},
	pax {
		path: "/usr/local/lib/firefox/plugin-container",
		mprotect: false,
	},
}
```

These are the available integriforce options:

| Option           	| Requirement	| Type		| Description									|
|-----------------------|---------------|---------------|-------------------------------------------------------------------------------|
| path			| Required	| String	| Fully-qualified path of the executable or shared library			|
| hash			| Required	| String	| [sha1(1)](https://www.freebsd.org/cgi/man.cgi?query=sha1&sektion=1&manpath=freebsd-release-ports) or [sha256(1)](https://www.freebsd.org/cgi/man.cgi?query=sha256&sektion=1&manpath=freebsd-release-ports) hash of the file						|
| type			| Required	| String	| Type of hash. Either "sha1" or "sha256".					|
| mode			| Required	| String	| Either "soft" or "hard". In soft mode, if the hash doesn't match, a warning is printed in syslog and execution is allowed. In hard mode, if the hash doesn't match, an error is printed in syslog and execution is denied. |

Example integriforce configuration:

```
secadm {
	integriforce {
		path: "/bin/ls",
		hash: "7dee472b6138d05b3abcd5ea708ce33c8e85b3aac13df350e5d2b52382c20e77",
		type: "sha256",
		mode: "hard",
	}
}
```

# Contributing to HardenedBSD

HardenedBSD uses GitHub for source control and bug reports. Users can
submit bug reports for the HardenedBSD base source code
[here](https://git.hardenedbsd.org/HardenedBSD/HardenedBSD/issues) and for ports
[here](https://git.hardenedbsd.org/HardenedBSD/hardenedbsd-ports/issues). When
submitting bug reports, please include the following information:

* HardenedBSD version
* Architecture
* If the report concerns a kernel panic, the backtrace of the panic
* Steps to reproduce the bug

## HardenedBSD Development Process

HardenedBSD uses three repositories during the development process:

| Repository		| Purpose						|
|-----------------------|-------------------------------------------------------|
| [HardenedBSD](https://git.hardenedbsd.org/HardenedBSD/HardenedBSD)		| Main development repository				|

HardenedBSD development branches:

| Branch           			| Repository		| Binary Updates| Purpose						|
|---------------------------------------|-----------------------|---------------|-------------------------------------------------------|
| hardened/current/master		| HardenedBSD		| amd64, arm64	| Main development branch (14-CURRENT)			|
| hardened/13-stable/master		| HardenedBSD		| amd64		| 13-STABLE development					|
| hardened/12-stable/master		| HardenedBSD		| amd64		| 12-STABLE development					|

# Updating HardenedBSD

HardenedBSD does not use
[freebsd-update(8)](https://www.freebsd.org/cgi/man.cgi?query=freebsd-update&sektion=8&manpath=freebsd-release-ports).
Instead, HardenedBSD uses an utility known as hbsd-update. hbsd-update
does not use deltas for publishing updates, but rather distributes the
base operating system as a whole. Not utilizing deltas incurs a
bandwidth overhead, but is easier to maintain and mirror. hbsd-update
relies on DNSSEC-signed TXT records for distributing version information.

hbsd-update is configured via a config file placed at
`/etc/hbsd-update.conf`. hbsd-update works on a branch level, meaning it
tracks branches within HardenedBSD's source tree. Thus, updating from
one major version to another requires changing the dnsrec and branch
variables in `hbsd-update.conf`. For example, the `hbsd-update.conf`
for the hardened/current/master branch in the HardenedBSD repo:

```
dnsrec="$(uname -m).master.current.hardened.hardenedbsd.updates.hardenedbsd.org"
capath="/usr/share/keys/hbsd-update/trusted"
branch="hardened/current/master"
baseurl="http://updates.hardenedbsd.org/pub/HardenedBSD/updates/${branch}/$(uname -m)"
```

And as another example, the `hbsd-update.conf` for the
hardened/11-stable/master branch in the HardenedBSD repo:

```
dnsrec="$(uname -m).master.11-stable.hardened.hardenedbsd.updates.hardenedbsd.org"
capath="/usr/share/keys/hbsd-update/trusted"
branch="hardened/11-stable/master"
baseurl="http://updates.hardenedbsd.org/pub/HardenedBSD/updates/${branch}/$(uname -m)"
```

Thus, generating a diff between the two configuration files would result in:

```
--- hbsd-update_current.conf	2017-07-21 20:08:22.153616000 -0400
+++ hbsd-update_11-stable.conf	2017-07-21 20:08:38.003508000 -0400
@@ -1,4 +1,4 @@
-dnsrec="$(uname -m).master.current.hardened.hardenedbsd.updates.hardenedbsd.org"
+dnsrec="$(uname -m).master.11-stable.hardened.hardenedbsd.updates.hardenedbsd.org"
 capath="/usr/share/keys/hbsd-update/trusted"
-branch="hardened/current/master"
+branch="hardened/11-stable/master"
 baseurl="http://updates.hardenedbsd.org/pub/HardenedBSD/updates/${branch}/$(uname -m)"
```

[back to top](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/home#)
