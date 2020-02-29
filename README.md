## command-not-found
```
sudo pacman -S --needed pkgfile

pkgfile -u

echo "source /usr/share/doc/pkgfile/command-not-found.bash" >> ~/.bashrc
```
## irqbalance (spread out interrupts across all cores)
```
sudo pacman -S --needed irqbalance

sudo systemctl enable irqbalance.service
```

## Ananicy (community driven automatic process cpu/io priority assignment)
```
trizen -S ananicy-git --noedit

sudo systemctl enable ananicy.service
```

## kwin-lowlatency (fork of kwin with major performance improvements)
```
trizen -S kwin-lowlatency
```
- kwinrc (~/.config/kwinrc)
    ```
    [Compositing]
    AnimationCurve=2
    Backend=OpenGL
    Enabled=true
    GLCore=true
    GLPreferBufferSwap=n
    GLTextureFilter=2
    HiddenPreviews=5
    LatencyControl=0
    MaxFPS=240
    MaxLatency=8
    MinLatency=0
    OpenGLIsUnsafe=false
    RefreshRate=240
    UnredirectFullscreen=true
    VSyncMechanism=0
    WindowsBlockCompositing=true
    XRenderSmoothScale=false
    ```
## 