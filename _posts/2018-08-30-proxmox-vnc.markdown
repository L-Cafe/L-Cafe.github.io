---
title:      "Proxmox tip: \"Failed to start VNC server: Failed to bind socket: Cannot assign requested address\""
date:       2018-08-30 14:44:32 +0100
tags:       Proxmox tip
comment:    header sizes are 2000x750
header:
  image:	/assets/images/2018-08-30-proxmox-vnc/header.jpg
  teaser:	/assets/images/2018-08-30-proxmox-vnc/header.jpg
  caption:	"Photo credit: [**Unsplash**](https://unsplash.com)"
---

Note: This issue appears on Proxmox 5.2-x, always check the date of the post when troubleshooting your systems.

I stumbled across this particularly interesting error when I was moving KVM backups from a Proxmox host to another host. For some reason, I wasn't able to find any information on the Internet regarding this issue.

Let's get to it!

## Steps to reproduce it:

1. Back up a KVM machine from a Proxmox host. If you back up to the default local storage, there will be a tarball file in `/var/lib/vz/dump/` (GZIP or LZO, depending on what you selected when backing up)
2. Copy that file to a different Proxmox host. You can use rsync, SFTP, FTP, it doesn't really matter.
3. Place that file in `/var/lib/vz/dump` in the second host (or wherever you want that's set up in Proxmox to use as VZDump backup file directory).
4. Try to restore the tarball through the Proxmox Web GUI by choosing the file in the appropriate storage and clicking "Restore".

The machine is now copied to the second server, but if you try to start it, an error will appear. It will look like this:

```
kvm: -vnc 192.168.33.5:85: Failed to start VNC server: Failed to bind socket: Cannot assign requested address
TASK ERROR: start failed: command '/usr/bin/kvm -id 115 -name xyz -chardev 'socket,id=qmp,path=/var/run/qemu-server/115.qmp,server,nowait' -mon 'chardev=qmp,mode=control' -pidfile /var/run/qemu-server/115.pid -daemonize -smbios 'type=1,uuid=abcd' -smp '1,sockets=1,cores=1,maxcpus=1' -nodefaults -boot 'menu=on,strict=on,reboot-timeout=1000,splash=/usr/share/qemu-server/bootsplash.jpg' -vga std -vnc unix:/var/run/qemu-server/115.vnc,x509,password -no-hpet -cpu 'kvm64,+lahf_lm,+sep,+kvm_pv_unhalt,+kvm_pv_eoi,hv_spinlocks=0x1fff,hv_vapic,hv_time,hv_reset,hv_vpindex,hv_runtime,hv_relaxed,enforce' -m 5120 -vnc 192.168.33.5:85 -device 'pci-bridge,id=pci.1,chassis_nr=1,bus=pci.0,addr=0x1e' -device 'pci-bridge,id=pci.2,chassis_nr=2,bus=pci.0,addr=0x1f' -device 'piix3-usb-uhci,id=uhci,bus=pci.0,addr=0x1.0x2' -device 'usb-tablet,id=tablet,bus=uhci.0,port=1' -device 'virtio-balloon-pci,id=balloon0,bus=pci.0,addr=0x3' -drive 'file=/var/lib/vz/images/115/vm-115-disk-1.raw,if=none,id=drive-ide0,cache=writeback,format=raw,aio=threads,detect-zeroes=on' -device 'ide-hd,bus=ide.0,unit=0,drive=drive-ide0,id=ide0,bootindex=100' -drive 'if=none,id=drive-ide2,media=cdrom,aio=threads' -device 'ide-cd,bus=ide.1,unit=0,drive=drive-ide2,id=ide2,bootindex=200' -netdev 'type=tap,id=net0,ifname=tap115i0,script=/var/lib/qemu-server/pve-bridge,downscript=/var/lib/qemu-server/pve-bridgedown' -device 'e1000,mac=xx:xx:xx:xx:xx:xx,netdev=net0,bus=pci.0,addr=0x12,id=net0,bootindex=300' -rtc 'driftfix=slew,base=localtime' -global 'kvm-pit.lost_tick_policy=discard'' failed: exit code 1
```

(Note: everything was copied from the Proxmox Web GUI, but the name, MAC and BIOS UUID have been altered)

At first glance, it could look like a ports issue. But we're not opening ports, are we? Why is this machine throwing a VNC error when I didn't even try to open up VNC?

## The solution

Well, Proxmox opens a VNC virtual screen when booting up a KVM machine, and the problem is that the backup carried the IP from the first Proxmox server, and now we're asking the second Proxmox server to open a port in an IP that doesn't belong to it.

Specifically, the first line is a dead giveaway: "kvm: -vnc **192.168.33.5:85**: Failed to start VNC server: Failed to bind socket: Cannot assign requested address"

What we have to do is go into the virtual machine Proxmox config file, which should be located in `/etc/pve/qemu-server/<VM ID>.conf`, and in this case, because the machine ID is 115, the config file would be in `/etc/pve/qemu-server/115.conf`. The ID is manually selected when restoring the backup.

The original contents of the file are as follows.

```
args: -vnc 192.168.33.5:85
balloon: 1024
bootdisk: ide0
cores: 1
ide0: local:115/vm-115-disk-1.raw,cache=writeback,size=50G
ide2: none,media=cdrom
memory: 5120
name: xyz
net0: e1000=xx:xx:xx:xx:xx:xx,bridge=vmbr1
numa: 0
onboot: 1
ostype: win8
scsihw: virtio-scsi-pci
smbios1: uuid=abcd
sockets: 1
```

Our second machine has the IP `192.168.33.10`, but this depends on your Proxmox and network configuration. The IP address of any Linux machine can be consulted by running `ip addr show`.

We only need to change the first line, to have the correct IP. Once this is done, the machine will boot successfully, but if you haven't already, it might be desirable to change the MAC address of the network adapter as well, depending on your network configuration, to avoid collisions, and maybe the network bridge in case the NAT in your second Proxmox server is set up differently.

```
args: -vnc 192.168.33.10:85
[...]
net0: e1000=yy:yy:yy:yy:yy:yy,bridge=vmbr1
[...]
```

That's it!
