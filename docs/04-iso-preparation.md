# Preparing the ISO Files

Before creating the virtual machines, I already downloaded the installation ISO files for Windows Server and Windows 11.

I stored the ISO files in a separate directory name `ISOs` so they were easier to manage when creating the virtual machines in `virt-manager`.

The main installation images used for this lab were:

- Windows Server 2025 Evaluation
- Windows 11

- VirtIO Drivers for Windows Guests

For the VirtIO, I went to the [Fedora People](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/latest-virtio/) repository and download `virtio-win.iso`.
I saved it into my `ISOs` directory so I can easily manage it. 

> **Note: Installing Guest Tools**
>
> One thing I learned when working with virtual machines is that installing guest tools can make the experience much smoother.
>
> If you are using VMware on macOS, Windows, or Linux, it is recommended to install VMware Tools inside the guest operating system after the installation is completed.
>
> These tools improve the communication between the host machine and the virtual machine. They allow features such as:
>
> - Better screen resolution and full-screen support
> - Better mouse integration
> - Copying and pasting text between the host and the virtual machine
> - Easier file sharing
>
> For KVM/QEMU, the equivalent tools are VirtIO drivers and QEMU guest tools, which provide similar improvements for Windows guests.


