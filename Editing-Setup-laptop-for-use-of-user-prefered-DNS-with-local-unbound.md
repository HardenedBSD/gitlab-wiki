# Introduction

(This guide was tested with HardenedBSD 12-STABLE 1200515)

Usually users with laptops connect to several wireless networks and get their
settings regarding DNS nameservers. Because of this auto-setup, we may get the
DNS servers that may restrict our domain queries or log them to trace profiles.

Also if we do a query each time we change network and get new name-servers we
need to submit new queries and that is a overhead that could be exponential in
growth, so maybe there are gain in have a local DNS resolver running and
benefit from the cache factor and, like previously mentioned, gain some privacy.

HardenedBSD (and FreeBSD) have available local-unbound(8) that is a caching
DNS resolver.

## Process of connecting to network

The default process of connecting to wireless network with DHCP is:

1. select network 

2. dhclient(8) is called to obtain configuration where it reads
/etc/dhclient.conf(5) and execute dhclient-script(8) with data from the DHCP server

3. resolv.conf(5) is changed to reflect the network configuration

4. pinging hardenedbsd.org works

So every time we change wi-fi network, this process is repeated and we have the 
configuration file /etc/resolv.conf(5) rewritten.

## Run local-unbound

The normal process to connect to network with DHCP give us new DNS nameservers
to query, so we can avoid this regular change by running a DNS resolver
and we have `local-unbound` in base and easy available.

During a fresh install of HardenedBSD 12-STABLE you can activate `local_unbound`
on the setup program or if you're already running HardenedBSD, you can enable it
by running the command:

`sysrc local_unbound_enable=YES`

or add in /etc/rc.conf:

`local_unbound_enable="YES"`

After enable `local-unbound(8)` we need to start it:

`service local_unbound onestart`

This runs `local-unbound-setup` and configures the service. Adding several
changes, setting up chroot environment to run the service, and updating
configuration files:

- `/etc/resolv.conf`: add `nameserver 127.0.0.1` and comment out all others

- `/etc/unbound/*`: add conf files with defaults and `forward.conf` with nameservers
from `/etc/resolv.conf`

- `/etc/resolvconf.conf(5)`: this file is written by the script, and contains
`resolv_conf="/dev/null"` to prevent updating of this file by resolver

#### The process of connecting to network (revised)

The process now is as follows: 

1. select network 

2. `dhclient(8)` is called to obtain configuration where it reads
`/etc/dhclient.conf(5)` and execute `dhclient-script(8)` with data from router

3. `/etc/resolv.conf(5)` is changed to reflect the network configuration

4. `local-unbound(8)` updates its configuration but doesn't touch `/etc/resolv.conf(5)`

5. ping hardenedbsd.org doesn't works

## Solution to this problem

The problem for keeping your choice of DNS and running `local-unbound(8)` is
`dhclient(8)` rewriting our `/etc/resolv.conf(5)`. We should also setup the 
`local-unbound-setup` to not change `local-unbound(8)` configuration files.

### Stop dhclient rewrite /etc/resolv.conf

Every time `dhclient(8)` runs to get network configuration it changes 
`/etc/resolv.conf(5)`, specially removing `nameserver 127.0.0.1`.

To solve, create file /etc/dhclient-enter-hooks with the content:

```sh
# disable dhclient(8) rewriting resolv.conf(5) when setup network

add_new_resolv_conf() {
        return 0
}
```

This overrides the function that `dhclient-script(8)` has defined.

### Add unbound forward-zone config file

Create file /etc/unbound/conf.d/01-nameserver.conf with the content:

```sh
# user choice of DNS resolvers

forward-zone:
        name: "."
        forward-addr: IP
```

Where `IP` is the IP address of the DNS nameserver.
For more than one name-server, provide additional `forward-addr:` lines.

Note that by default `local-unbound` runs with DNSSEC active,
to disable check section "Disable DNSSEC"

Placing files in folder `conf.d` assures that every time `local-unbound-setup` is run
when connecting to network you don't lose your configuration.

More information: [unbound.conf(8)](https://www.freebsd.org/cgi/man.cgi?query=unbound.conf&apropos=0&sektion=0&manpath=FreeBSD+12.0-RELEASE+and+Ports&arch=default&format=html)

### Check /etc/resolv.conf

Make sure `/etc/resolv.conf(5)` as this entry `nameserver 127.0.0.1`.

*Note: Insert only this nameserver entry. If you have more, this must be first but check CAVEAT*

### CAVEAT

Forward-zone names have to be unique, so if you have `/etc/resolv.conf(5)`
multiple entries for nameservers, `local-unbound-setup` will fill 
`/etc/unbound/forward.conf` with those entries and the name of the zone
will be `name : "."`.

Note that `nameserver 127.0.0.1`as to be the first entry of nameservers.

This will result in error and may cause nondeterministic behaviour.

## Other options

#### Disable DNSSEC

Add `/etc/unbound/conf.d/99-disable-dnssec.conf`

```sh
# Disable unbound DNSSEC workings

server:
        harden-dnssec-stripped: no
        disable-dnssec-lame-check: yes
```

#### Use IPv6

For IPv6 users:

1. add `nameserver ::1` to /etc/resolv.conf(5)

2. add `forward-addr: Address` in IPv6 unbound config file with
nameservers.
