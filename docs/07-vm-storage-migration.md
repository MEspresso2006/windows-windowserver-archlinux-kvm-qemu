## Moving the VM Storage to `/home`

While installing Windows, I noticed that the root partition on my Arch Linux host became almost full.

I checked the disk usage with:
```bash
df -h
```

At that time, the root partition was only around 32 GB and reached almost 100% usage, while the `/home` partition still had much more free space.

I also checked how much space the virtual machines were using:
```bash
sudo du -sh /var/lib/libvirt/images
```

The VM disk files were stored in the default libvirt directory:
```text
/var/lib/libvirt/images/
```

Because the root partition was too small for multiple Windows virtual machines, I decided to move the VM storage to the larger `/home` partition.

I created a new directory for the virtual machine disks:
```bash
sudo mkdir -p /home/libvirt-images
```

Then I created a new libvirt storage pool:
```bash
sudo virsh pool-define-as vm-storage dir --target /home/libvirt-images
sudo virsh pool-start vm-storage
sudo virsh pool-autostart vm-storage
```

I checked the storage pools with:
```bash
sudo virsh pool-list --all
```

At this point, I had a new storage pool named:
```text
vm-storage
```

which used:
```text
/home/libvirt-images
```

This allowed the Windows VM disks to use the larger `/home` partition instead of filling up the Arch Linux root partition.
