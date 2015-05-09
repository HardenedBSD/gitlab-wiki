The following applications need special handling with respect to exploit mitigation features in HardenedBSD. Each application's section will include sample secadm rules.

## Base secadm Configuration
    {
        applications: [
            /* Add per-application rules here*/
        ]
    }

## www/chromium
### Incompatibilities
* PAGEEXEC
* MPROTECT

### secadm rule
    {
        path: "/usr/local/share/chromium/chrome",
        features: [
            mprotect: false,
            pageexec: false,
        ]
    }

## www/kdepim
### Incompatibilities
* /usr/local/bin/kmail: PAGEEXEC
* /usr/local/bin/kmail: MPROTECT

### secadm rule
    {
        path: "/usr/local/bin/kmail",
        features: [
            mprotect: false,
            pageexec: false,
        ]
    }