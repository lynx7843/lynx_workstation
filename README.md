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

## os-installation
1. plugin the bootable flash drive and restart the laptop
2. during restarting repeatedly press `esc` to enter boot menu
3. select the fedora installer and boot live
> Important: test for wifi, bluetooth, camera, microphone and speakers. there can be issues after a major release

<br/>

4. after testing select install option
5. select you laptop drive and claim the entire space
6. wait for the installation to finish and click shut down, remove the flash drive and restart 


## post-installation-issues

one of the main recurring after a major fedora replace is web cam not working or malfunctioning. in my case it was default **vulkan** graphics. run the following command to fix it.
```bash
GSK_RENDERER=gl snapshot
```

<br/>

it can also be due to missing pipewire plugins. run this command to install it:
```bash
sudo dnf install gst-plugin-pipewire
```

if none of the above worked your best option is to fallback to the classic camera app. use the following command to install it:
```bash
sudo dnf install cheese
```

