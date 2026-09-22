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


