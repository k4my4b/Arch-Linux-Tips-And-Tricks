## kernels that failed the test of desktop responsive vs high workload

```
linux-ck
linux-lqx
linux-clear
linux-pf
linux-xanmod
```

linux remains quite balanced and should suffice for most people \
linux-zen runs scheduler at 1000hz, it's a bit more aggressive and better suited for realtime applications.

## Kernel Parameters

> HPET (High Precision Event Timer). Certain early implmentaitons of the HPET are buggy and the kernel will want to disable it, but if you are using a modern system e.g. intel 7th gen+ or an AMD Ryzen you should be using this. Bare in mind this will reduce the overall system performance but will result greater consistency i.e. (reduced fps but more stable frametimes). [Check out the kernel parameters docs here](https://www.kernel.org/doc/html/v4.12/admin-guide/kernel-parameters.html?highlight=clocksource)

```
clocksource=hpet # tsc (timestamp counter register)
                 # hpet
                 # acpi_pm

cat /sys/devices/system/clocksource/*/current_clocksource
```

> CPU Frequency Scaling Driver, the pstate driver (at least for now) suffers from poor performance e.g. stuttering. It is best to either disable it completely and fallback to acpi driver if you have customised your cpu frequency e.g. overclocked, or set it to passive to bypass the driver but keep boost clocks. [Read about intel_pstate here](https://www.kernel.org/doc/html/v4.12/admin-guide/pm/intel_pstate.html#passive-mode).

```
intel_pstate=<passive> or <disable>

cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_driver 
```

## Siera Breeze Enhanced <sup>[AUR](https://aur.archlinux.org/packages/kwin-decoration-sierra-breeze-enhanced-git/)</sup>

```
trizen -S kwin-decoration-sierra-breeze-enhanced-git --noedit
```

## Latte-dock

```
trizen -S latte-dock-git --noedit
```

## ttf-roboto

```
sudo pacman -S --needed ttf-roboto ttf-roboto-mono
```

## command-not-found (there are better options)

```
sudo pacman -S --needed pkgfile

pkgfile -u

echo "source /usr/share/doc/pkgfile/command-not-found.bash" >> ~/.bashrc
```

## irqbalance

> (spread out interrupts across all cores)

```
sudo pacman -S --needed irqbalance

sudo systemctl enable irqbalance.service
```

## Ananicy <sup>[AUR](https://aur.archlinux.org/packages/ananicy-git/)</sup>

> (community driven automatic process cpu/io priority assignment)

```
trizen -S ananicy-git --noedit

sudo systemctl enable ananicy.service
```

## kwin-lowlatency <sup>[AUR](https://aur.archlinux.org/packages/kwin-lowlatency/)</sup>

> (fork of kwin with major performance improvements)

```
trizen -S kwin-lowlatency
```

- kwinrc (~/.config/kwinrc) **settings for my 240hz dispaly**
  ```
  [Compositing]
  LatencyControl=0
  MaxLatency=0
  MinLatency=0
  OpenGLIsUnsafe=false
  UnredirectFullscreen=false
  ```

## Install Nvidia drivers

```
sudo pacman -S --needed nvidia-dkms
```

## Enable Nvidia services

```
sudo systemctl enable nvidia-hibernate
sudo systemctl enable nvidia-resume
sudo systemctl enable nvidia-suspend
```

## Adding Nvidia driver modules to initramfs

> sudo vim /etc/mkinitcpio.conf

- ````
      MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
      ```
  > sudo mkinitcpio -p \<profile e.g linux-zen>
  ````

## Enable (basic) networking via systemd

```
sudo systemctl enable systemd-networkd

sudo systemctl enable systemd-resolved
```

- > sudo vim /etc/systemd/network/wired.network

      ```
      [Match]
      Type=ether #Name=, PermanentMACAddress=, Path=, Driver=

      [Network]
      DHCP=ipv4

      [DHCP]
      RouteMetric=10
      ```

  `The following is only needed for wifi enabled systems`

- > sudo vim /etc/systemd/network/wireless.network

  ```
  [Match]
  Type=wlan #Name=, PermanentMACAddress=, Path=, Driver=

  [Network]
  DHCP=ipv4

  [DHCP]
  RouteMetric=20
  ```

```
sudo pacman -S --needed wpa_supplicant
```

- > sudo vim /etc/wpa_supplicant/wpa_supplicant-\<interface name>.conf
  ```
  ctrl_interface=/run/wpa_supplicant
  ctrl_interface_group=wheel
  update_config=1
  eapol_version=1
  ap_scan=1
  fast_reauth=1
  ```

```
sudo systemctl enable wpa_supplicant@<interface name>
```

`wpa_gui provides an interface for all wifi related stuff you'll need, run it with -qt to start (q)uitely in (t)ray`

```
trizen -S wpa_supplicant_gui
```
