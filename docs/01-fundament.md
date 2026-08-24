# Phase 1: Foundation — Ubuntu Server VM

## Environment

- Host: Windows + VirtualBox
- VM: Ubuntu Server, network mode: Bridged
- Network interface: `enp0s3`
- Docker pre-installed on the VM (`docker0` interface visible)

## Static IP configuration

Changed `/etc/netplan/00-installer-config.yaml` from DHCP to a static address.

**Parameters:**
- IP: `192.168.1.X/24` (static, assigned from home LAN range)
- Gateway: `192.168.1.1`
- DNS: `8.8.8.8`, `1.1.1.1`

**Steps:**
1. Backed up the config file: `00-installer-config.yaml.bak`
2. Edited the netplan file (`dhcp4: false`, added `addresses`, `routes`, `nameservers`)
3. Validated syntax: `sudo netplan generate`
4. Applied changes: `sudo netplan apply`
5. Verified: `ip a` — address shown without the `dynamic` flag, `valid_lft forever`

## System clock fix

After the network change, `apt update` returned a "Release file not valid yet" error —
the VM's clock was 7 days behind the actual date.

**Fix:**
- Time sync service on this system: `chrony` (not `systemd-timesyncd`)
- `sudo systemctl restart chrony` forced immediate synchronization
- Verified with `timedatectl status` → `System clock synchronized: yes`

## Connectivity test

```bash
ping -c 4 google.com
# 0% packet loss — network and DNS working correctly
```

## Next step

Configure SSH — access the VM from the host (Windows) terminal instead of the VirtualBox console.