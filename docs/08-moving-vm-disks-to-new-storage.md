# Moving VM Disks to the New Storage Pool

After creating the new storage pool, I needed to move the existing virtual machine disk files from the default libvirt storage location to the new storage location.

The original VM disks were stored in:
```text
/var/lib/libvirt/images/
```

Because the root partition had limited space, I moved the VM disks to:
```text
/home/libvirt-images/
```

The virtual machines that were moved were:

- DC1
- WS1
---

## Shutting Down the Virtual Machines

Before moving the disk files, I shut down the virtual machines to prevent data corruption.

I checked the VM status:

```bash
virsh -c qemu:///system list --all
```

Then I shut down the running machines:

```bash
virsh -c qemu:///system shutdown "WS1"

virsh -c qemu:///system shutdown "DC1"
```

---

## Copying the QCOW2 Disk Files

I copied the virtual disk files into the new storage directory:

```bash
sudo cp "/var/lib/libvirt/images/DC1.qcow2" "/home/libvirt-images/"

sudo cp "/var/lib/libvirt/images/WS1.qcow2" "/home/libvirt-images/"
```

After copying, I checked that the files existed:

```bash
ls -lh /home/libvirt-images
```

---

## Updating the VM Disk Location

After moving the files, I updated each virtual machine configuration so libvirt would use the new disk location.

I opened the VM configuration:

```bash
virsh -c qemu:///system edit "DC1"
```

I changed the disk source path from:

```xml
/var/lib/libvirt/images/DC1.qcow2
```

to:

```xml
/home/libvirt-images/DC1.qcow2
```

I repeated the same process for:

```bash
virsh -c qemu:///system edit "WS1"
```

---

## Verifying the Migration

After updating the configuration, I checked the disk location:

```bash
virsh -c qemu:///system domblklist "DC1"
```

The output showed:

```text
sda   /home/libvirt-images/DC1.qcow2
```

I also verified the second VM:

```bash
virsh -c qemu:///system domblklist "WS1"
```

The output showed:

```text
sda   /home/libvirt-images/WS1.qcow2
```

This confirmed that both virtual machines were now using the new storage location.

---
## Comparing the Original and New Disk Files

Before deleting the original VM disk files, I compared the original QCOW2 files with the copied files to make sure the migration was successful.

I used `qemu-img compare`:

```bash
qemu-img compare "/var/lib/libvirt/images/DC1.qcow2" "/home/libvirt-images/DC1.qcow2"
```

Then I repeated the same process for the Windows 11 workstation:
```bash
qemu-img compare "/var/lib/libvirt/images/WS1.qcow2" "/home/libvirt-images/WS1.qcow2"
```


## Removing the Old Disk Files

After confirming that both VMs started successfully from the new location, I removed the old disk files from the root partition to recover storage space.

```bash
sudo rm "/var/lib/libvirt/images/DC1.qcow2"

sudo rm "/var/lib/libvirt/images/WS1.qcow2"
```

After this migration, the Windows virtual machines were stored in:

```text
/home/libvirt-images/
```

This provided more available space on the Arch Linux host and allowed me to continue expanding the Active Directory lab.
