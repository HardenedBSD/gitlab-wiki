HardenedBSD developers do their work in feature branches, generally based on 11-CURRENT (FreeBSD HEAD). When ready, the feature branch is merged into the hardened/current/unstable branch for wider testing. After a period of time which changes due to the nature of the feature, the feature branch will then be merged into the hardened/current/master branch and deemed stable for general use. If possible, the feature will be backported to the hardened/10-stable/master branch.

Current branches:
* hardened/current/master
  * Tracking FreeBSD HEAD. HardenedBSD code deemed fit for general use.
* hardened/current/unstable
  * Tracking FreeBSD HEAD. HardenedBSD code deemed fit for general testing.
* hardened/10-stable/master
  * Tracking FreeBSD stable/10. HardenedBSD code backported to 10-STABLE.