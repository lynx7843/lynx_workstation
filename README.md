# Fedora 44 laptop Setup guide

installation and post setup guide for running fedora on a asus laptop specifically ``Zenbook 14 UX3405MA_Q415MA``. this system will be used for development and running light weight AI models. 
<br/>

## Table of content

#### part 1 - Installation
- [setting up bootable drive](#setting-up-bootable-drive)
- [preparing asus bios](#preparing-asus-bios)
- [os-installation](#os-installation)

#### part 2 - post installation system setup
- [dnf config](#dnf-config)
- [rpm fusion](#rpm-fusion)
- [codecs](#codecs)
- [firmware updates](#firmware-updates)
- [flatpak setup](#flatpak-setup)
- [appimage setup](#appimage-setup)
- [memory optimization](#memory-optimization)

### part 3 - developement environment setup
- [installing vs code](#installing-vs-code)
- [container tooling](#container-tooling)
- [git and core building tools](#git-and-core-building-tools)

### part 4 - light-weight gaming
- [installing steam and heroic launcher](#installing-steam-and-heroic-launcher)
- [gamemode and mangohud](@gamemode-and-mangohud)

### part 5 - customizing the gnome UI (macOS ricing)
- [theme](#theme)
- [icons](#icons)

#### troubleshooting
- [web camera](#web-camera)
- [bluetooth](#bluetooth)
- [timezone](#timezone)

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

## dnf config
1. open the dnf config file:
```bash
sudo nano /etc/dnf/dnf.conf
```
2. past the following under the main section
```bash
max_parallel_downloads=10
keepcache=True
```
3. update to test the changes
```bash
sudo dnf update -y
```

<br/>

## rpm fusion

1. enable both free and non-free repositories
```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```
2. run the following command to check the changes
```bash
sudo dnf repolist --all | grep -i fusion
```
> check if rpmfusion-free-rawhide and rpmfusion-free is enabled

<br/>

3. if the following are disabled, run the following commands
```bash
sudo dnf config-manager setopt rpmfusion-free.enabled=1
sudo dnf config-manager setopt rpmfusion-free-updates.enabled=1
sudo dnf config-manager setopt rpmfusion-free-rawhide.enabled=0
```

<br/>

## codecs

1. replace the **ffmpeg-free** package with rpm fusion
```bash
 sudo dnf swap ffmpeg-free ffmpeg --allowerasing
```
2. update the multimedia group
```bash
sudo dnf group upgrade multimedia
```
3. install the app stream metadata
```bash
sudo dnf group upgrade core
```

<br/>

## firmware updates

1. enable **terra** to install **zen editor**
```bash
sudo dnf install --nogpgcheck --repofrompath 'terra,https://repos.fyralabs.com/terra$releasever' terra-release
```
2. update the device firmware using:
```bash
fwupdmgr refresh --force
fwupdmgr get-devices
fwupdmgr get-updates
fwupdmgr update
```

<br/>

## flatpak setup

enable third party flatpak repository using the following command:
```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```

<br/>

## appimage setup

1. for app image support install fusion
```bash
sudo dnf install fuse-libs
```
2. install **Gearlever** to manage appimages
```bash
flatpak install it.mijorus.gearlever
```

<br/>

## memory optimization

gnome software runs silently in the background at startup to check for updates. this could eat up around 1GB of ram, stop it from autostarting by running the following commands.
```bash
mkdir -p ~/.config/autostart
cp /usr/share/applications/org.gnome.Software.desktop ~/.config/autostart/
echo "X-GNOME-Autostart-enabled=false" >> ~/.config/autostart/org.gnome.Software.desktop
```

<br/>

## installing vs code

to maintain lean memory usage and while providing complete development workspace, we need to configure efficient, native and manage necessary permissions. microsoft provides a stable rpm repo for fedora. run the following commands one by one to import the security key, add the repo and install it.
```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'
sudo dnf upgrade --refresh
sudo dnf install code
```

<br/>

## container tooling
instead of running docker, which runs continuous background daemon use fedora's native **podman**. install it alongside the docker alias packages so all your standard docker commands keep working, but instead of docker [command] you use podman [command].
```bash
sudo dnf install podman podman-docker podman-compose
```

<br/>

because podman is highly secure, it runs **rootless** by default. if you use local volume binds like mapping a local folder to a database container, fedora's **SELinux** might block it. to fix this when running container you need to append a ``:z`` to the volume path in your ``docker-compose.yml`` file.

```yml
volumes:
  - ./my-database-data:/data/db:Z
```

<br/>

## git and core building tools
install git along with standard development headers required for compiling native code dependencies.
```bash
sudo dnf install git gcc-c++ make
```

after installing git you have to configure it with your github account. run the following commands (replacing the quotes with actual credentials) to set your global identity.
```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

generate an ssh key using the following command.
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

start the ssh agent and add your newly created private key to it.
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

print your public key using the following command. copy the entire output.
```bash
cat ~/.ssh/id_ed25519.pub
```

<br/>

add the key to github by following these steps:
<br/>
1. log into github and click on the profile icon
2. click settings then **ssh and gpg keys** section
3. click on **new ssh key** button
4. give it a title, make sure type is set to **authentication key**
5. past the key you copied to **key** box and click add ssh key.

<br/>

test the connection by running the following command in the terminal
```bash
ssh -T git@github.com
```

<br/>

## installing steam and heroic launcher

steam handles primary libraries natively and for epic games and gog use heroic games launcher instead, this is a light-weight open-sourced alternative built specifically for linux. run the following commands to install both.
```bash
sudo dnf install steam
flatpak install flathub com.heroicgameslauncher.hgl
```

<br/>

## gamemode and mangohud

gamemode is a daemon that temporarily alters your laptop's power state, cpu governor and process priority. mangohud is a on-screen overlay that allows you to monitor your system info. install both using the following command
```bash
sudo dnf install gamemode mangohud
```

to enable these in your steam games paste the below command on game **launch options**
```bash
gamemoderun mangohud %command%
```

<br/>

## theme

install tweaks and extension manager for applying themes and UI modifications
```bash
sudo dnf install gnome-tweaks
flatpak install flathub com.mattjakeman.ExtensionManager
```
install extension manager and search and install the following
- user themes
- dash to dock
- blur my shell
- app grid tuner
- just perfection
- dock window preview

<br/>

theme that I am using it from: https://github.com/vinceliuice/WhiteSur-gtk-theme. run the following commands to install this theme. 
> note: I am using -c dark -t grey flags to ensure the theme render is a striking black and dark grey palette. remove these flags if you want to use the default transparent look.

```bash
sudo dnf install sassc glib2-devel
git clone https://github.com/vinceliuice/WhiteSur-gtk-theme.git --depth=1
cd WhiteSur-gtk-theme
./install.sh -c dark -t grey
```

apply the theme to gtk4 and fltpaks
```bash
./install.sh -l
./tweaks.sh -F
```

finally to apply the theme navigate to appearance tab in **tweaks** application and select WhiteSur-Dark-grey for **legacy applications** and **shell**.

<br/>

## icons

clone the icon repository using the following commands. this is created by the same developer under ``WhiteSur gtk theme`` thous this is more so a companion theme for the base **WhiteSur theme**.
```bash
git clone https://github.com/vinceliuice/WhiteSur-icon-theme.git --depth=1
```

run the installer with ``-a`` flag which will install all color varients
```bash
cd WhiteSur-icon-theme
./install.sh -a
```

apply the icons in tweaks by following the below steps
1. open teaks app and navigate to appearance tab
2. look for icon drop down menu
3. select **WhiteSur-dark**

<br/>

## troubleshoot

### web camera

one of the main recurring after a major fedora replace is web cam not working or malfunctioning. in my case it was default **vulkan** graphics. run the following command to fix it.
```bash
GSK_RENDERER=gl snapshot
```

<br/>

it can also be due to missing pipewire plugins. run this command to install it:
```bash
sudo dnf install gst-plugin-pipewire
```

<br/>

if none of the above worked your best option is to fallback to the classic camera app. use the following command to install it:
```bash
sudo dnf install cheese
```

<br/>

### bluetooth

if you are facing issues with bluetooth connectivity, reinstall the bluetooth drivers using the following command and reboot to apply the changes
```bash
sudo dnf reinstall bluez bluez-libs
```

<br/>

### timezone

if you are getting a message like bellow after entering ``timedatectl`` command in terminal it means that your motherboard hardware clock (RTC or Real Time Clock) is set to store your **local time** instead of UTC (Universal Time) and linux strongly disapproves it.
```bash
Local time: Tue 2026-09-08 00:19:54 +0530
Universal time: Mon 2026-09-07 18:49:54 UTC
RTC time: Mon 2026-09-07 18:53:59
Time zone: Asia/Colombo (+0530, +0530)

System clock synchronized: no
NTP service: active
RTC in local TZ: yes
```

<br/>

this issue may cause time to get outdated when the laptop is put to sleep or suspended. to fix this run the following commands one after the other.
```bash
sudo timedatectl set-local-rtc 0
sudo timedatectl set-ntp true
```

if the following commands didn't fixed the time issue it maybe because your device hasn't synced system clock (device hasn't checked the internet to get the real time yet). in fedora this is managed by a service called ``chrony``. run the following command to step the system clock immediately.

```bash
sudo chronyc makestep
```

if that didn't immediately snap gnome top bar to the correct time, restart the chrony service to trigger a fresh connection.
```bash
sudo systemctl restart chronyd
```

<br/>

this can also cause by dual booting the device with windows. windows by default saves the local time to the hardware clock and linux by default saves UTC to the hardware clock and then calculates your local time (like +05.30). to fix this run the above commands on your linux os and in windows run the following commands in windows by running ``cmd`` as admin.

```bash
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_DWORD /f
```

<br/>






