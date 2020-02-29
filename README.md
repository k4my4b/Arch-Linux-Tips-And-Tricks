## command-not-found

```
sudo pacman -S --needed pkgfile

pkgfile -u

echo "source /usr/share/doc/pkgfile/command-not-found.bash" >> ~/.bashrc
```
## irqbalance

```
sudo pacman -S --needed irqbalance

sudo systemctl enable irqbalance.service
```

## Ananicy

```
trizen -S ananicy-git --noedit

sudo systemctl enable ananicy.service
```