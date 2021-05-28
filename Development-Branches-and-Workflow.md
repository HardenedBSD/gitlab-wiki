HardenedBSD developers do their work in feature branches, generally based on CURRENT (FreeBSD HEAD). When ready, the feature branch is merged into the _hardened/current/master_ branch for wider testing. After a period of time which changes due to the nature of the feature and if possible, the feature will be backported to the stable branches.

Current branches:
* [hardened/current/master](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD)
  * Tracking FreeBSD HEAD. HardenedBSD code deemed fit for general use.
* [hardened/13-stable/master](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/tree/hardened/13-stable/master)
  * Tracking FreeBSD stable/13. HardenedBSD code backported to 13-STABLE.
* [hardened/12-stable/master](https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/tree/hardened/12-stable/master)
  * Tracking FreeBSD stable/12. HardenedBSD code backported to 13-STABLE.