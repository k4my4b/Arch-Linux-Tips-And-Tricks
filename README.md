# A collection of handy-dandy tips, tricks and How-to(s)

## Kernel Parameters
> kernel parameters are usually passed in via the [bootloader](https://wiki.archlinux.org/index.php/Arch_boot_process#Boot_loader) e.g. [grub](https://wiki.archlinux.org/index.php/GRUB). If you are using [dracut](https://wiki.archlinux.org/index.php/Dracut) kernel parameters can be built into the initramfs if you wish. </br> 
> To view the current kernel parameters run ``` cat /proc/cmdline ```

- [clocksource](https://www.kernel.org/doc/html/v4.12/admin-guide/kernel-parameters.html?highlight=clocksource) (used to set the default clock source.) **Note:** Certain early implmentaitons of HPET are buggy and the kernel will want to disable it, but if you are using a modern system e.g. intel 7th gen+ or an AMD Ryzen you should be using this. Bare in mind this will reduce the overall system performance but will result in greater consistency i.e. (reduced fps but more stable frametimes).

  ```bash
    clocksource=hpet # tsc (timestamp counter register)
                     # hpet
                     # acpi_pm

    # verify
    cat /sys/devices/system/clocksource/*/current_clocksource
  ```

- [intel_pstate](https://www.kernel.org/doc/html/v4.12/admin-guide/pm/intel_pstate.html#passive-mode) (intel specific [cpu frequency scaling](https://www.kernel.org/doc/html/v4.12/admin-guide/pm/cpufreq.html?highlight=acpi%20cpufreq) driver). **Note:** the pstate driver (at least for now) suffers from poor performance e.g. stuttering. It is best to either disable it completely and fallback to [acpi](https://www.kernel.org/doc/html/v4.12/admin-guide/pm/cpufreq.html?highlight=acpi%20cpufreq) driver if you have customised your cpu frequency e.g. overclocked, or set it to passive to bypass the driver's built-in governor but keep boost clocks.

  ```bash
    intel_pstate=<passive or disable>

    # verify
    cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver
  ```

## Pacman/package management related stuff.

- searching, installing, unstalling, updating and upgrading with pacman
  
  ```bash
    # To install
    sudo pacman -S

    # To uninstall
    sudo pacman -Runcs # (-d skip dependency version checks (-dd to skip all checks))

    # To refresh/update package database
    sudo pacman -Sy

    # To upgrade
    sudo pacman -Syu   # (-yy to force a refresh even if up to date)
                       # (-uu enables downgrades)
    # To search
    pacman -Ss         # (-q show less information for query and search)

    # To view specific package info
    pacman -Si         # (-ii for extended information)

    # To clean up uneeded/orphaned packges
    pacman -Runcs $(pacman -Qdtq)
  ```

- pacman output colorization

  ```bash
    #Color

    sudo sed -i 's/#Color/Color/g' /etc/pacman.conf
  ```

- pacman easter egg (changes progress bar to a Pac-Man)

  ```bash
    #ILoveCandy

    sudo sed -i 's/VerbosePkgLists/VerbosePkgLists\nILoveCandy/g' /etc/pacman.conf
  ```

- pacman verbose package change
  
  ```bash
    #VerbosePkgLists

    sudo sed -i 's/#VerbosePkgLists/VerbosePkgLists\n/g' /etc/pacman.conf
  ```

- updating mirrors

  ```bash
    # install reflector first if not installed already
    sudo pacman -S --needed reflector

    # use relfector --usage to determine the optimal flags for you
    sudo reflector --country <your country code e.g. gb> --ipv4 --protocol "http,https" --sort score --save /etc/pacman.d/mirrorlist
  ```

- adding a new key
  
  ```bash
    sudo pacman-key --recv-keys <key-id>
    pacman-key --finger <key-id> # shoudln't require root 
    sudo pacman-key --lsign-key  <key-id>
    sudo pacman-key --refresh-keys

    # you can use the --keyserver flag to specify a keyserver
  ```

- chaotic-aur: add user mainted repo of pre-built aur packages (some packages).
  
  ```bash
    trizen -S --noedit chaotic-mirrorlist chaotic-keyring

    # you will find the installed files under 
    # /etc/pacman.d/chaotic-mirrorlist
    # /usr/share/pacman/keyrings/chaotic*
  ```

## Useful packages to install

- ### **Siera Breeze Enhanced** <sup>[AUR](https://aur.archlinux.org/packages/kwin-decoration-sierra-breeze-enhanced-git/)</sup>

  ```bash
  trizen -S --noedit kwin-decoration-sierra-breeze-enhanced-git

  # compiled binary available in chaotic-aur repo
  ```

- ### **Latte-dock** <sup>[AUR](https://aur.archlinux.org/packages/latte-dock-git/)</sup>

  ```bash
  trizen -S --noedit latte-dock-git 

  # compiled binary available in chaotic-aur repo
  ```

- ### **ttf-roboto** (it's an awesome font that is a dependecy of multiple mainstream packages)

  ```bash
  sudo pacman -S --needed ttf-roboto ttf-roboto-mono
  ```

- ### **pkgfile** (command-not-found).

  ```bash
  sudo pacman -S --needed pkgfile

  sudo pkgfile -u # as pkgfile maintains a seperate database 
                  # it needs to be kept up-to-date

  echo "source /usr/share/doc/pkgfile/command-not-found.bash" >> ~/.bashrc
  ```

- ### **irqbalance** (spread out interrupts across all cores)

  ```bash
  sudo pacman -S --needed irqbalance

  # enable the systemd daemon
  sudo systemctl enable --now irqbalance.service
  ```

- ### **Ananicy** <sup>[AUR](https://aur.archlinux.org/packages/ananicy-git/)</sup> (community driven automatic process cpu/io priority assignment)

  ```bash
  trizen -S --noedit ananicy-git 

  # enable the systemd daemon
  sudo systemctl enable --now ananicy.service

  # compiled binary available in chaotic-aur repo
  ```

- ### **kwin-lowlatency** <sup>[AUR](https://aur.archlinux.org/packages/kwin-lowlatency/)</sup> (fork of kwin with major performance improvements)

  ```bash
  trizen -S --noedit kwin-lowlatency

  # edit kwinrc located at ~/.config/kwinrc
  # and replace everything under compositing
  # as follow if the section doesn't exist
  # then simply append this to the file.
  -------------------------------------------------------
  [Compositing]
  LatencyControl=0
  MaxLatency=0
  MinLatency=0
  OpenGLIsUnsafe=false
  UnredirectFullscreen=false
  -------------------------------------------------------

  # compiled binary available in chaotic-aur repo
  ```

## Drivers (Nvidia etc...)

- Nvidia drivers
  
  ``` bash
  # install the latest dkms module
  sudo pacman -S --needed nvidia-dkms

  # enable systemd nvidia services
  # DO NOT ISSUE WITH --now FLAG
  sudo systemctl enable nvidia-hibernate 
  sudo systemctl enable nvidia-resume
  sudo systemctl enable nvidia-suspend

  # open mkinitcpio.conf with editor of your choice
  sudo vim /etc/mkinitcpio.conf

  # find "MODULES" and append the nvidia driver modules
  MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)

  # enable nvidia-drm by passing kernel parameter
  # for example in the case of grub open the grub
  # config file located at /etc/default/grub
  # and append nvidia-drm.modeset=1 to 
  # GRUB_CMDLINE_LINUX_DEFAULT like so
  GRUB_CMDLINE_LINUX_DEFAULT=" ... nvidia-drm.modeset=1"

  # now rebuild the initramfs image and update grub
  sudo mkinitcpio -P && sudo grub-mkconfig -o /boot/grub/grub.cfg 
  ```

## Networking

- Enable (basic) networking via systemd

  ```bash
  # this will automatically manage interfaces
  sudo systemctl enable --now systemd-networkd

  # systemd equivilant to dhclient
  sudo systemctl enable --now systemd-resolved

  # to configure a new interface for networkd
  # we need to create a config file for it
  # under /etc/systemd/network/ as follow: 
  sudo vim /etc/systemd/network/20-wired.network
  
  # add the following to it (make any neccessary changes):
  -------------------------------------------------------
  [Match]
  Type=ether #Name=, PermanentMACAddress=, Path=, Driver=

  [Network]
  DHCP=ipv4

  [DHCP]
  RouteMetric=10
  -------------------------------------------------------

  # TODO add instructions for Wi-Fi
  ```
