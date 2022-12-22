# How to create your own production build base + packages build environment

## Creating the base update artifact

HardenedBSD provides a tool to build binary updates for base, called
`hbsd-update-build`. This is a shell script that builds the update artifact that
can be built once and installed many times on disparate systems.

`hbsd-update-build` assumes that `/usr/src` is populated and a `make buildworld`
has been done previously. It will use this to build a chroot in which it builds
the binary update artifact.

Configuring `hbsd-update-build` for your environment is simple. For a list of
all the settings you can change in `hbsd-update-build`, look at the
`setup_environment` function in `/usr/sbin/hbsd-update-build`.

If I were to want to build a 13-STABLE update artifact, I would create a config
file with the following settings:

```
BRANCH="hardened/13-stable/master"
INTEGRIFORCE=0
UNSIGNED=1
```

Then, I would run `hbsd-update-build`, passing `-c /path/to/config` as an
argument.

At the end of the build process, `hbsd-update-build` will print out a single
line with two words separated by a space. The first word is the status, whether
the build succeeded ("OK") or failed ("FAILED"). The second word, if the build
succeeded, is the version string that should be placed in a file called
`update-latest.txt`.

The `update-latest.txt` file should be placed in the same directory from which
you will serve (likely via HTTP(S)) the update artifact. You can find the
resulting artifact at its default location of /builds/updater/output.

So, when `hbsd-update-build` completes, if it was successfull, I should see a
message like:

```
OK 1670383090|hbsd-v1400003-1ed85d694008e8ca6fa3edd10cf9720e75c169d1|sha256:084d308d478a734c88ac21bf61dd80a389b3c6ecb0d3cc3bb5954b451391b83c
```

I would create a file called `update-latest.txt` that would contain:

```
1670383090|hbsd-v1400003-1ed85d694008e8ca6fa3edd10cf9720e75c169d1|sha256:084d308d478a734c88ac21bf61dd80a389b3c6ecb0d3cc3bb5954b451391b83c
```

I would set my HTTP(S) web server (in my case, nginx) to expose the directory:

```
http {
    ... snip ...

    server {
        ... snip ...

        location /updates {
                alias /builds/updater/output;
                autoindex on;
        }
    }
}
```

## Building packages

I would use the `poudriere-hbsd` port/package build packages. I would follow the
steps documented by the FreeBSD project to set up Poudriere.

There are a few crucial bits needed in `poudriere.conf`:

```
JAIL_PARAMS="hardening.pax.aslr.status=1 hardening.pax.pageexec.status=1 hardening.pax.mprotect.status=1 hardening.pax.disallow_map32bit.status=1 hardening.pax.segvguard.status=1 allow.unprivileged_proc_debug=1 allow.extattr=1 hardening.harden_rtld=0"
BUILD_AS_NON_ROOT=no
```

Make sure to use the following for src and ports when configuring Poudriere:

src repo: https://git.hardenedbsd.org/HardenedBSD/HardenedBSD.git
ports repo: https://git.hardenedbsd.org/HardenedBSD/ports.git

src branch:
 * 14-current: hardened/current/master
 * 13-stable: hardened/13-stable/master

ports branch: hardenedbsd/main

## Configuring hbsd-update

Here's the config file I use for my home infrastructure:

```
dnsrec=""
capath="/usr/share/keys/hbsd-update/trusted"
baseurl="http://hbsd-build-02.ip6.home.lan/updates/current"
dnssec="no"
unsigned=1
```

Then I put hbsd-update to use that config file

```
# hbsd-update -V \
    -b name_of_zfs_boot_environment_to_install_into \
    -c /path/to/home/config/file
```

## Configuring pkg

I disable the main HardenedBSD repo by creating
`/usr/local/etc/pkg/repos/HardenedBSD.conf` with the following text:

```
HardenedBSD: {
    enabled: no
}
```

Then I create my local repo config by creating
`/usr/local/etc/pkg/repos/local.conf` with the following text:

```
Local_Repo: {
    url: "http://hbsd-build-02.ip6.home.lan/pkg/${ABI}",
    mirror_type: "http",
    enabled: yes
}
```
