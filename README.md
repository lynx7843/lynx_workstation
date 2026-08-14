# Fedora 44 laptop Setup guide

installation and post setup guide for running fedora on a asus laptop specifically Zenbook 14 UX3405MA_Q415MA. this system will be used for development and light weight gaming
<br/>

## Table of content

#### part 1 - Installation
- [setting up bootable drive](#setting-up-bootable-drive)
- [preparing asus bios](#preparing-asus-bios)
- [os-installation](#os-installation)
- [post installation issues](#post-installation-issues)


<br/>

## setting up bootable drive
1. download the latest fedora workstation ISO (Note: this setup is tailored for fedora 44)
2. flash the iso to a drive using a tool like **BalenaEtcher**
> alternatively you can use fedora media writer to fetch the latest iso and flash it to a usb drive

<br/>

## preparing asus bios
1. reboot asus laptop and repeatedly press `f2` to enter bios
2. navigate to security tab and disable `secure boot`
> while fedora support secure boot, disabling it temporarily prevents third-party driver issue

<br/>

3. find fast boot and disable it
4. save changes and exit (**f10**)
