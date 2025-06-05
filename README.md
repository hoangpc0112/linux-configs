# Fedora 42 Workstation Post-Installation Guide

---

## Configure DNS Settings

### IPv4 DNS

- Primary: `8.8.8.8`
- Secondary: `8.8.4.4`

### IPv6 DNS

- Primary: `2001:4860:4860::8888`
- Secondary: `2001:4860:4860::8844`

---

## Update the System And Reboot

```bash
sudo dnf upgrade --refresh -y
sudo reboot
```

---

## Install Media Codecs

```bash
# Install multimedia group
sudo dnf group install -y multimedia

# Switch to full FFmpeg
sudo dnf swap -y ffmpeg-free ffmpeg --allowerasing

# Install additional sound and video packages
sudo dnf group install -y sound-and-video
```

---

## Hardware Video Acceleration

### Install VA-API

```bash
sudo dnf install -y ffmpeg-libs libva libva-utils
```

### Intel GPUs

```bash
sudo dnf swap -y libva-intel-media-driver intel-media-driver --allowerasing
sudo dnf install -y libva-intel-driver
```

### AMD GPUs

```bash
sudo dnf swap -y mesa-va-drivers mesa-va-drivers-freeworld
sudo dnf swap -y mesa-vdpau-drivers mesa-vdpau-drivers-freeworld
sudo dnf swap -y mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686
sudo dnf swap -y mesa-vdpau-drivers.i686 mesa-vdpau-drivers-freeworld.i686
```

### OpenH264 for Firefox

```bash
sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264
sudo dnf config-manager --set-enabled fedora-cisco-openh264
```

---

## Configure Disk Mounts

- Open **Disks** (GNOME Disk Utility).
- Select the target disk or partition.
- Click the gear icon and choose **Edit Mount Options**.
- Disable **User Session Defaults**.
- Set the **Mount Point** to `/mnt/<disk-name>` (e.g., `/mnt/storage`).
- Ensure **Mount at System Startup** is enabled.
- Click **OK** and authenticate with your password.

**Verification**:

```bash
ls /mnt/<disk-name>
```

---

## Install Hyprland ([Ja.KooLit Fedora Hyprland](https://github.com/JaKooLit/Fedora-Hyprland))

``` bash
git clone --depth=1 https://github.com/JaKooLit/Fedora-Hyprland.git ~/Fedora-Hyprland
cd ~/Fedora-Hyprland
chmod +x install.sh
./install.sh
```

---

## Install Applications

1. GNOME tweaks
2. OnlyOffice
3. VLC
4. Zen browser
5. Btop

---

## Configure Zen Browser

- Open Zen Browser and navigate to `about:config`.
- Search and set `layers.acceleration.force-enabled` to `True`.
- Search and set `gfx.webrender.all` to `True`.

---

## Install Browser Extensions

1.  [Bitwarden Password Manager](https://addons.mozilla.org/en-US/firefox/addon/bitwarden-password-manager/)
2.  [Enhancer for YouTube™](https://addons.mozilla.org/en-US/firefox/addon/enhancer-for-youtube/)
3.  [Dark Reader](https://addons.mozilla.org/en-US/firefox/addon/darkreader/)
4.  [uBlock Origin](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/)
5.  [Smooth Scroll](https://chromewebstore.google.com/detail/smoothscroll/nbokbjkabcmbfdlbddjidfmibcpneigj?hl=vi&pli=1) (Optional). Settings: `Step Size: 120`, `Smoothness: 999`, `Acceleration: 100`, `Sensitivity: 80`, `Friction: 10`, `Speed: 50`.

---

## Install Custom Fonts

### JetBrains Mono Nerd Font for terminal font

- Visit https://www.nerdfonts.com/font-downloads.
- Download **JetBrainsMono Nerd Font**.
- Visit https://fonts.google.com/specimen/Inter.
- Download **Inter** font.

### Apply Fonts

- Open **GNOME Tweaks** > **Fonts**.
- Set **Interface** and **Document** to **Inter**.
- Set **Monospace** to **JetBrains Mono Nerd Font**.

---

## Install Preload for Faster App Loading

```bash
sudo dnf copr enable kylegospo/preload -y
sudo dnf install preload -y
sudo systemctl enable --now preload
```

---

## Set Up Vietnamese Input

- Install IBus Unikey:
```bash
  sudo dnf install ibus-unikey -y
  ibus restart
  ```
- Install and open fcitx5-configtool
``` bash
  sudo dnf install fcitx5-configtool -y
  fcitx5-configtool
```
- Select Unikey from Available Input Method -> OK

**Verification**:

- Ctrl + Space to change input method, try `đ`, `ạ`.

**Note**: If Unikey doesn’t appear, restart IBus:

```bash
ibus restart
```

---

## Customize GRUB Bootloader

Apply the Elegant GRUB2 theme for a modern bootloader appearance.

```bash
# Clone the theme repository
git clone https://github.com/vinceliuice/Elegant-grub2-themes
cd Elegant-grub2-themes

# Install the Mojave theme
sudo ./install.sh -b -t mojave
```

**Explanation**:

- `-b`: Installs the theme to the GRUB configuration.
- `-t mojave`: Applies the Mojave theme.

**Verification**:

- Reboot and observe the GRUB menu:
  ```bash
  sudo reboot
  ```

**Troubleshooting**:

- If the theme doesn’t apply, ensure GRUB is configured correctly:
  ```bash
  sudo grub2-mkconfig -o /boot/grub2/grub.cfg
  ```

---

## Install Cursor and Icon Themes

- Create the `~/.icons` directory:

  ```bash
  mkdir -p ~/.icons
  ```

- **Cursor Theme** (Bibata Modern Ice):

  - Download from https://www.gnome-look.org/p/1197198/.
  - Extract the archive.
  - Copy theme folder to ~/.icons
  - Apply in **GNOME Tweaks** > **Appearance** > **Cursor**.

- **Icon Theme** (Papirus):
  - Download from https://www.gnome-look.org/p/1166289.
  - Extract the archive.
  - Copy theme folder to ~/.icons
  - Apply in **GNOME Tweaks** > **Appearance** > **Icons**.

---

## Optimizations

### Enable nvidia-modeset

- Useful if you have a laptop with an Nvidia GPU. Necessary for some PRIME-related interoperability features.

```bash
sudo grubby --update-kernel=ALL --args="nvidia-drm.modeset=1"
```

### Disable `NetworkManager-wait-online.service`

- Disabling it can decrease the boot time by at least ~15s-20s:

```bash
sudo systemctl disable NetworkManager-wait-online.service
```

### Disable GNOME Software from Startup Apps

- GNOME software autostarts on boot for some reason, even though it is not required on every boot unless you want it to do updates in the background, this takes at least 100MB of RAM upto 900MB (as reported anecdotically). You can stop it from autostarting by:

```bash
sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
```
