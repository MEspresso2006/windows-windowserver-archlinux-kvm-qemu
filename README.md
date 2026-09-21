# Windows and Windows Server on Arch Linux with KVM/QEMU
## Overview

I will document all the steps and my thoughts about setting up a Windows virtualization lab on Arch Linux using KVM/QEMU, libvirt, and virt-manager. <br>

The main goal of this project is to create an environment where I can run Windows Server and Windows 11 virtual machines for my future Active Directory Security Lab.  <br>

For this project, I chose KVM/QEMU mainly because it's well integrated with Linux and gives me more control over the virtualization environment. I will work with virtual networking, storage pools, UEFI, snapshots, cloning, and remote management.

## Lab Architecture

My main computer is a MacBook, which I use to connect to the Arch Linux host through SSH.

The Arch Linux laptop works as the virtualization host. It runs KVM/QEMU and libvirt, while the Windows virtual machines run inside this environment.

```text
MacBook
   |
   | SSH
   v
Arch Linux Host
KVM/QEMU + libvirt
   |
   +-- Windows Server
   |
   +-- Windows 11 Workstation
   |
   +-- Windows 11 Management Client
```

## SSH Remote Access
I use my MacBook to connect to the Arch Linux host through SSH. At the first time, I mostly use the command below:
```bash
ssh huynh@192.168.4.93
```

However, the IP address is assigned by DHCP, so it may change over time. So that to make the connection easier, I configured an SSH host alias on my MacBook.
I edited the SSH configuration file:

```bash
nano ~/.ssh/config
```
Then I added:
```text
Host mesolab
    HostName mesolab.local
    User huynh
```
After saving the file, I can connect to the Arch Linux host with:
```bash
ssh mesolab
```
This is easier than typing `ip a` on the Linux to get the IP address every time, and `mesolab.local` allows the MacBook to find the Arch Linux host by hostname on the local network.


## Installing KVM/QEMU and libvirt

> **Note:** One thing I learned from this setup is that it is better to download the Windows 11 and Windows Server ISO files before starting. The ISO files are quite large and may take some time to download, so doing this first can save time later when creating the virtual machines.

After setting up SSH access, I started preparing the Arch Linux host for virtualization.

I installed the main packages that I needed for this lab with the following command:

```bash
sudo pacman -S qemu-full qemu-img libvirt virt-install virt-manager virt-viewer \
edk2-ovmf dnsmasq swtpm guestfs-tools libosinfo
```
These packages are used for different parts of the virtualization environment:
- `qemu-full` - provides the main QEMU virtualization tools.
- `qemu-img` - used to create and manage virtual disk images such as QCOW2 files.
- `libvirt` - manages virtual machines, virtual networks, and storage.
- `virt-install` - allows virtual machines to be created from the command line.
- `virt-manager` - provides a graphical interface for managing virtual machines.
- `virt-viewer` - used to open and interact with VM consoles.
- `edk2-ovmf` - provides UEFI firmware for virtual machines.
- `dnsmasq` - provides DHCP and DNS services for the libvirt virtual network.
- `swtpm` - provides a software TPM, which is needed for Windows 11.
- `guestfs-tools` - provides tools for inspecting and working with virtual machine disks.
- `libosinfo` - provides operating system information that helps libvirt identify guest operating systems.

I decided not to install `tuned` at this stage because performance tuning was not necessary for the current lab setup.



## Enabling and Validating libvirt
After installing the virtualization packages, I enabled the libvirt socket so that the virtualization service can start when it is needed.

```bash
sudo systemctl enable libvirtd.socket
sudo systemctl start libvirtd.socket
```

I chose socket activation instead of starting `libvirtd.service` directly.

After that, I checked whether the host was ready for virtualization by running:

```bash
sudo virt-host-validate qemu
```

Most of the checks passed, including:

- Hardware virtualization support
- `/dev/kvm`
- `/dev/vhost-net`
- `/dev/net/tun`
- Required cgroup controllers
- IOMMU hardware support

There were also some warnings about IOMMU not being enabled in the kernel and some advanced CPU security features not being available. These warnings were not important for my current Windows and Active Directory lab, so I did not change anything.

Finally, I tested the libvirt connection with:

```bash
virsh -c qemu:///system list --all
```
At this point, the command worked successfully and showed that libvirt was ready to manage virtual machines.




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





## Preparing the ISO Files

Before creating the virtual machines, I already downloaded the installation ISO files for Windows Server and Windows 11.

I stored the ISO files in a separate directory name `ISOs` so they were easier to manage when creating the virtual machines in `virt-manager`.

The main installation images used for this lab were:

- Windows Server 2025 Evaluation
- Windows 11

- VirtIO Drivers for Windows Guests

For the VirtIO, I went to the [Fedora People](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/) repository and download `virtio-win.iso`.
I saved it into my `ISOs` directory so I can easily manage it. 






## Creating the Windows Server VM

After preparing the ISO files, I created my first virtual machine in `virt-manager` for Windows Server.

For this lab, I used **Windows Server 2025 Standard Evaluation with Desktop Experience**. I chose the Desktop Experience version because I wanted to use the graphical interface while learning Windows Server and Active Directory.

For the initial setup, I used:

- Windows Server 2025 Evaluation
- QCOW2 virtual disk
- SATA disk controller
- `e1000e` network adapter
- UEFI firmware
- libvirt `default` NAT network
- Windows Server installation ISO

I used SATA and `e1000e` at the beginning because Windows can recognize them without requiring extra VirtIO drivers during the installation.

After Windows Server was installed successfully, I removed the Windows installation ISO from the virtual CD-ROM and mounted `virtio-win.iso` instead.

In `virt-manager`, I used the CD-ROM device to attach:

```text
virtio-win.iso
```

This ISO contains VirtIO drivers and guest tools that can be installed inside Windows to improve the integration between the Windows guest and the KVM/QEMU host.

The virtual machine was later renamed to:
```text
DC1
```

At this stage, Windows Server was installed and working, but Active Directory had not been configured yet.


