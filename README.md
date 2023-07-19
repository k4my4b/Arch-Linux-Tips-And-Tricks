# A collection of handy-dandy tips, tricks and How-to(s)

## Pacman/package management related stuff.

- **searching, installing, unstalling, updating and upgrading with pacman**

  ```bash
  # To install
  sudo pacman -S

  # To uninstall
  # NOTE (-d skip dependency version checks (-dd to skip all checks))
  sudo pacman -Runcs

  # To refresh/update package database
  sudo pacman -Sy

  # To upgrade
  # NOTE (-yy to force a refresh even if up to date)
  # NOTE (-uu enables downgrades)
  sudo pacman -Syu

  # To search remotely
  # NOTE (-q show less information for query and search)
  pacman -Ss

  # To search locally
  # NOTE (-q show less information for query and search)
  pacman -Qs
  
  # To search for a package that provides X.
  pacman -F <X>

  # To view remote package info
  # NOTE (-ii for extended information)
  pacman -Si

  # To view installed package info
  # NOTE (-ii for extended information)
  pacman -Qi

  # To view packages installed by you
  pacman -Qe

  # To view packages installed as dependencies
  pacman -Qd

  # To clean up uneeded/orphaned packges
  sudo pacman -Runcs $(pacman -Qdtq)
  ```

- **pacman parallel downloading**

  ```bash
  #ParallelDownloads = 5

  sudo sed -i 's/^#ParallelDownloads/ParallelDownloads/g' /etc/pacman.conf
  ```

- **pacman output colorization**

  ```bash
  #Color

  sudo sed -i 's/^#Color/Color/g' /etc/pacman.conf
  ```

- **pacman easter egg** (changes progress bar to a Pac-Man)

  ```bash
  #ILoveCandy

  sudo sed -i 's/VerbosePkgLists/VerbosePkgLists\nILoveCandy/g' /etc/pacman.conf
  ```

- **pacman verbose package change**

  ```bash
  #VerbosePkgLists

  sudo sed -i 's/^#VerbosePkgLists/VerbosePkgLists\n/g' /etc/pacman.conf
  ```

- **(automatically) updating mirrors**

  ```bash
  # install reflector first if not installed already
  sudo pacman -S --needed reflector

  # delete everything from the reflector service config file 
  # and replace them with only the following flags
  # /etc/xdg/reflector/reflector.conf
  --protocol https
  --sort score
  --save /etc/pacman.d/mirrorlist

  # enable the systemd timer to automatically update the mirrors every week
  sudo systemctl enable --now reflector.timer
  ```

- **adding a new key**

  ```bash
  sudo pacman-key --recv-keys <key-id>
  pacman-key --finger <key-id> # shoudln't require root
  sudo pacman-key --lsign-key  <key-id>
  sudo pacman-key --refresh-keys

  # you can use the --keyserver flag to specify a keyserver
  ```

- **[chaotic-aur](https://aur.chaotic.cx/)** (user maintained repo of pre-built popular AUR packages).

  ```bash
  # add chaotic-aur keys
  sudo pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com

  # sign said keys
  sudo pacman-key --lsign-key 3056513887B78AEB

  # install the keyring and chaotic mirrorlist packages so
  # they can be updated in the future
  sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-'{keyring,mirrorlist}'.pkg.tar.zst'

  # you will find the installed files under
  # /etc/pacman.d/chaotic-mirrorlist
  # /usr/share/pacman/keyrings/chaotic*
  # you must now add the repo to pacman.conf
  # as follow

  echo -e "\n[chaotic-aur]\nInclude = /etc/pacman.d/chaotic-mirrorlist" | sudo tee -a /etc/pacman.conf

  # Refresh
  sudo pacman -Syy
  ```

## Useful packages to install

- ~~**[rng-tools](https://wiki.archlinux.org/index.php/Rng-tools)** (The rng-tools is a set of utilities related to random number generation in kernel)~~
  > [rng-tools is not needed anymore since Kernel 5.6 because /dev/random does not block anymore.](https://github.com/torvalds/linux/commit/30c08efec8884fb106b8e57094baa51bb4c44e32)

  <details>

    ```bash
    # first check your entropy count
    cat /proc/sys/kernel/random/entropy_avail

    sudo pacman -S rng-tools opensc

    # enable service
    sudo systemctl enable --now rngd.service

    # verify your entropy count is higher
    cat /proc/sys/kernel/random/entropy_avail
    ```

  </details>

- **Siera Breeze Enhanced** <sup>[AUR](https://aur.archlinux.org/packages/kwin-decoration-sierra-breeze-enhanced-git/)</sup>

  ```bash
  trizen -S --noedit kwin-decoration-sierra-breeze-enhanced-git

  # pre-built package available in chaotic-aur repo
  ```

- ~~**Latte-dock** <sup>[AUR](https://aur.archlinux.org/packages/latte-dock-git/)</sup>~~
  > [The development of latte-dock ceased back in Jul 2022, thus the project is officially dead](https://www.phoronix.com/news/Latte-Dock-Ends).
  
  <details>
  
  ```bash
  trizen -S --noedit latte-dock-git

  # pre-built package available in chaotic-aur repo
  ```
  
  </details>

- **ttf-roboto** (it's an awesome font that is a dependecy of multiple mainstream packages)

  ```bash
  sudo pacman -S --needed ttf-roboto ttf-roboto-mono
  ```

- **pkgfile** (command-not-found).
  > In case you run a command that is not available on your system pkgfile hook will suggest a package that contains said command
  ```bash
  sudo pacman -S --needed pkgfile
  
  # as pkgfile maintains a seperate database it needs to be kept up-to-date
  sudo pkgfile -u
  
  # enable automatic pkgfile database updating
  sudo systemctl enable --now pkgfile-update.timer

  # for bash
  echo "source /usr/share/doc/pkgfile/command-not-found.bash" >> ~/.bashrc

  # for zsh
  echo "source /usr/share/doc/pkgfile/command-not-found.zsh" >> ~/.zshrc
  ```

- **[irqbalance](https://wiki.archlinux.org/index.php/improving_performance#irqbalance)** (spread out interrupts across all cores) </br>
  > This is may help with increased system throughput but will result in increased system latency and jitter

  ```bash
  sudo pacman -S --needed irqbalance

  # enable the systemd daemon
  sudo systemctl enable --now irqbalance.service
  ```

- **[Ananicy](https://wiki.archlinux.org/index.php/improving_performance#Adjusting_priorities_of_processes)** <sup>[AUR](https://aur.archlinux.org/packages/ananicy-cpp/)</sup> (community driven automatic process cpu/io priority assignment)

  ```bash
  trizen -S --noedit ananicy-cpp

  # enable the systemd daemon
  sudo systemctl enable --now ananicy.service

  # pre-built package available in chaotic-aur repo
  ```

- ~~**kwin-lowlatency** <sup>[AUR](https://aur.archlinux.org/packages/kwin-lowlatency/)</sup> (fork of kwin with major performance improvements)~~

  > In the beginning of 2021, [a massive change set](https://invent.kde.org/plasma/kwin/-/merge_requests/507) has been merged to KWin which pretty much defeats the point of this project.

  <details>

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

  # pre-built package available in chaotic-aur repo
  ```

  </details>

- **update-grub** <sup>[AUR](https://aur.archlinux.org/packages/update-grub/)</sup> (Simple wrapper around grub-mkconfig)

  ```bash
  trizen -S --noedit update-grub

  # pre-built package available in chaotic-aur repo
  ```

## Drivers (Nvidia etc...)

- **Nvidia drivers**

  ```bash
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
  sudo mkinitcpio -P && sudo update-grub
  ```

## Networking

- **Enable (basic) networking via systemd**

  ```bash
  # this will automatically manage interfaces
  sudo systemctl enable --now systemd-networkd

  # systemd equivalent for dhclient
  sudo systemctl enable --now systemd-resolved

  # systemd equivalent for resolvconf
  sudo pacman -S --needed systemd-resolvconf

  # link systemd-resolvd controlled resolv.conf to
  # /etc/resolv.conf
  sudo ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf

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

## Boot/Splash screen

- **[Plymouth](https://wiki.archlinux.org/index.php/plymouth)** (Plymouth is an independant module desgined to be called during the
  boot process to provide a graphical boot screen.)

  ```bash
  pacman -S --needed plymouth

  # add plymouth hook to mkinitcpio.conf
  # open /etc/mkinitcpio.conf with the editor
  # of your choice and make the following changes
  # *NOTE* It must be added after base and udev
  HOOKS=(base udev plymouth ...)

  # enable boot splash screen by passing the "splash" parameter to
  # the kernel command line.
  # e.g. edit grub config (/etc/default/grub) as follows:
  -------------------------------------------------------
  GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 quiet splash"
  -------------------------------------------------------

  # If your system boots too fast to show the splash screen
  # you can forcefully delay the process for N seconds
  sudo systemctl edit plymouth-quit.service
  -------------------------------------------------------
  [Service]
  ExecStartPre=/usr/bin/sleep 3
  -------------------------------------------------------

  # to set the change the plymouth theme
  # run the following command
  sudo plymouth-set-default-theme <theme-name> # (see --list for available themes)

  # you must also make sure the graphics driver is included
  # in the initramfs, refer to Drivers section
  # for doing this nvidia for example.
  # once everything is done go ahead and rebuild the
  # initramfs and update grub
  # *NOTE* everytime you make a change to plymouth
  # e.g. change the theme initramfs needs to be rebuilt
  sudo mkinitcpio -P && sudo update-grub
  ```

## [Kernel Parameters](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)

> kernel parameters are usually passed in via the [bootloader](https://wiki.archlinux.org/index.php/Arch_boot_process#Boot_loader) e.g. [grub](https://wiki.archlinux.org/index.php/GRUB). If you are using [dracut](https://wiki.archlinux.org/index.php/Dracut) kernel parameters can be built into the initramfs if you wish. </br>
> To view the current kernel parameters run `cat /proc/cmdline`

- **[clocksource](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)** (used to set the default clock source.) </br>
**Note:** Certain early implmentaitons of HPET are buggy and the kernel will want to disable it, but if you are using a modern system e.g. AMD Ryzen you should, probably, be using HPET. Bare in mind clocksource seems to grealty affect the system throughput and latency and it would require a lot of testing to determine the proper extent of what these changes could mean for you (and their side effects).</br>
**Note:** [Mesa Fixes A Large Performance Regression For Systems Using HPET](https://www.phoronix.com/news/Mesa-Fixes-HPET-Regression). In order to force enable tsc on AMD systems you must add the following to your kernel cmdline `tsc=reliable`</br>
**Note:** To list available clocksources run `cat /sys/devices/system/clocksource/**/available_clocksource` </br>
**Note:** To verify run `cat /sys/devices/system/clocksource/**/current_clocksource`
  ```bash
  clocksource=hpet # tsc (timestamp counter register)
                   # hpet
                   # acpi_pm
                   # jiffies (DO NOT USE THIS OR ELSE ...)
  ```
- **[skew_tick](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=weight_single%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20and%20weight_many.-,skew_tick,-%3D%20%20%20%20%20%20%5BKNL%5D%20Offset%20the)** ("Offset the periodic timer tick per cpu to mitigate xtime_lock contentio") </br>
**Note:** This can help with reducing system latency (on larger systems) at the cost of increased power consumption.
  ```bash
  skew_tick=1 # enable
            0 # disable
  ```

- **[intel_pstate](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)** (intel specific [cpu frequency scaling](https://www.kernel.org/doc/html/v4.12/admin-guide/pm/cpufreq.html?highlight=acpi%20cpufreq) driver).</br>
**Note:** the pstate driver (at least for now) suffers from poor performance e.g. stuttering. It is best to either disable it completely and fallback to [acpi](https://www.kernel.org/doc/html/v4.12/admin-guide/pm/cpufreq.html?highlight=acpi%20cpufreq) driver if you have customised your cpu frequency e.g. overclocked, or set it to passive to bypass the driver's built-in governor but keep boost clocks. </br>
**Note:** To verify run `cat /sys/devices/system/cpu/**/cpufreq/scaling_driver`

  ```bash
  intel_pstate=no_hwp #recommended
               disable
               passive
  ```
- **[amd_pstate](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=support%20is%20present.\)-,amd_pstate,-%3D%20%20%20%20%20%5BX86%5D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20disable%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20Do)** (AMD scaling driver for the supported processors). </br>
**Note:** To verify run `cat /sys/devices/system/cpu/**/cpufreq/scaling_driver`
  ```bash
  amd_pstate=guided #recommended
             active
             passive
             disable
  ```
- **[console_msg_format](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=VisioBraille%20is%20supported.-,console_msg_format,-%3D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5BKNL%5D%20Change%20console)** (Change console messages format)
  ```bash
  console_msg_format=syslog #recommended
                     default
  ```
- **[cpufreq.default_governor](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=cpufreq.default_governor%3D)** (set the default cpu governor)
  ```bash
  cpufreq.default_governor=schedutil #recommended
                           powersave
                           performance
                           userspace
                           ondemand
                           conservative
  ```
- **[lsm](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=initialization%20debugging%20output.-,lsm%3D,-lsm1%2C...%2ClsmN%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%5BSECURITY)** (Linux Security Modules) </br>
**Note:** To verify/check the current LSM list run `cat /sys/kernel/security/lsm`
  ```bash
  lsm=capability,landlock,lockdown,yama,apparmor,bpf # this will enable apparmor you will also need to install the apprmor package for userspace utils
  ```
- **[iommu](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=from%20userspace.%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20relaxed-,iommu%3D,-%5BX86%5D%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20off%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20force)** (Set IOMMU mode)
  ```bash
  iommu=pt # sets iommu mode to pass-through
  
  # see docs for more options
  ```
- **[intel_iommu](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=integrity%20auditing%20messages.-,intel_iommu%3D,-%5BDMAR%5D%20Intel%20IOMMU)** (Intel IOMMU driver options) </br>
**Note:** Intel IOMMU is off by default on most (if not all) Intel platforms
  ```bash
  intel_iommu=on
              off 
  
  # see docs for more options
  ```
- **[amd_iommu](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html#:~:text=for%20more%0A%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20%20information.-,amd_iommu%3D,-%5BHW%2CX86%2D64)** (AMD IOMMU driver options) </br>
**Note:** AMD iommu mode is enabled by default
  ```bash
  amd_iommu=off
            force_enable # Force enable the IOMMU on platforms known to be buggy with IOMMU enabled.

  # see docs for more options
  ```