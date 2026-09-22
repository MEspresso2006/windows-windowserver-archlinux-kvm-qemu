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

## Documentation

### Remote Access

- [SSH Remote Access](docs/00-ssh-remote-access.md)

### Virtualization Setup

- [Installing KVM/QEMU and libvirt](docs/01-kvm-qemu-installation.md)

- [Enabling and Validating libvirt](docs/02-libvirt-setup.md)

- [Configuring the libvirt Network](docs/03-libvirt-networking.md)

### Windows Virtual Machines

- [Preparing the ISO Files](docs/04-iso-preparation.md)

- [Creating the Windows Server VM](docs/05-windows-server-vm.md)

- [Creating the Windows 11 VM](docs/06-windows-11-vm.md)

### Storage Management

- [Moving the VM Storage to `/home`](docs/07-vm-storage-migration.md)
