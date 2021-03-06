# Linux things

### Video driver info

```
# lshw -c video
$ modinfo i915 | nvidia | nouveau
```

### About integrated GPU in XPS 15 9570 (Intel UHD Graphics 630)

https://www.reddit.com/r/archlinux/comments/8z09am/xps_15_9570_intel_graphics_optimize/


### Screen tearing

#### Nvidia driver

```
# nano /etc/modprobe.d/zz-nvidia-modeset.conf
(old) options nvidia_375_drm modeset=1
(new) options nvidia_drm modeset=1

sudo update-initramfs -u
```

- https://www.cmscritic.com/stop-screen-tearing-with-optimus-laptops-using-nvidia-drivers-in-linux/

- https://askubuntu.com/questions/1185277/screen-tearing-in-linux-with-nvidia-graphics

#### Intel i915 graphics

```
# nano /usr/share/X11/xorg.conf.d/20-intel.conf

Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "TearFree" "true"
EndSection
```

Original source: https://www.pcsuggest.com/fix-linux-screen-tearing/

_UPDATE 26-04-2020:_
- Changed location from `/etc/X11/xorg.conf.d` to `/usr/share/X11/xorg.conf.d` based on: https://manpages.debian.org/buster/xserver-xorg-core/xorg.conf.5.en.html
- Experiencing glitching/artifacts with `TearFree` on `true`, so currently disabled my wallpaper, this helps a bit.
- Glichting/artifacts and screen tearing fixed by switching to Wayland (instead of X11). This is done in by selecting `Ubuntu on Wayland` in the login screen.

More: https://forum.manjaro.org/t/screen-tearing-and-lagging-using-intel-open-source-drive-i915/95065

#### Diagonal tearing in Firefox

Following works well when running X11/Xorg not when running Wayland.

Visit `about:config`

Set `gfx.xrender.enabled` to `true`

Source: https://forums.linuxmint.com/viewtopic.php?p=1202538#p1202538


### Screen sharing under Wayland

When sharing screen in MS Teams while under Wayland, MS Teams crashed.

Sources to fix this:

- https://jgrulich.cz/2018/07/04/how-to-enable-and-use-screen-sharing-on-wayland/
- https://wiki.gnome.org/Projects/Mutter/RemoteDesktop
- https://gitlab.gnome.org/GNOME/mutter/-/issues/1184


### Prime select

Since the PRIME profiles selector is not showing in my Nvidia X Server Settings any more, using this tool you can do the same. Reboot after change is needed.

```
$ prime-select --help
$ prime-select query
$ prime-select intel | nvidia | on-demand
```

Not yet tested: https://github.com/matthieugras/Prime-Ubuntu-18.04

### Turn off GPU completely

To be tested: https://www.reddit.com/r/linux/comments/78is1r/complete_disable_of_discrete_gpu/

### Force hibernate on laptop close

```
# nano /etc/default/grub

GRUB_CMDLINE_LINUX_DEFAULT="quiet splash mem_sleep_default=deep"

# update-grub
```

### Turn on 'Synchronization’ NVIDIA X Server Settings

```
# crontab -e

@reboot nvidia-settings --assign CurrentMetaMode="nvidia-auto-select +0+0 { ForceFullCompositionPipeline = On }"
```

Reboot

https://askubuntu.com/questions/1185277/screen-tearing-in-linux-with-nvidia-graphics


### Keyboard LED control

#### X11

```
(on) $ xset led 3
(off) $ xset -led 3
```

Or use script:
```
#!/bin/bash
MASK=`xset q | grep LED | cut -d' ' -f 21 | cut -b 8`
if [ $(($MASK & 4)) = 4 ]; then
    xset -led 3
else
    xset led 3
fi
```


#### Wayland

`inputX` can differ!
```
(on) $ sudo sh -c 'echo 1 > /sys/class/leds/input15::scrolllock/brightness'
(off) $ sudo sh -c 'echo 0 > /sys/class/leds/input15::scrolllock/brightness'
```

Source: https://askubuntu.com/questions/967373/wayland-equivalent-to-xset-led


Or use script:
```
#!/bin/bash

BOOL=`cat /sys/class/leds/input15::scrolllock/brightness`

if [ $BOOL = 1 ]; then
    export NEW_BOOL=0
else
    export NEW_BOOL=1
fi

sudo -E sh -c 'echo $NEW_BOOL > /sys/class/leds/input15::scrolllock/brightness'
```


### Fan control

Source: https://askubuntu.com/questions/1094485/dell-xps-15-9570-how-to-control-the-fans
More info: https://www.cyberciti.biz/faq/controlling-dell-fan-speeds-temperature-on-ubuntu-debian-linux/

```
$ sensors
dell_smm-virtual-0
Adapter: Virtual device
fan1:           2531 RPM
fan2:           2532 RPM
```

```
# apt install i8kutils
# echo "i8k" > /etc/modules
# echo "options i8k force=1" > /etc/modprobe.d/i8k.conf
```

Reboot

http://keenformatics.blogspot.com/2013/06/how-to-solve-dell-laptops-fan-issues-in.html

```
$ git clone https://github.com/TomFreudenberg/dell-bios-fan-control.git
$ cd dell-bios-fan-control
$ make
# dell-bios-fan-control 0
```

https://aur.archlinux.org/cgit/aur.git/tree/dell-bios-fan-control.service?h=dell-bios-fan-control-git

Simply run i8kmon to control your fans or try ik8fan to manually control your fans.

```
# i8kfan 2 2
```

### Powertop

```
powertop --calibrate
powertop --auto-tune
```
```
cat /etc/systemd/system/powertop.service

[Unit]
Description=Powertop tunings

[Service]
Type=exec
ExecStart=/usr/bin/powertop --auto-tune
RemainAfterExit=true

[Install]
WantedBy=multi-user.target

https://github.com/fenrus75/powertop
https://wiki.archlinux.org/index.php/Powertop
```

### Setting mirror Arch Linux

```
nano /etc/pacman.d/mirrorlist
```

https://linuxhint.com/pacman_mirrors_arch_linux/


### Convert .msg file

```
sudo apt-get install libemail-outlook-message-perl
msgconvert filename*.msg
```

https://rohitmurame.blogspot.com/2018/10/how-to-open-msg-file-in-linux.html

### Port scanning

Ports of own machine

```
sudo netstat -lnp
```

To list all listening port numbers with the process responsible on each one. Terminate or kill the process to close port. (kill, pkill ...)

https://askubuntu.com/questions/410218/how-to-close-an-open-port-in-ubuntu

Ports of other machines

```
sudo apt install nmap
nmap -Pn google.com | 83.24.772.218
```

https://linuxhint.com/port_scan_linux/


### SSH Config

``` 
$ touch ~/.ssh/config
$ chmod 644 ~/.ssh/config
$ nano ~/.ssh/config

Host server-1
    HostName 671.288.17.927
    User stan

Host server-1-tunnel
    HostName 671.288.17.927
    User stan
    LocalForward 5432 127.0.0.1:5432

$ ssh server-1
$ ssh -N server-1-tunnel
```

https://shapeshed.com/manage-ssh-connections-with-a-ssh-config-file/

### Nano tabsize

```
$ nano ~/.nanorc
set tabsize 4
set tabstospaces
```

https://stackoverflow.com/questions/11173769/how-to-make-the-tab-character-4-spaces-instead-of-8-spaces-in-nano


### Citrix certificates

#### Certs from Mozilla

Add needed certificate to Citrix certificate folder `/opt/Citrix/ICAClient/keystore/cacerts/`

```
$ locate Nederland
...
/opt/Citrix/ICAClient/keystore/cacerts/Staat_der_Nederlanden_Root_CA_-_G3.crt
...
```

```
sudo ln -s /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts
```

https://forum.ubuntu-nl.org/index.php?topic=101708.0

#### Certs from Ubuntu

```
cd /opt/Citrix/ICAClient/keystore/ 
sudo mv cacerts cacerts_old 
sudo ln -s /etc/ssl/certs cacerts
```

https://discussions.citrix.com/topic/392126-receiver-138-no-cgp-service/

### pgAdmin4

#### How to install pgadmin4 in desktop mode in Ubuntu 20.04 with python 3.8.2 (April 25th, 2020)

Following main lines of this guide: https://www.pgadmin.org/download/pgadmin-4-python-wheel/

Install requirements:
```
# apt install build-essential python3-dev python3-venv
```

Setup venv:
```
$ cd ~
$ python3 -m venv pgadmin4
$ source pgadmin4/bin/activate
```

Install pgadmin4, make sure to change version to most recent when using the link, e.g. 4.17 > 4.20:
```
(pgadmin4) $ pip install https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v4.20/pip/pgadmin4-4.20-py2.py3-none-any.whl
```

Before error occurs create these folder yourself:
```
# mkdir /var/lib/pgadmin4
# mkdir /var/log/pgadmin4
```

And own them:
```
# chown -R $USER:$USER /var/lib/pgadmin4
# chown -R $USER:$USER /var/log/pgadmin4
```

Set desktop mode by creating new config file:
```
$ nano ~/pgadmin4/lib/python3.8/site-packages/pgadmin4/config_local.py

LOG_FILE = '/var/log/pgadmin4/pgadmin4.log'
SQLITE_PATH = '/var/lib/pgadmin4/pgadmin4.db'
SESSION_DB_PATH = '/var/lib/pgadmin4/sessions'
STORAGE_DIR = '/var/lib/pgadmin4/storage'
SERVER_MODE = False
```

Change in both files the same line:
```
$ nano ~/pgadmin4/lib/python3.8/site-packages/flask_wtf/recaptcha/validators.py
$ nano ~/pgadmin4/lib/python3.8/site-packages/flask_wtf/recaptcha/widgets.py

replace: from werkzeug import url_encode
with: from werkzeug.urls import url_encode
```
(Source: https://stackoverflow.com/questions/60896993/importerror-cannot-import-name-url-encode-from-werkzeug)

Run setup:
```
(pgadmin4) $ python ~/pgadmin4/lib/python3.8/site-packages/pgadmin4/setup.py 
```

I already had a `pgadmin4.db` with settings from my previous install, so I linked it:
```
$ cd /var/lib/pgadmin4
# ln -s ~/.pgadmin/pgadmin4.db pgadmin4.db
```

Try running it:
```
(pgadmin4) $ python ~/pgadmin4/lib/python3.7/site-packages/pgadmin4/pgAdmin4.py
```

### Dummy display

When remotely connecting to a (desktop) Ubuntu machine with no default connected physical display a dummy screen can be added. This way e.g. TeamViewer can always connect to a display even when none in connected to the machine.

Install package: `sudo apt-get install xserver-xorg-video-dummy`

Config files location: `/usr/share/X11/xorg.conf.d/`

Dummy display config:
```
Section "Device"
    Identifier  "Configured Video Device"
    Driver      "dummy"
EndSection

Section "Monitor"
    Identifier  "Configured Monitor"
    HorizSync 31.5-48.5
    VertRefresh 50-70
EndSection

Section "Screen"
    Identifier  "Default Screen"
    Monitor     "Configured Monitor"
    Device      "Configured Video Device"
    DefaultDepth 24
    SubSection "Display"
    Depth 24
    Modes "1920x1080"
    EndSubSection
EndSection
```

### Wifi keeps disconnecting

Attempt 1 DIDN'T FIX MY ISSUE

```
I had the same problem with this wifi card on Debian. Updating settings on router wasn't an option. What helped was to update binary firmware from here: https://github.com/kvalo/ath10k-firmware/tree/master/QCA6174/hw3.0/4.4.1

Just download last version and replace file firmware-6.bin located at /lib/firmware/ath10k/QCA6174/hw3.0/.

Then reload affected kernel modules:

modprobe -r ath10k_pci ath10k_core
modprobe ath10k_pci
modprobe ath10k_core
```

https://askubuntu.com/questions/870412/wifi-dell-xps-13-9360-keeps-disconnecting-with-qca6174-802-11ac-wireless-network

Attempt 2 DIDNT WORK
```
I finally solved this after browsing and trying a couple of threads on various blogs.

The problem: My router is old and didn't supported 802.11n protocol, that caused the internet to slow down only in Ubuntu because by default it was being used.

The Solution: To force disable the 802.11 protocol permanently:

Step 1: Open iwlwifi.conf using nano or any other text editor.

sudo nano /etc/modprobe.d/iwlwifi.conf

Step 2: Add a line to force disable 802.11n protocol

options iwlwifi 11n_disable=1

Step 3: Save and reboot your PC.

NOTE: The changes won't take effect until you reboot your PC.

If you want to reverse the changes the please remove the line you've added and reboot.

This solved my problem and If your PC has the same issue, I'm sure it will solve yours too.
```

https://askubuntu.com/questions/1243381/wifi-is-slow-and-unstable-on-ubuntu-20-04-dual-boot-dell-xps-l502x


Attempt 3

```
Edit /etc/NeworkManager/conf.d/default-wifi-powersave-on.conf

In that file you will see a line that looks like this;

wifi.powersave = 3

Change the 3 to a 2 (which means disabled) and reboot. The problem should be resolved.
```

https://www.reddit.com/r/SurfaceLinux/comments/g8dqbr/ubuntu_2004_wifi_problem/
https://gist.github.com/jcberthon/ea8cfe278998968ba7c5a95344bc8b55


### WiFi got really slow after update in Ubuntu 20.04.1

Fix:
```
sudo apt install backport-iwlwifi-dkms
```

Info:

https://askubuntu.com/questions/1046589/backport-for-iwlwifi

### Remove orphaned packages

https://sites.google.com/site/easytipsforlinux/using-deborphan

```
$ sudo apt-get install deborphan
```

### Thunderbird

#### Add-ons

- ExQuilla for Exchange
- TbSync
- Thunderbird Conversations
- Provider for Google Calender
- Provider for Exchange ActiveSync
- Lightning


### Apps

- Psensor
- Gparted
- Grub Customizer

#### Work

- Slack
- Citrix Workspace
- Signal
- Insomnia
- pgAdmin4
- PyCharm CE
- Skype
- Microsoft Teams

#### Other

- Telegram

#### CLI

- zsh, then run `chsh -s $(which zsh)`
- curl
- git
- mlocate, then run `updatedb`
- [docker](https://docs.docker.com/engine/install/ubuntu/)
- net-tools, for ifconfig