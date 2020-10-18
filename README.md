## ProxmoxVE
Describes how to setup Proxmox VE with ZFS and device passthrough (Intel CPU).

### Hardware used
[Supermicro X11SCZ-F](https://www.supermicro.com/en/products/motherboard/X11SCZ-F) using C246 chipset.

[Intel I3-9300](https://ark.intel.com/content/www/us/en/ark/products/134886/intel-core-i3-9300-processor-8m-cache-up-to-4-30-ghz.html) with integrated UHD630 GPU.

[Kingston KVR24E17D8/16MA](https://www.kingston.com/datasheets/KVR24E17D8_16MA.pdf), 32GiB total.

Two SSD drives for ZFS on root mirror.

## BIOS changes
Enable VT-d, VT-x, etc.

Enable ASPM or set to Auto.

Enable UEFI boot.

Set primary GPU as PCIe to be able to passthrough integrated GPU.

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

## References
[Admin Guide PCI passthrough](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#qm_pci_passthrough)

[Pci passthrough](https://pve.proxmox.com/wiki/Pci_passthrough)

[Windows 10 guest best practices](Windows 10 guest best practices)
