# Creating the Windows 11 VM

After Windows Server was working, I created a Windows 11 virtual machine in `virt-manager`.

For the initial setup, I used:

- Windows 11 installation ISO
- QCOW2 virtual disk
- SATA disk controller
- `e1000e` network adapter
- UEFI firmware
- libvirt `default` NAT network

Windows 11 also required TPM 2.0, so I added a virtual TPM device in `virt-manager`.

The TPM configuration I used was:

```text
Type: Emulated
Model: CRB
Version: 2.0
```

This allowed Windows 11 to meet the TPM requirement without using the physical TPM from the Arch Linux host.

After Windows 11 was installed successfully, I removed the Windows installation ISO from the virtual CD-ROM and mounted:

```text
virtio-win.iso
```

The VirtIO ISO was used to provide additional Windows guest drivers and tools for the KVM/QEMU environment.

The Windows 11 virtual machine was later renamed to:

```text
WS1
```

This machine will later be used as the normal workstation in my Active Directory Security Lab.


