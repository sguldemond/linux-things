# Linux things

### Video driver info

```
# lshw -c video
$ modinfo i915 | nvidia | nouveau
```

### About integrated GPU in XPS 15 9570 (Intel UHD Graphics 630)

https://www.reddit.com/r/archlinux/comments/8z09am/xps_15_9570_intel_graphics_optimize/

### Screen tearing fix (when using Nvidia driver)

```
# nano /etc/modprobe.d/zz-nvidia-modeset.conf
(old) options nvidia_375_drm modeset=1
(new) options nvidia_drm modeset=1

sudo update-initramfs -u
```

- https://www.cmscritic.com/stop-screen-tearing-with-optimus-laptops-using-nvidia-drivers-in-linux/

- https://askubuntu.com/questions/1185277/screen-tearing-in-linux-with-nvidia-graphics

### Screen tearing fix (Intel i915 graphics)

```
# mkdir -p /etc/X11/xorg.conf.d/
# nano /etc/X11/xorg.conf.d/20-intel.conf

Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "TearFree" "true"
EndSection
```

https://www.pcsuggest.com/fix-linux-screen-tearing/

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
```

# Turn on 'Synchronization’ NVIDIA X Server Settings

```
# crontab -e

@reboot nvidia-settings --assign CurrentMetaMode="nvidia-auto-select +0+0 { ForceFullCompositionPipeline = On }"
```

Reboot

https://askubuntu.com/questions/1185277/screen-tearing-in-linux-with-nvidia-graphics

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

### Post distro install packages

- zsh
- curl
- git
- mlocate (run `updatedb` after)
