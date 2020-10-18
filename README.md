# ProxmoxVE
Describes how to setup Proxmox VE with ZFS and device passthrough (Intel CPU).

## Hardware Used
[Supermicro X11SCZ-F](https://www.supermicro.com/en/products/motherboard/X11SCZ-F) using C246 chipset.

[Intel I3-9300](https://ark.intel.com/content/www/us/en/ark/products/134886/intel-core-i3-9300-processor-8m-cache-up-to-4-30-ghz.html) with integrated UHD630 GPU.

[Kingston KVR24E17D8/16MA](https://www.kingston.com/datasheets/KVR24E17D8_16MA.pdf), 32GiB total.

Two SSD drives for ZFS on root mirror.

# BIOS changes
Enable VT-d, VT-x, etc.

Enable ASPM or set to Auto.

Enable UEFI boot.

Set primary GPU as PCIe to be able to passthrough integrated GPU.

# Enable IOMMU

Edit systemd-boot file `nano /etc/kernel/cmdline` by adding `intel_iommu=on iommu=pt` options.

Execute `pve-efiboot-tool refresh`

## Add modules
Add required modules `nano /etc/modules`:
```
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```

Execute `update-initramfs -u -k all`


# References
[Admin Guide PCI passthrough](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#qm_pci_passthrough)

[Pci passthrough](https://pve.proxmox.com/wiki/Pci_passthrough)
