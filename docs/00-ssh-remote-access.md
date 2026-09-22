## SSH Remote Access
I use my MacBook to connect to the Arch Linux host through SSH. At the first time, I mostly used the command below:
```bash
ssh huynh@192.168.4.93
```

However, the IP address is assigned by DHCP, so it may change over time. So that to make the connection easier, I configured an SSH host alias on my MacBook.
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

