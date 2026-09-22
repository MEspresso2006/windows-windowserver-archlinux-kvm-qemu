# Enabling and Validating libvirt
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



