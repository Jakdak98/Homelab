# Phase 1: Foundation — Ubuntu Server VM

##  Environment  ##

- Host: Windows + VirtualBox
- VM: Ubuntu Server, network mode: Bridged
- Network interface: `enp0s3`
- Docker pre-installed on the VM (`docker0` interface visible)

##  Static IP configuration  ##

Changed `/etc/netplan/00-installer-config.yaml` from DHCP to a static address.

**Parameters:**
- IP: `192.168.X.X/24` (static, assigned from home LAN range)
- Gateway: `192.168.1.1`
- DNS: `8.8.8.8`, `1.1.1.1`

**Steps:**
1. Backed up the config file: `00-installer-config.yaml.bak`
2. Edited the netplan file (`dhcp4: false`, added `addresses`, `routes`, `nameservers`)
3. Validated syntax: `sudo netplan generate`
4. Applied changes: `sudo netplan apply`
5. Verified: `ip a` — address shown without the `dynamic` flag, `valid_lft forever`

##  System clock fix  ##

After the network change, `apt update` returned a "Release file not valid yet" error —
the VM's clock was 7 days behind the actual date.

**Fix:**
- Time sync service on this system: `chrony` (not usual `systemd-timesyncd`)
- `sudo systemctl restart chrony` forced immediate synchronization successfully
- Verified with `timedatectl status` → `System clock synchronized: yes`. After that the issue was fixed

##  Connectivity test  ##

```bash
ping -c 4 google.com
# 0% packet loss — network and DNS working correctly
```

##  Next step  ##

Configure SSH — access the VM from the host (Windows) terminal instead of the VirtualBox console.




## SSH key-based authentication

Generated an Ed25519 SSH key pair on the host (with passphrase) and copied the public key
to the VM using `ssh-copy-id`. Verified that SSH now authenticates via the key (prompts for
the key's passphrase instead of the account password).

@PC-MyPersonalPC ToJA64 ~
$ ssh 'UserX@192.168.X.X'
Enter passphrase for key 'file's path':
Welcome to Ubuntu 26.04 LTS (GNU/Linux 7.0.0-27-generic x86_64)


Troubleshooting note: initial key generation attempt silently failed to save the private key
on the host — regenerating from scratch resolved it. 
Verified with `ls -la ~/.ssh/` and
`head -1 ~/.ssh/id_ed25519` after generating a key, if the jey is there. 


##  Next step  ##

Move to Phase 2: reverse proxy + TLS.  Coming soon