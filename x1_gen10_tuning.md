# ThinkPad X1 Gen 10 -- Linux Hardware Tuning Guide (Debian + dwm)

## Overview

This guide focuses on: - Fan and thermal control - Suspend/resume
reliability - Power efficiency tweaks - Stability for daily teaching use

Tested with modern Intel (12th Gen+) platforms.

------------------------------------------------------------------------

## 1. Firmware & BIOS Settings

Enter BIOS (F1 on boot):

Recommended: - **UEFI Only** (disable Legacy) - **Secure Boot**:
Disabled (optional, but avoids driver friction) - **Sleep State**: Linux
(S3) if available, otherwise Modern Standby - **Intel AMT / vPro**:
Disable if unused - **Thunderbolt Security**: User Authorization (or off
for simplicity)

Update firmware via: - fwupd (Linux) or Lenovo Vantage (Windows)

------------------------------------------------------------------------

## 2. Kernel Parameters (Critical)

Edit:

``` bash
sudo nano /etc/default/grub
```

Append to `GRUB_CMDLINE_LINUX_DEFAULT`:

    quiet splash i915.enable_fbc=1 i915.enable_psr=2 intel_pstate=active

Then:

``` bash
sudo update-grub
```

------------------------------------------------------------------------

## 3. Thermal & Fan Management

### Install thermald

``` bash
sudo apt install thermald
sudo systemctl enable thermald
sudo systemctl start thermald
```

### Optional: Manual fan control (advanced)

Install:

``` bash
sudo apt install thinkfan lm-sensors
```

Detect sensors:

``` bash
sudo sensors-detect
```

Configure:

``` bash
sudo nano /etc/thinkfan.conf
```

Example minimal config:

    sensor /proc/acpi/ibm/thermal
    fan /proc/acpi/ibm/fan

    (0, 0, 55)
    (1, 50, 65)
    (2, 60, 75)
    (3, 70, 85)
    (7, 80, 100)

Enable:

``` bash
sudo systemctl enable thinkfan
```

------------------------------------------------------------------------

## 4. Suspend / Resume Fixes

### Enable proper suspend handling

``` bash
sudo nano /etc/systemd/logind.conf
```

Set:

    HandleLidSwitch=suspend
    HandleLidSwitchDocked=ignore

Restart:

``` bash
sudo systemctl restart systemd-logind
```

------------------------------------------------------------------------

### Fix common resume issues

Install firmware:

``` bash
sudo apt install firmware-iwlwifi firmware-misc-nonfree
```

For Intel graphics issues, try:

    i915.enable_dc=2

(add to GRUB if needed)

------------------------------------------------------------------------

## 5. Wi-Fi Power Stability

Edit TLP config:

``` bash
sudo nano /etc/tlp.conf
```

Ensure:

    WIFI_PWR_ON_BAT=on
    WIFI_PWR_ON_AC=off

Restart TLP:

``` bash
sudo systemctl restart tlp
```

------------------------------------------------------------------------

## 6. Audio Stability (PipeWire)

Install modern audio stack:

``` bash
sudo apt install pipewire wireplumber
```

Enable:

``` bash
systemctl --user enable pipewire
```

------------------------------------------------------------------------

## 7. Battery Health (ThinkPad-specific)

Install:

``` bash
sudo apt install tlp
```

Set charge thresholds:

``` bash
sudo nano /etc/tlp.conf
```

Example:

    START_CHARGE_THRESH_BAT0=40
    STOP_CHARGE_THRESH_BAT0=80

This extends battery lifespan significantly.

------------------------------------------------------------------------

## 8. Debugging Tools

### Check power usage:

``` bash
sudo powertop
```

### Check thermals:

``` bash
sensors
```

### Check suspend issues:

``` bash
journalctl -b -1
```

------------------------------------------------------------------------

## 9. Practical Usage Rules

-   Use VMware only on AC power
-   Avoid heavy workloads on battery
-   Keep system updates controlled (not constant)
-   Reboot occasionally after kernel updates

------------------------------------------------------------------------

## Final Result

With these tweaks:

-   Stable suspend/resume
-   Lower fan noise
-   Better battery life
-   Reliable teaching workstation behavior

This setup balances: - Linux flexibility - ThinkPad hardware
optimization - OpenBSD-like system discipline
