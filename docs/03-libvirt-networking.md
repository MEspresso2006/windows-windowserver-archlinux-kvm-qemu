## Configuring the libvirt Network

After libvirt was working, I checked the default virtual network:

```bash
sudo virsh net-list --all
```

At first, the `default` network was inactive, so I started it with:

```bash
sudo virsh net-start default
```

Then I enabled it to start automatically:
```bash
sudo virsh net-autostart default
```

I checked the network again:
```bash
sudo virsh net-list --all
```

The `default` network was now active and set to autostart.

<img width="849" height="171" alt="image" src="https://github.com/user-attachments/assets/6a5b483d-ffd4-4d63-a758-a4c2778c96ee" />

I also checked the virtual bridge created by libvirt:
```bash
ip addr show virbr0
```

The bridge had this address:
```text
192.168.122.1/24
```

Then to see the full network configuration, I used:
```bash
sudo virsh net-dumpxml default
```

The default network uses:
```text
Network: 192.168.122.0/24
Gateway: 192.168.122.1
DHCP range: 192.168.122.2 - 192.168.122.254
Mode: NAT
Bridge: virbr0
```
