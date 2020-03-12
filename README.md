## command-not-found (there are better options)
```
sudo pacman -S --needed pkgfile

pkgfile -u

echo "source /usr/share/doc/pkgfile/command-not-found.bash" >> ~/.bashrc
```
## irqbalance 
>(spread out interrupts across all cores)
```
sudo pacman -S --needed irqbalance

sudo systemctl enable irqbalance.service
```

## Ananicy <sup>[AUR](https://aur.archlinux.org/packages/ananicy-git/)</sup>
>(community driven automatic process cpu/io priority assignment)
```
trizen -S ananicy-git --noedit

sudo systemctl enable ananicy.service
```

## kwin-lowlatency <sup>[AUR](https://aur.archlinux.org/packages/kwin-lowlatency/)</sup>
>(fork of kwin with major performance improvements)
```
trizen -S kwin-lowlatency
```
- kwinrc (~/.config/kwinrc) **settings for my 240hz dispaly**
    ```
    [Compositing]
    AnimationCurve=2
    Backend=OpenGL
    Enabled=true
    GLCore=false
    GLPreferBufferSwap=n
    GLTextureFilter=2
    HiddenPreviews=5
    LatencyControl=0
    MaxFPS=240
    MaxLatency=4
    MinLatency=0
    OpenGLIsUnsafe=false
    RefreshRate=240
    UnredirectFullscreen=false
    VSyncMechanism=1
    WindowsBlockCompositing=true
    XRenderSmoothScale=true
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
-   ```
    MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)
    ```
> sudo mkinitcpio -p \<profile e.g linux-zen>
## Enable (basic) networking via systemd 
```
sudo systemctl enable systemd-networkd
sudo systemctl enable systemd-resolved
```
- > sudo vim /etc/systemd/network/20-wired.network
    ```
    [Match]
    Type=ether #Name=, PermanentMACAddress=, Path=, Driver=

    [Network]
    DHCP=ipv4

    [DHCP]
    RouteMetric=10
    ```
- > sudo vim /etc/systemd/network/25-wireless.network
    ```
    [Match]
    Type=wlan #Name=, PermanentMACAddress=, Path=, Driver=

    [Network]
    DHCP=ipv4

    [DHCP]
    RouteMetric=20
    ```