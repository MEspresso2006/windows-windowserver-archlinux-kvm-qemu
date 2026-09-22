# Configuring the libvirt Network

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


### Why I Used NAT Instead of a Network Bridge

My Arch Linux host connects to the home network through Wi-Fi. At first, I researched creating a separate bridge interface so that the virtual machines could connect directly to the physical network.
However, I decided not to use this method because bridging a Wi-Fi interface can be more complicated and it was not necessary for this lab.

Instead, I kept the libvirt default NAT network.
This allows the virtual machines to communicate with each other and access the internet while keeping the lab separated from the physical home network.

I also checked the host routing table with:
```bash
ip route
```

The Arch Linux host had two different networks:
```text
Physical network: 192.168.4.0/22
Virtual lab network: 192.168.122.0/24
```
Because these networks do not overlap, there was no subnet conflict.
