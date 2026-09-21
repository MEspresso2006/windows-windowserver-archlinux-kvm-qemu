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

However, the IP address can change because it is assigned by DHCP. When the lease is end the IP address will be changed, so that to make the connection easier, I configured an SSH host alias on my MacBook.
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
