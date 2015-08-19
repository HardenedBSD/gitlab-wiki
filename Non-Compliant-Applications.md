The following applications need special handling with respect to exploit mitigation features in HardenedBSD. Each application's section will include sample secadm rules.

## Base secadm Configuration
    {
        applications: [
            /* Add per-application rules here*/
        ]
    }

## graphics/xpdf
### Incompatibilities
* SHLIBRANDOM

### secadm rule
    {
        path: "/usr/local/bin/xpdf",
        features: [
            shlibrandom: false,
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

## www/firefox
### Incompatibilities
* PAGEEXEC
* MPROTECT

### secadm rule
    {
        path: "/usr/local/lib/firefox/firefox",
        features: [
            mprotect: false,
            pageexec: false,
        ]
    }

## www/kdepim
### Incompatibilities
* PAGEEXEC
* MPROTECT

### secadm rule
    {
        path: "/usr/local/bin/kmail",
        features: [
            mprotect: false,
            pageexec: false,
        ]
    }


## www/privoxy
### Incompatibilities
* SHLIBRANDOM

### secadm rule
    {
        path: "/usr/local/sbin/privoxy",
        features: [
            shlibrandom: false,
        ]
    }

## Java
### Incompatibilities
* PAGEEXEC
* MPROTECT

### secadm rules
		{
			"path": "/usr/local/openjdk7/bin/appletviewer",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/openjdk7/bin/java",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/openjdk7/bin/javac",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/openjdk8/bin/java",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/openjdk8/bin/javac",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/openjdk8/jre/bin/java",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/openjdk7/jre/bin/java",
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/bin/mongo"
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},
		{
			"path": "/usr/local/bin/mongod"
			"features": {
				"mprotect": false,
                "pageexec": false,
			},
		},