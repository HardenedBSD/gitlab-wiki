# secadm

* Authors: Shawn Webb <shawn.webb@hardenedbsd.org>, Brian Salcedo <brian.salcedo@hardenedbsd.org>
* Copyright (c) 2014, 2015 Shawn webb <shawn.webb@hardenedbsd.org>
* License: 2-Clause BSD License

https://git.hardenedbsd.org/hardenedbsd/secadm

## Introduction

secadm is a project to replace the mac_bsdextended(4)/ugidfw(8)
integration the HardenedBSD project has done for ASLR, SEGVGUARD, and
PTrace hardening. The secadm project will be implemented as a custom
ports entry in the HardenedBSD/freebsd-ports repo. The port will
consist of three parts: a kernel module that integrates with the MAC
framework, a shared library that communicates between kernel and
userland, and an application that consumes the shared library.

The MAC module will work on a per-jail basis. It will communicate
with userland via a sysctl node. The MAC module should hook into the
execve() call to set per-process security/hardening flags, such as
toggling ASLR or SEGVGUARD. Each jail manages its own rules. Rules
applied in one jail do not interact or impact other jails.

The shared library will be named libsecadm and will simply act as a
communication layer between userland applications and the sysctl.
The shared library will perform the same sanitization and sanity
checking on all rule changes, including the removal of rules, that the
MAC module performs.

The userland application will be named secadm. It will consume libsecadm
and libucl. Rules will be written in json to allow for a
configuration file format that is readable and parseable by both
humans and machines. Using the json format will also allow for
additional flexibility and dynamic content. One can imagine secadm
deployed in a security appliance where the rulesets are created and
updated via a web service API.

secadm supports toggling ASLR, mmap(MAP_32BIT), SEGVGUARD, SHLIBRANDOM,
PAGEEXEC, and MPROTECT restrictions. As of version 0.2, secadm also
introduces a new Integriforce feature. Integriforce ensures executable
file integrity prior to execution.

## About Version 0.3.0

Version 0.3.0 is a complete rewrite of secadm. You'll notice that
commands like `secadm set` and `secadm list` no longer work. They have
been replaced by `secadm load /path/to/file` and `secadm show`
respectively. Additionally, individual rules can be added and deleted
with the `secadm add` and `secadm del` commands. Rules can be enabled
and disabled with the `secadm enable` and `secadm disable` commands.

The flags that can be passed to `secadm add pax` are:

* A, a: Enable, disable ASLR
* B, b: Enable, disable mmap(MAP_32BIT) protection
* L, l: Enable, disable SHLIBRANDOM
* M, m: Enable, disable MPROTECT
* P, p: Enable, disable PAGEEXEC
* S, s: Enable, disable SEGVGUARD
* O, o: Enable, disable hbsdcontrol based FS-EA rules overriding

By default, `secadm show` will show the active ruleset in abbreviated
format. secadm now integrates with libxo to provide ruleset output in
JSON, UCL, or XML formats. Specify a different format by using the -f
option to `secadm show`. For example, `secadm show -f ucl`.

## Order of rule evaluation

When the kernel is compiled with the PAX_CONTROL_EXTATTR kernel option, the
order of the evaluation is secadm then hbsdcontrol. This ensures that
the hbsdcontrol's settings always take precedence. To make secadm's
rules take precedence, use the O flag for that rule (prefer_acl is the
long option).

## Requirements

* HardenedBSD version 1200055 or greater:
  - `sysctl hardening.version` should show 1200055
* HardenedBSD kernel compiled with options PAX_CONTROL_ACL
* textproc/libucl
* textproc/libxo

## Installation And Usage

```
# make
# make depend all install
```

To list which per-applicatin features your version of secadm supports:

```
# secadm list features
```

To load the secadm kernel module:

```
# kldload secadm
```

Copy the sample ruleset to the right spot:

```
# cp etc/secadm-desktop.rules.example /usr/local/etc/secadm.rules
```

Edit your rules:

```
# vi /usr/local/etc/secadm.rules
```

Activate them. Please note that setting a new ruleset will flush your
previously-loaded rules.

```
# secadm load /usr/local/etc/secadm.rules
```

To verify that your ruleset loaded successfully:

```
# secadm list
```

To flush rules:

```
# secadm flush
```

Installing to a Jail
------------------------
The libsecadm shared library and secadm userland application must both be
installed into, or be accessible by, each jail individually in order to
enforce security policies inside of the jail.

Note: if jails are setup to use a read-only basejail, manual installation
of libsecadm.0.so into the basejail's /usr/lib directory is required.

Writing Application Rules
=========================

secadm currently supports toggling ASLR, SEGVGUARD, mprotect(exec)
hardening, and on certain HardenedBSD builds, PAGEEXEC hardening. In
the etc directory, you will find secadm.rules.sample, which shows
how to write rules.
You can use the prefer_acl keyword, to ensure secadm's rule takes
in effect over the file system extended attributes based settings.

secadm uses libucl for parsing its config file. As it stands right
now, the order of the rules do not matter, but that could change with
time as we add new features. The sample config file is in a relaxed
JSON format, though libucl supports different syntaxes. Please refer
to libucl's documentation for help in learning the different possible
syntaxes.

```
secadm {
	pax {
		path: "/bin/ls",
		aslr: false,
		segvguard: false
	},
	pax {
		path: "/bin/pwd",
		mprotect: true,
		pageexec: true,
		prefer_acl: true
	}
}
```

## Integriforce

secadm version 0.2 supports a new feature, called Integriforce. This
feature provides executable file integrity enforcement. If a rule
exists for a given file, that file's hash as defined in the rule is
matched against the hash of the file. If the hashes don't match,
execution may be disallowed, depending on the configuration settings.
Integriforce is an optional, but powerful, feature. Integriforce
currently supports only SHA1 or SHA256.

NOTE: Files that are under Integriforce management cannot be modified
or deleted. The ruleset will need to be flushed prior to
modifying or deleting the file.

### Configuring Integriforce

In the root object of the configuration file, secadm will look for an
integriforce object. In the integriforce object, add a files array. In
the files array, place an array of objects, where each object contains
the following options:

1. mode (string, default "hard"): If set, this must equal either
"soft" or "hard". Soft mode means execution is allowed if hashes don't
match, but a warning message is printed. Hard mode prints an error
messages and disallows execution.
1. files (array of objects): Each object must contain the following
fields:
   1. path (string): The path to the executable.
   1. hash (string): The hash (sha1 or sha256) of the executable.
   1. type (string): Either "sha1" or "sha256".
   1. mode (optional, string, default to inherit): The enforcing
      mode for this file.

Example config:

```
secadm {
	integriforce {
		path: "/bin/ls",
		hash: "873e49767e36f80a8814f41c90c98d888c83eeb4fe2fcab155b5ecb6cc6b67f6",
		type: "sha256",
		mode: "hard"
	}
}
```

### Strict Application Allow Listing

When Integriforce rules have been added, `secadm` can be placed in
strict application allow (whitelist) mode. `secadm` will prohibit the
execution of applications and the loading of shared libraries not
having a corresponding entry in the Integriforce configuration.

To enable strict application allow list mode:

```
# secadm set -W
```

To disable strict application allow list mode:

```
# secadm set -w
```

To enable strict application allow list mode in the `secadm`
configuration file:

```
secadm {
	whitelist_mode: true
}
```

Adding secadm itself and the shared libraries it depends on to the
Integriforce configuration is highly recommended. Otherwise, the
secadm configuration cannot be changed without first rebooting the
system.

## Trusted Path Execution (TPE)

Trusted Path Execution prevents users from directly executing binaries
in untrusted directories. A trusted directory is defined as a
directory that is writable only by root and owned by root. The current
TPE implementation does not handle the indirect execution of scripts.

TPE options that can be set:

1. `enable`:
   * Required. Type: Boolean
   * Description: Enable TPE protections.
1. `all`:
   * Optional. Type: Boolean
   * Description: Enable TPE for all users.
1. `invert`:
   * Optional. Type: Boolean
   * Description: Invert the Group ID (GID) logic.
1. `gid`:
   * Optional. Type: Integer
   * Description: Group ID for which TPE is applied.

The enable TPE via the command-line:

```
# secadm tpe -TA
```

Configuring TPE in the `secadm` configuation file:

```
secadm {
	tpe {
		enable: true,
		all: true,
		invert: false,
		gid: 1000
	}
}
```

## Note About ABI and API Stability

Both the userland and kernel ABI and API are under heavy development.
Though care has been taken to keep future changes and features in
mind, the API and ABI are not stable and may change from release to
release. If you plan to develop third-party applications that consume
libsecadm, please do so at your own risk. If you feel you need added
features or a change to an existing feature, please file a bug report
at secadm's issue tracker at HardenedBSD's self-hosted GitLab
instance.