```
Clocksources:
	tsc (timestamp counter register)
	hpet
	acpi_pm

Kernel param: 
	clocksource=hpet (confirm though dmesg and /sys/devices/system/clocksource/*/current_clocksource)
```

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
sudo pacman -S --needed ttf-roboto
```

## To install

> sudo pacman -S --needed

```

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
