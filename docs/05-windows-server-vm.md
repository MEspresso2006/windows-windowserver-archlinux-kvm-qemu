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


