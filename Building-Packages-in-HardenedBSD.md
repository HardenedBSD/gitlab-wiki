Building Packages in HardenedBSD
================================

This article will document how we at HardenedBSD have set up
a local package building system for binary packages.
Using Poudriere on HardenedBSD has some extra steps compared
to setting it up on FreeBSD.

Requirements
------------

These are the requirements for our setup:

1. devel/git (or devel/git-lite)
1. ports-mgmt/poudriere (or ports-mgmt/poudriere-devel)

The Ports Tree
--------------

HardenedBSD maintains its own ports repository. We've added a special
hardening framework, which is easy to extend. The ports tree is on
[Gitea](https://git-01.md.hardenedbsd.org/HardenedBSD/hardenedbsd-ports). The ports
tree is synced every six hours with FreeBSD's. We resolve the
occasional merge conflicts usually within a twenty-four to forty-eight
hour period.

We install the ports tree to ```/usr/ports``` on the package building
server. We tell Poudriere that we will maintain the ports tree
ourselves. We've configured Poudriere to use the name ```local``` to
reference our ports tree.

```
# git clone https://git-01.md.hardenedbsd.org/HardenedBSD/hardenedbsd-ports.git \
    /usr/ports
# mkdir -p /usr/local/etc/poudriere.d/ports/local
# echo > /usr/local/etc/poudriere.d/ports/local/method
# echo /usr/ports > /usr/local/etc/poudreire.d/ports/local/mnt
```

Once this is done, you should be able to run ```poudriere ports -l```
and see that Poudriere knows about our ports tree:

```
# poudriere ports -l
PORTSTREE METHOD   TIMESTAMP PATH
local     -                  /usr/ports
```

If getting an error: ZPOOL variable is not set. 
Make sure that your ZPOOL value is configured correctly on your poudriere.conf.


Base System Source
------------------

In our setup, we assume that the system has a fully populated
`/usr/src`. We assume that ```make buildworld``` has been
performed previously and successfully.

If not, then follow these steps if on 12-STABLE:

When running the 12-stable release the kernel ABI can change without extra notices.
Building the world from the most recent commit will usually mean that your buildworld is ahead of your installed system. This will result in things breaking and kernel modules not loading, you don't want this.

To make sure this doesn't happen we first check the commit version of the running system with
```
~ cat /var/db/hbsd-update/version 
hbsd-v1200060-fb193275a276c540d1890a279e20e4515dd26aa2
```
So the git commit for this version is "fb193275a276c540d1890a279e20e4515dd26aa2"


Now we use this commit to build our world. Now we download the HardenedBSD source from the Github repo (it's recommended to use Github for the initial sync as they the downloads are faster) which will take some time.
After the download we checkout the same version as we are runnning.

sidenote always try to avoid running programs as root while connecting to the Internet when you don't need to.

```
# mkdir /usr/src
# chown <user>:wheel /usr/src
~ git clone --branch hardened/12-stable/master https://github.com/HardenedBSD/hardenedBSD.git /usr/src
~ cd /usr/src
~ git checkout fb193275a276c540d1890a279e20e4515dd26aa2 .
```

for reference here is how to sync from the official HardenedBSD repo
```
~ git clone --branch hardened/12-stable/master https://git-01.md.hardenedbsd.org/HardenedBSD/HardenedBSD.git /usr/src

```

Now we buildworld with the same version as the installed system.
This will take several minutes, depenging on how many cores and how fast they are.

```
# make -sj`sysctl -n hw.ncpu` buildworld
```




Poudriere will use the artifacts generated from the source tree at
`/usr/src`, so make sure that the source tree matches your target
deployment for base.

Check for existing poudriere jails:

```
# poudriere jail -l
```


If it is not needed it can be destroyd with:

```
# poudriere jail -d -j stable_amd64
```

Now create the HardenedBSD STABLE/amd64 jail:

```
# poudriere jail -c -v STABLE -p local -m src=/usr/src -j stable_amd64
```


Now as a test try building the pkg package with poudriere.
First create a file which lists ports that you want to compile and package.
Then start poudriere in bulk mode.
```
# echo 'ports-mgmr/pkg' > /usr/local/etc/poudriere.d/port-list
# poudriere bulk -j stable_amd64 -p local -f /usr/local/etc/poudriere.d/port-list
```

If you run into issues take a look at the reference poudriere.conf file below.


Poudriere Configuration File
----------------------------

Posted below is the Poudriere config file:

```

# Poudriere can optionally use ZFS for its ports/jail storage. For
# ZFS define ZPOOL, otherwise set NO_ZFS=yes
# 
#### ZFS
# The pool where poudriere will create all the filesystems it needs
# poudriere will use tank/${ZROOTFS} as its root
#
# You need at least 7GB of free space in this pool to have a working
# poudriere.
#
ZPOOL=tank

### NO ZFS
# To not use ZFS, define NO_ZFS=yes
#NO_ZFS=yes

# root of the poudriere zfs filesystem, by default /poudriere
ZROOTFS=/poudriere/rootfs

# the host where to download sets  for the jails setup
# You can specify here a host or an IP
# replace _PROTO_ by http or ftp
# replace _CHANGE_THIS_ by the hostname of the mirrors where you want to fetch
# by default: ftp://ftp.freebsd.org
#
# Also not that every protocols supported by fetch(1) are supported here, even
# file:///
#FREEBSD_HOST=http://0xfeedface.org/~shawn/nightlies/freebsd
FREEBSD_HOST=file:///src/release

# By default the jails have no /etc/resolv.conf, you will need to set
# REVOLV_CONF to a file on your hosts system that will be copied has
# /etc/resolv.conf for the jail, except if you don't need it (using an http
# proxy for example)
RESOLV_CONF=/etc/resolv.conf

# The directory where poudriere will store jails and ports
BASEFS=/poudriere/jails

# The directory where the jail will store the packages and logs
# by default a zfs filesystem will be created and set to
# ${BASEFS}/data
#
#POUDRIERE_DATA=${BASEFS}/data

# Use portlint to check ports sanity
USE_PORTLINT=no

# When building packages, a memory device can be used to speedup the build.
# Only one of MFSSIZE or USE_TMPFS is supported. TMPFS is generally faster
# and will expand to the needed amount of RAM. MFS is a bit slower, but is
# more mature and can have its memory usage capped.

# If set WRKDIRPREFIX will be mdmfs of the given size (mM or gG)
#MFSSIZE=32G

# Use tmpfs(5)
# This can be a space-separated list of options:
# wrkdir    - Use tmpfs(5) for port building WRKDIRPREFIX
# data      - Use tmpfs(5) for poudriere cache/temp build data
# localbase - Use tmpfs(5) for LOCALBASE (installing ports for packaging/testing)
# all       - Run the entire build in memory, including builder jails.
# yes       - Only enables tmpfs(5) for wrkdir
# EXAMPLE: USE_TMPFS="wrkdir data"
USE_TMPFS="all"

# If set the given directory will be used for the distfiles this allow the share
# the distfiles between jails and ports tree
DISTFILES_CACHE=/usr/ports/distfiles

# if set the ports tree marked to use csup method will use the defined mirror
#CSUP_HOST=cvsup._CHANGE_THIS_.freebsd.org

# if set the ports tree or source tree marked to use svn will use the defined
# mirror by default svn.FreeBSD.org
#SVN_HOST=svn.FreeBSD.org

# Automatic OPTION change detection
# When bulk building packages, compare the options from kept packages to
# the current options to be built. If they differ, the existing package
# will be deleted and the port will be rebuilt.
# Valid options: yes, no, verbose
# verbose will display the old and new options
#CHECK_CHANGED_OPTIONS=verbose

# Automatic Dependency change detection
# When bulk building packages, compare the dependencies from kept packages to
# the current dependencies for every port. If they differ, the existing package
# will be deleted and the port will be rebuilt. This helps catch changes such
# as DEFAULT_RUBY_VERSION, PERL_VERSION, WITHOUT_X11 that change dependencies
# for many ports.
# Valid options: yes, no
#CHECK_CHANGED_DEPS=yes


# Path to the RSA key to sign the PKGNG repo with. See pkg-repo(8)
#PKG_REPO_SIGNING_KEY=/usr/local/etc/ssl/keys/hardenedbsd.key


# ccache support. Supply the path to your ccache cache directory.
# It will be mounted into the jail and be shared among all jails.
#CCACHE_DIR=/var/cache/ccache
#

# parallel build support.
#
# By default poudriere uses hw.ncpu to determine the number of builders.
# You can override this default by changing PARALLEL_JOBS here, or
# by specifying the -J flag to bulk/testport.
#
# Example to define PARALLEL_JOBS to one single job
PARALLEL_JOBS=10

#PREPARE_PARALLEL_JOBS=5


# If set, failed builds will save the WRKDIR to ${POUDRIERE_DATA}/wrkdirs
# SAVE_WRKDIR=yes

# Choose the default format for the workdir packing: could be tar,tgz,tbz,txz
# default is tbz
# WRKDIR_ARCHIVE_FORMAT=tbz

# Disable linux support
NOLINUX=yes


# by default poudriere set PACKAGE_BUILDING
# to disable it:
# NO_PACKAGE_BUILDING=yes

# If you are using a proxy define it here:
# export HTTP_PROXY=bla
# export FTP_PROXY=bla
#
# Cleanout the restricted packages
# NO_RESTRICTED=yes

# By default MAKE_JOBS is disabled to allow only one process per cpu
# Use the following to allow it anyway
#ALLOW_MAKE_JOBS=yes


# Define as the URL that your POUDRIERE_DATA/logs is hosted at
# This will be used for giving URL hints to the HTML output when
# scheduling and starting builds
#URL_BASE=http://yourdomain.com/poudriere/


# This defines the max time (in seconds) that a command may run for a build
# before it is killed for taking too long. Default: 86400
MAX_EXECUTION_TIME=172800

# This defines the how long (in seconds) before a command is considered to
# be in a runaway state for having no output on stdout. Default: 7200
NOHANG_TIME=57600

URL_BASE=http:// <your custom domain here> /
USE_COLORS=no

#This is the only HardenedBSD specific part when comparing to FreeBSD setups.
JAIL_PARAMS="hardening.pax.aslr.status=1 hardening.pax.pageexec.status=1 hardening.pax.mprotect.status=1 hardening.pax.disallow_map32bit.status=1 hardening.pax.segvguard.status=1 allow.unprivileged_proc_debug=1"

BUILD_AS_NON_ROOT=no

ALLOW_MAKE_JOBS_PACKAGES="libreoffice* pkg chromium* iridium* ocaml-camomile*"
```
