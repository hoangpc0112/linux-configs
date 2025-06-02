# Fedora 42 Workstation Post-Installation Guide

---

## Table of Contents

1. [Optimize DNF Package Manager](#1-optimize-dnf-package-manager)
2. [Configure DNS Settings](#2-configure-dns-settings)
3. [Enable RPM Fusion Repositories](#3-enable-rpm-fusion-repositories)
4. [Update the System](#4-update-the-system)
5. [Install Media Codecs](#5-install-media-codecs)
6. [Install NVIDIA Drivers](#6-install-nvidia-drivers)
7. [Configure Disk Mounts](#7-configure-disk-mounts)
8. [Set Up Custom Keybindings](#8-set-up-custom-keybindings)
9. [Install Applications](#9-install-applications)
10. [Configure Zen Browser](#10-configure-zen-browser)
11. [Install GNOME Extensions](#11-install-gnome-extensions)
12. [Install Browser Extensions](#12-install-browser-extensions)
13. [Install Custom Fonts](#13-install-custom-fonts)
14. [Install Preload for Faster App Loading](#14-install-preload-for-faster-app-loading)
15. [Set Up Vietnamese Input](#15-set-up-vietnamese-input)
16. [Customize GRUB Bootloader](#16-customize-grub-bootloader)
17. [Install Cursor and Icon Themes](#17-install-cursor-and-icon-themes)
18. [Optimizations](#18-optimizations)

---

## 1. Optimize DNF Package Manager

```bash
sudo tee -a /etc/dnf/dnf.conf << EOF
fastestmirror=True
max_parallel_downloads=10
EOF
```

**Explanation**:

- `fastestmirror=True`: Automatically selects the fastest available mirror for package downloads.
- `max_parallel_downloads=10`: Allows up to 10 simultaneous downloads, reducing wait times.

---

## 2. Configure DNS Settings

### 2.1. IPv4 DNS

- Primary: `8.8.8.8`
- Secondary: `8.8.4.4`

### 2.2. IPv6 DNS

- Primary: `2001:4860:4860::8888`
- Secondary: `2001:4860:4860::8844`

**Instructions**:

- Open **Settings** > **Network**.
- Select your active connection (Wi-Fi or Ethernet).
- Click the gear icon to edit settings.
- Navigate to the **IPv4** or **IPv6** tab.
- Set **DNS** to **Manual** and enter the addresses above, separated by commas.
- Save and reconnect to apply changes.

---

## 3. Enable RPM Fusion Repositories

```bash
sudo dnf install \
  https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

---

## 4. Update the System

```bash
sudo dnf upgrade --refresh -y
```

**Explanation**:

- `--refresh`: Forces DNF to check for new metadata.
- `-y`: Automatically confirms prompts to streamline the process.

**Note**: Reboot if a kernel update is applied:

```bash
sudo reboot
```

---

## 5. Install Media Codecs

```bash
# Install multimedia group
sudo dnf group install -y multimedia

# Switch to full FFmpeg
sudo dnf swap -y ffmpeg-free ffmpeg --allowerasing

# Install additional sound and video packages
sudo dnf group install -y sound-and-video
```

### Hardware Video Acceleration

#### Install VA-API

```bash
sudo dnf install -y ffmpeg-libs libva libva-utils
```

#### Intel GPUs

For Intel chipsets (5th Gen and above):

```bash
sudo dnf swap -y libva-intel-media-driver intel-media-driver --allowerasing
sudo dnf install -y libva-intel-driver
```

#### AMD GPUs

For AMD chipsets, replace Mesa drivers with freeworld versions:

```bash
sudo dnf swap -y mesa-va-drivers mesa-va-drivers-freeworld
sudo dnf swap -y mesa-vdpau-drivers mesa-vdpau-drivers-freeworld
sudo dnf swap -y mesa-va-drivers.i686 mesa-va-drivers-freeworld.i686
sudo dnf swap -y mesa-vdpau-drivers.i686 mesa-vdpau-drivers-freeworld.i686
```

#### OpenH264 for Firefox

Enable H.264 video support in Firefox:

```bash
sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264
sudo dnf config-manager --set-enabled fedora-cisco-openh264
```

---

## 6. Install NVIDIA Drivers

```bash
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda -y
```

**Explanation**:

- `akmod-nvidia`: Ensures the driver is recompiled for new kernel updates.
- `xorg-x11-drv-nvidia-cuda`: Includes CUDA libraries for GPU computing.
- Wait 5-10 minutes for the driver to compile, then reboot:

```bash
sudo reboot
```

**Verification**:

```bash
nvidia-smi
```

This should display GPU information if the driver is installed correctly.

---

## 7. Configure Disk Mounts

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

## 8. Set Up Custom Keybindings

| Action                    | Shortcut              |
| ------------------------- | --------------------- |
| Switch to Workspace 1-4   | `Alt + [1-4]`         |
| Move Window to Workspace  | `Shift + Alt + [1-4]` |
| Open Code Editor          | `Alt + C`             |
| Open Terminal             | `Alt + Enter`         |
| Open Browser              | `Alt + B`             |
| Open Files                | `Alt + E`             |
| Hide Window               | `Alt + X`             |
| Close Window              | `Alt + Q`             |
| Switch Windows            | `Alt + Tab`           |
| Toggle maximization state | `Alt + Z`             |

---

## 9. Install Applications

1. Gnome tweaks
2. Only office
3. VLC
4. Zed editor
5. Zen browser
6. Extension manager
7. Ghostty terminal

```bash
sudo dnf copr enable pgdev/ghostty -y
sudo dnf install ghostty -y
```

8. Btop

```bash
sudo dnf install btop -y
```

---

## 10. Configure Zen Browser

- Open Zen Browser and navigate to `about:config`.
- Click **Accept the Risk and Continue**.
- Search and modify the following settings:
  - `layers.acceleration.force-enabled`: Set to `True`.
  - `gfx.webrender.all`: Set to `True`.

**Explanation**:

- These settings enable hardware acceleration and WebRender for smoother rendering.
- Restart the browser after applying changes.

---

## 11. Install GNOME Extensions

1.  [Blur My Shell](https://extensions.gnome.org/extension/3193/blur-my-shell/)
2.  [Burn My Windows](https://extensions.gnome.org/extension/4679/burn-my-windows/)
3.  [Clipboard Indicator](https://extensions.gnome.org/extension/779/clipboard-indicator/)
4.  [Forge](https://extensions.gnome.org/extension/4481/forge/)
5.  [GNOME 4x UI Improvements](https://extensions.gnome.org/extension/4158/gnome-40-ui-improvements/)
6.  [No Overview at Start-Up](https://extensions.gnome.org/extension/4099/no-overview/)
7.  [Space Bar](https://extensions.gnome.org/extension/5090/space-bar/)
8.  [Window Title Is Back](https://extensions.gnome.org/extension/6310/window-title-is-back/)
9.  [Tray Icons: Reloaded](https://extensions.gnome.org/extension/2890/tray-icons-reloaded/)

---

## 12. Install Browser Extensions

1.  [Bitwarden Password Manager](https://addons.mozilla.org/en-US/firefox/addon/bitwarden-password-manager/)
2.  [Enhancer for YouTube™](https://addons.mozilla.org/en-US/firefox/addon/enhancer-for-youtube/)
3.  [Dark Reader](https://addons.mozilla.org/en-US/firefox/addon/darkreader/)
4.  [uBlock Origin](https://addons.mozilla.org/en-US/firefox/addon/ublock-origin/)
5.  [Smooth Scroll](https://chromewebstore.google.com/detail/smoothscroll/nbokbjkabcmbfdlbddjidfmibcpneigj?hl=vi&pli=1) (Optional). Settings: `Step Size: 120`, `Smoothness: 999`, `Acceleration: 100`, `Sensitivity: 80`, `Friction: 10`, `Speed: 50`.

---

## 13. Install Custom Fonts

### 13.1. JetBrains Mono Nerd Font for terminal font

- Visit https://www.nerdfonts.com/font-downloads.
- Download **JetBrainsMono Nerd Font**.
- Extract the archive
- Install font

### 13.2. Inter Font for UI and document font

- Visit https://fonts.google.com/specimen/Inter.
- Download and extract the **Inter** font archive
- Install font

### 13.3. Apply Fonts

- Open **GNOME Tweaks** > **Fonts**.
- Set **Interface** and **Document** to **Inter**.
- Set **Monospace** to **JetBrains Mono Nerd Font**.

---

## 14. Install Preload for Faster App Loading

```bash
sudo dnf copr enable kylegospo/preload -y
sudo dnf install preload -y
sudo systemctl enable --now preload
```

**Explanation**:

- Preload monitors application usage and caches files in memory.
- The service runs automatically on boot.

**Verification**:

```bash
systemctl status preload
```

---

## 15. Set Up Vietnamese Input

Configure IBus Unikey for typing in Vietnamese.

- Install IBus Unikey:
  ```bash
  sudo dnf install ibus-unikey -y
  ibus restart
  ```
- Open **Settings** > **Keyboard** > **Input Sources**.
- Click **+** and search for **Vietnamese**.
- Select **Vietnamese (Unikey)**.
- Switch input methods using `Super + Space` (or configure a custom shortcut).

**Verification**:

- Test typing in a text editor with Vietnamese characters (e.g., `đ`, `ạ`).

**Note**: If Unikey doesn’t appear, restart IBus:

```bash
ibus restart
```

---

## 16. Customize GRUB Bootloader

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

## 17. Install Cursor and Icon Themes

- Create the `~/.icons` directory:

  ```bash
  mkdir -p ~/.icons
  ```

- **Cursor Theme**:

  - Download from https://www.gnome-look.org/p/1197198/.
  - Extract the archive.
  - Copy theme folder to ~/.icons
  - Apply in **GNOME Tweaks** > **Appearance** > **Cursor**.

- **Icon Theme**:
  - Download from https://www.gnome-look.org/p/1166289.
  - Extract the archive.
  - Copy theme folder to ~/.icons
  - Apply in **GNOME Tweaks** > **Appearance** > **Icons**.

## 18. Optimizations

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

### Disable Gnome Software from Startup Apps

- Gnome software autostarts on boot for some reason, even though it is not required on every boot unless you want it to do updates in the background, this takes at least 100MB of RAM upto 900MB (as reported anecdotically). You can stop it from autostarting by:

```bash
sudo rm /etc/xdg/autostart/org.gnome.Software.desktop
```
