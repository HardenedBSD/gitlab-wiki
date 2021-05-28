# Stable to Current
So you might be considering the move from stable to current but not sure why. There are several reasons why you might want to move to current. Below is just a small subset of reasons why;

- You want to help the community
- You need a feature that is only currently in
- You want to be on the bleeding edge of hardenedbsd

Whatever your reason, this article will detail the process to get you their. This article assumes two things. First that you are using ZFS. The second that you have installed the boot enviroments administration package. The reason for these two is that zfs gives us the ability to snapshot our environment. Combine that with BE (boot environments) and you have the ability to keep your current known working state intact in case something should happen. 

## The Pre Setup
The first step will be to 

```shell
cd ${HOME}
```

and then run 

```shell
fetch https://raw.githubusercontent.com/HardenedBSD/hardenedBSD/hardened/current/master/usr.sbin/hbsd-update/hbsd-update.conf
```

This will pull down the hardened bsd update configuration file that we will use while leaving the config file in /etc untouched. 


## Lets Start the upgrade process
Now the second thing we will do is create a bootable environment with our -current configuration we pulled down. This can be done by issuing 
```shell
hbsd-update -I -V -b current`date +%Y%m%d`
```

which will create a bootable environment named current with the current date appended to it. once this completes make sure to carefully fix any conflict in configuration files. 
Once those are fixed lets verify that you have a new configuration. This can be done by issuing the following command

```shell
beadm list
```
you should see in the ouput 

```shell
root@hardenedbsd:~ # beadm list
BE              Active Mountpoint  Space Created
default         N      /           95.4M 2018-04-05 20:24
current20180530 R      -          887.4M 2018-05-30 09:35
```

If you have an R next to the current then you are set. That means that on Reboot it will be your active environment. Now lets reboot and complete the rest of the tasks

```shell
shutdown -r now
```

## Update your pkg to a newer version
Great you are now in your new current hardenedbsd environment. But we still have more todo. Lets start by first updating our pkg since current uses newer libraries and a newer ssl version.
This can be done by issuing the following commands

```shell
pkg-static clean -y
pkg-static upgrade -f pkg
pkg-static upgrade -f
pkg-static upgrade -f 
```

No you are not seeing things. We ran the pkg-static upgrade -f twice. after that lets reboot again 

## Fixing /usr/src
if you previously download your src tree you will want to remove it and then set it to the current running version. This can be done by doing the following

```shell
rm -fr /usr/src/*
git clone https://github.com/HardenedBSD/hardenedBSD /usr/src
cd /usr/src
git reset --hard `cut -f 3 -d '-' /var/db/hbsd-update/version`
```

Once you run those four commands your /usr/src tree should be in sync with your current hardenedbsd version.