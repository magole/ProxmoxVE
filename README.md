## ProxmoxVE
Describes how to setup Proxmox VE 6.2 with ZFS and device passthrough (Intel CPU).

### Hardware used
[Supermicro X11SCZ-F](https://www.supermicro.com/en/products/motherboard/X11SCZ-F) using C246 chipset.

[Intel I3-9300](https://ark.intel.com/content/www/us/en/ark/products/134886/intel-core-i3-9300-processor-8m-cache-up-to-4-30-ghz.html) with integrated UHD630 GPU.

[Kingston KVR24E17D8/16MA](https://www.kingston.com/datasheets/KVR24E17D8_16MA.pdf), 32GiB total.

Two SSD drives for ZFS on root mirror.

## BIOS changes
Enable VT-d, VT-x, etc.

Enable ASPM or set to Auto.

Enable UEFI boot.

Set primary display to PCI and enable Internal Graphics option to be able to passthrough integrated GPU.

## Enable IOMMU

Edit systemd-boot file `nano /etc/kernel/cmdline` by adding `intel_iommu=on iommu=pt` options.

Apply changes by executing `pve-efiboot-tool refresh`

### Add kernel modules
Add required modules `nano /etc/modules`:
```
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```

Apply changes by executing  `update-initramfs -u -k all`

## Verify configuration
Reboot the host

Execute `dmesg | grep -e DMAR -e IOMMU` and check the output for:

```
[    0.103246] DMAR: IOMMU enabled
[    0.172321] DMAR-IR: Queued invalidation will be enabled to support x2apic and Intr-remapping.
[    0.175358] DMAR-IR: Enabled IRQ remapping in x2apic mode
[    0.913016] DMAR: Intel(R) Virtualization Technology for Directed I/O
```

## Device blacklisting
Prevent passthrough devices from host loading by editing `nano /etc/modprobe.d/blacklist.conf`. For example:

```
blacklist snd_hda_intel
blacklist snd_hda_codec_hdmi
blacklist i915
```

## GPU passthrough
```
All functions [Y]
ROM-Bar [Y]
PCI-Express [Y]
```

## NIC passthrough
Tested on Intel I350-T4. Each nic must be in its own iommu group. When passing through individual nics, do not enable `All Functions` or `ROM-Bar`.

## Enable updates for Proxmox free version
```
rm /etc/apt/sources.list.d/pve-enterprise.list
echo "deb http://download.proxmox.com/debian/pve buster pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
apt update
apt full-upgrade
```


## References
[Create USB installer](https://pve.proxmox.com/wiki/Prepare_Installation_Media)

[Admin Guide PCI passthrough](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#qm_pci_passthrough)

[Pci passthrough](https://pve.proxmox.com/wiki/Pci_passthrough)

[Qemu/KVM Virtual Machines Wiki](https://pve.proxmox.com/wiki/Qemu/KVM_Virtual_Machines)

[Qemu/KVM Virtual Machines Docs](https://pve.proxmox.com/pve-docs/chapter-qm.html)

[Windows 10 guest best practices](https://pve.proxmox.com/wiki/Windows_10_guest_best_practices)

[PCI passthrough via OVMF](https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF)

[Proxmox ZFS on Linux](https://pve.proxmox.com/wiki/ZFS_on_Linux)
