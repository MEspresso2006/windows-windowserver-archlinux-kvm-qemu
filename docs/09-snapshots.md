# Creating VM Snapshots
After completed all the Windows installations and moving the VM storage to `/home/libvirt-images`, 
I created a snapshot of the virtual machines before starting the Active Directory Lab.

The purpose of this snapshot is to store the fresh start of each virtual machine. I highly recommend doing this step because I can revert back to my start point
when something goes wrong. 

> **Note:** A snapshot is mainly used as a restore point. In some desktop virtualization applications, such as VMware Fusion or VirtualBox,
> the software provides a more convenient way to create a clone from a selected snapshot state.
>
> However, in KVM/QEMU with libvirt, a snapshot cannot be directly cloned into a new virtual machine. If I want to create a new VM from a snapshot state,
> I need to revert the original VM back to that snapshot first and then clone the VM from that state.
>

I used libvirt snapshots to save the current state of the virtual machines.

The command format is:
```bash
virsh snapshot-create-as <VM_NAME> <SNAPSHOT_NAME>
```

For example:
```bash
virsh snapshot-create-as "DC1" "Base WinServer 2025"
```

and:

```bash
virsh snapshot-create-as "WS1" "Base Windows 11"
```

The snapshot name describes the purpose of the restore point.

---

## Checking Available Snapshots

After creating the snapshots, I verified them using:

```bash
virsh snapshot-list "DC1"
```

Example output:

```text
I will put image here later 
```

I repeated the same check for the Windows workstation:

```bash
virsh snapshot-list "WS1"
```

---

## Restoring a Snapshot

If a future configuration change causes problems, I can restore the VM to the previous working state.

The restore command is:

```bash
virsh snapshot-revert "DC1" --snapshotname "Base WinServer 2025"
```

The same process can be used for other virtual machines.

---

## Why Snapshots Are Important for This Lab

Snapshots make it safer to experiment with the Active Directory environment.

Instead of reinstalling Windows after a mistake, I can return the VM to the clean baseline and continue testing.

This is especially useful when learning:

- Active Directory configuration
- Group Policy changes
- Security testing
- System administration tasks
