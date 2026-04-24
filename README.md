# Antix + dwm + Battery Optimization Setup (ThinkPad X1 Gen 10)

> Antix on my laptop X1 gen 10


## 1. Base System Installation

Install Antix minimal

### Network

vi /etc/wpa_supplicant/wpa_supplicant.conf
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="YOUR_WIFI_NAME"
    psk="YOUR_PASSWORD"
}
```

vi /etc/rc.local
```bash
wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf
dhclient wlan0
```

Then:

``` bash
sudo apt update
sudo apt install xorg xinit build-essential git
```

------------------------------------------------------------------------

## 2. dwm Setup

``` bash
sudo apt install dwm dmenu stterm
```

Create `~/.xinitrc`:

``` bash
exec dwm
```

Start:

``` bash
startx
```


## 3. 

```bash
apt install openssh-server rsync
```


------------------------------------------------------------------------

## 3. Battery Optimization

### Install TLP

``` bash
sudo apt install tlp tlp-rdw
sudo systemctl enable tlp
sudo systemctl start tlp
```

### Install power tools

``` bash
sudo apt install intel-microcode powertop
```

### Configure TLP

Edit `/etc/tlp.conf`:

    CPU_SCALING_GOVERNOR_ON_BAT=powersave
    CPU_SCALING_GOVERNOR_ON_AC=performance
    CPU_ENERGY_PERF_POLICY_ON_BAT=power
    CPU_ENERGY_PERF_POLICY_ON_AC=balance_performance
    SATA_LINKPWR_ON_BAT=min_power
    WIFI_PWR_ON_BAT=on

### Powertop auto-tune

``` bash
sudo powertop --auto-tune
```

### Disable unused services

``` bash
sudo systemctl disable bluetooth
```

### Thermal management

``` bash
sudo apt install thermald
sudo systemctl enable thermald
```

------------------------------------------------------------------------

## 4. Intel GPU Power Saving

Edit GRUB:

``` bash
sudo nano /etc/default/grub
```

Add to GRUB_CMDLINE_LINUX:

    i915.enable_fbc=1 i915.enable_psr=2

Update:

``` bash
sudo update-grub
```

------------------------------------------------------------------------

## 5. VMware Setup

Install dependencies:

``` bash
sudo apt install linux-headers-$(uname -r) build-essential
```

Note: - Use VMware mainly on AC power - Use snapshots for teaching
workflows

------------------------------------------------------------------------

## 6. dwm Status Bar (Battery)

Add to `.xinitrc`:

``` bash
while true; do
    xsetroot -name "$(date) | $(cat /sys/class/power_supply/BAT0/capacity)%"
    sleep 10
done &

exec dwm
```

------------------------------------------------------------------------

## 7. Laptop Behavior (Suspend)

Edit:

``` bash
sudo nano /etc/systemd/logind.conf
```

Set:

    HandleLidSwitch=suspend
    HandleLidSwitchDocked=ignore

------------------------------------------------------------------------

## Final Notes

-   Keep host system minimal
-   Do development inside VMs
-   Use snapshots heavily
-   Update system conservatively

This setup provides: - OpenBSD-like discipline - Linux hardware
compatibility - Efficient battery usage
