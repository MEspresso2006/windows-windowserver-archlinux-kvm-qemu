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
