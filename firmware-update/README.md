https://docs.nvidia.com/dgx/dgx-rhel8-install-guide/installing-bf3-fw-steps.html

# Firmware update utilities

https://network.nvidia.com/support/firmware/mlxup-mft/

### mlxup

* mlxup - a firmware update and query utility. The utility enables scanning the server machine for available NVIDIA adapters
  and indicates whether firmware update is required for each adapter. To make it easier for our customers the mlxup can also
  automatically update the latest firmware either from the web, or use binaries provided locally.
  For other functionalities such as card configuration and binary creation, please use MFT.

* [mlxup 4.30.0 for Linux x64](https://www.mellanox.com/downloads/firmware/mlxup/4.30.0/SFX/linux_x64/mlxup)
* [mlxup 4.30.0 for Linux Arm](https://www.mellanox.com/downloads/firmware/mlxup/4.30.0/SFX/linux_arm64/mlxup)
* [mlxup user manual](https://docs.mellanox.com/display/MLXUPFWUTILITY)

Example of using `mlxup --query` with a ConnectX-6 card on RHEL 9.4:

```console
Querying Mellanox devices firmware ...

Device #1:
----------

  Device Type:      ConnectX6LX
  Part Number:      MCX631102AS-ADA_Ax
  Description:      ConnectX-6 Lx EN adapter card; 25GbE; Dual-port SFP28; PCIe 4.0 x8; Secure Boot; No Crypto;
  PSID:             MT_0000000575
  PCI Device Name:  0002:01:00.0
  Base GUID:        e8ebd3030070cf12
  Base MAC:         e8ebd370cf12
  Versions:         Current        Available
     FW             26.33.1048     26.43.1014
     PXE            3.6.0502       3.7.0500
     UEFI           14.26.0017     14.36.0016

  Status:           Update required
```

or `mlxup --query --query-format XML`:

```xml
<Devices>
    <Device pciName="0002:01:00.0" type="ConnectX6LX" psid="MT_0000000575" partNumber="MCX631102AS-ADA_Ax">
      <Versions>
        <FW current="26.33.1048" available="26.43.1014"/>
        <PXE current="3.6.0502" available="3.7.0500"/>
        <UEFI current="14.26.0017" available="14.36.0016"/>
      </Versions>
      <MACs Base_Mac="e8ebd370cf12" />
      <GUIDs Base_Guid="e8ebd3030070cf12" />
      <Status>Update required</Status>
      <Description>ConnectX-6 Lx EN adapter card; 25GbE; Dual-port SFP28; PCIe 4.0 x8; Secure Boot; No Crypto;</Description>
    </Device>
</Devices>
```

The output contains the PSID of a card, which is required for downloading the right firmware.

The tool can use a local file or a directory, or query for updates online:

```console
# mlxup --online
Querying Mellanox devices firmware ...

Device #1:
----------

  Device Type:      ConnectX6LX
  Part Number:      MCX631102AS-ADA_Ax
  Description:      ConnectX-6 Lx EN adapter card; 25GbE; Dual-port SFP28; PCIe 4.0 x8; Secure Boot; No Crypto;
  PSID:             MT_0000000575
  PCI Device Name:  /dev/mst/mt4127_pciconf0
  Base GUID:        e8ebd3030070cf12
  Base MAC:         e8ebd370cf12
  Versions:         Current        Available
     FW             26.33.1048     26.43.1014
     PXE            3.6.0502       3.7.0500
     UEFI           14.26.0017     14.36.0016

  Status:           Update required

Release notes for the available Firmware:
-----------------------------------------

  For more details, please refer to the following FW release notes:
    1- ConnectX3 (2.42.5000):    http://www.mellanox.com/pdf/firmware/ConnectX3-FW-2_42_5000-release_notes.pdf
    2- ConnectX3Pro (2.42.5000): http://www.mellanox.com/pdf/firmware/ConnectX3Pro-FW-2_42_5000-release_notes.pdf
    3- Connect-IB (10.16.1200):  http://www.mellanox.com/pdf/firmware/ConnectIB-FW-10_16_1200-release_notes.pdf
    4- ConnectX4 (12.28.2006):   http://docs.mellanox.com/display/ConnectX4Firmwarev12282006
    5- ConnectX4Lx (14.32.1010): http://docs.mellanox.com/display/ConnectX4LxFirmwarev14321010
    6- ConnectX5 (16.35.4030):   http://docs.mellanox.com/display/ConnectX5Firmwarev16354030
    7- ConnectX6 (20.43.1014):   http://docs.mellanox.com/display/ConnectX6Firmwarev20431014
    8- ConnectX6Dx (22.43.1014):   http://docs.mellanox.com/display/ConnectX6DxFirmwarev22431014
    9- ConnectX6Lx (26.43.1014):   http://docs.mellanox.com/display/ConnectX6LxFirmwarev26431014
    10- BlueField2 (24.43.1014):   http://docs.mellanox.com/display/BlueField2Firmwarev24431014
    11- ConnectX7 (28.43.1014):   http://docs.mellanox.com/display/ConnectX7Firmwarev28431014
    12- BlueField3 (32.43.1014):   http://docs.mellanox.com/display/BlueField3Firmwarev32431014

---------
Found 1 device(s) requiring firmware update...

Perform FW update? [y/N]: N
```

### MFT

[MFT](https://network.nvidia.com/products/adapter-software/firmware-tools/) - MFT package is a set of firmware management
tools used to generate a standard or customized NVIDIA firmware image, query for firmware information, and burn a firmware image.

The actual download link for MFT is https://content.mellanox.com/MFT/mft-4.30.1-8-arm64-rpm.tgz (instead of https://www.mellanox.com/downloads/MFT/mft-4.30.1-8-arm64-rpm.tgz),
or enable redirects (`curl -L`).

After unpacking:

```console
├── install.sh
├── LICENSE.txt
├── old-mft-uninstall.sh
├── RPMS
│   ├── mft-4.30.1-8.arm64.rpm
│   ├── mft-autocomplete-4.30.1-8.arm64.rpm
│   ├── mft-oem-4.30.1-8.arm64.rpm
│   └── mft-pcap-4.30.1-8.arm64.rpm
├── SDEBS
│   └── kernel-mft-dkms_4.30.1-8_all.deb
├── SRPMS
│   └── kernel-mft-4.30.1-8.src.rpm
└── uninstall.sh
```

Trying to run `install.sh` gives this message:

```
# ./install.sh
-E- There are missing packages that are required for installation of MFT.
-I- You can install missing packages using: yum install gcc rpm-build kernel-devel-5.14.0-427.13.1.el9_4.aarch64
```

When trying to install from the RPM and then `mst start` (as suggested in [Identifying Adapter Cards](https://network.nvidia.com/support/firmware/identification/)):

```console
# rpm -i RPMS/mft-4.30.1-8.arm64.rpm
# mst start
Starting MST (Mellanox Software Tools) driver set
Loading MST PCI modulemodprobe: FATAL: Module mst_pci not found in directory /lib/modules/5.14.0-427.13.1.el9_4.aarch64
 - Failure: 1
Loading MST PCI configuration modulemodprobe: FATAL: Module mst_pciconf not found in directory /lib/modules/5.14.0-427.13.1.el9_4.aarch64
 - Failure: 1
Create devices

mst_pci driver not found
-W- Missing "lsusb" command, skipping MTUSB devices detection
Unloading MST PCI module (unused)modprobe: FATAL: Module mst_pci not found.
 - Failure: 1
Unloading MST PCI configuration module (unused)modprobe: FATAL: Module mst_pciconf not found.
 - Failure: 1
```

After installing the requested packages

```console
# dnf install -y -q gcc rpm-build kernel-devel-$(uname -r)
...

# ./install.sh
-I- Removing all installed mft packages: mft
-I- Building the MFT kernel binary RPM...
-I- Installing the MFT RPMs...
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:kernel-mft-4.30.1-5.14.0_427.13.1################################# [100%]
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:mft-4.30.1-8                     ################################# [100%]
Verifying...                          ################################# [100%]
Preparing...                          ################################# [100%]
Updating / installing...
   1:mft-autocomplete-4.30.1-8        ################################# [100%]
-I- In order to start mst, please run "mst start".

# mst start
Starting MST (Mellanox Software Tools) driver set
Loading MST PCI module - Success
Loading MST PCI configuration module - Success
Create devices
-W- Missing "lsusb" command, skipping MTUSB devices detection
Unloading MST PCI module (unused) - Success
[root@hpe-rl300gen11-02 mft-4.30.1-8-arm64-rpm]# mst status
MST modules:
------------
    MST PCI module is not loaded
    MST PCI configuration module loaded

MST devices:
------------
/dev/mst/mt4127_pciconf0         - PCI configuration cycles access.
                                   domain:bus:dev.fn=0002:01:00.0 addr.reg=88 data.reg=92 cr_bar.gw_offset=-1
                                   Chip revision is: 00
```

Note: the documentation seems broken and outdated, the command I had to run doesn't have a _Board ID_:

```console
# flint -d /dev/mst/mt4127_pciconf0 query
Image type:            FS4
FW Version:            26.33.1048
FW Release Date:       29.4.2022
Product Version:       26.33.1048
Rom Info:              type=UEFI version=14.26.17 cpu=AMD64,AARCH64
                       type=PXE version=3.6.502 cpu=AMD64
Description:           UID                GuidsNumber
Base GUID:             e8ebd3030070cf12        4
Base MAC:              e8ebd370cf12            4
Image VSD:             N/A
Device VSD:            N/A
PSID:                  MT_0000000575
Security Attributes:   secure-fw
```

### mstflint

* Mentioned in the [alternative methods](https://docs.nvidia.com/dgx/dgx-rhel8-install-guide/installing-bf3-fw-steps.html#alternative-methods).
* Download [here](https://www.openfabrics.org/downloads/mstflint/), but can also be installed on RHEL.
* [mstflint FW Burning Tool README](https://network.nvidia.com/pdf/firmware/mstflint_README.txt).

```console
# dnf install -y -q mstflint
...

# lspci -d 15b3:
0002:01:00.0 Ethernet controller: Mellanox Technologies MT2894 Family [ConnectX-6 Lx]
0002:01:00.1 Ethernet controller: Mellanox Technologies MT2894 Family [ConnectX-6 Lx]

# mstflint -d 0002:01:00.0 q
Image type:            FS4
FW Version:            26.33.1048
FW Release Date:       29.4.2022
Product Version:       26.33.1048
Rom Info:              type=UEFI version=14.26.17 cpu=AMD64,AARCH64
                       type=PXE version=3.6.502 cpu=AMD64
Description:           UID                GuidsNumber
Base GUID:             e8ebd3030070cf12        4
Base MAC:              e8ebd370cf12            4
Image VSD:             N/A
Device VSD:            N/A
PSID:                  MT_0000000575
Security Attributes:   secure-fw
```

## Firmware

Latest firmware binaries: https://network.nvidia.com/support/firmware/firmware-downloads/

## Hardware

Free servers on Beaker with:
* [ConnectX-6](https://beaker.engineering.redhat.com/free/?systemsearch-0.table=Devices%2FDescription&systemsearch-0.keyvalue=&systemsearch-0.operation=contains&systemsearch-0.value=connectx-6)
* [ConnectX-7](https://beaker.engineering.redhat.com/free/?systemsearch-0.table=Devices%2FDescription&systemsearch-0.keyvalue=&systemsearch-0.operation=contains&systemsearch-0.value=connectx-7)
* [BlueField-2](https://beaker.engineering.redhat.com/free/?systemsearch-0.table=Devices%2FDescription&systemsearch-0.keyvalue=&systemsearch-0.operation=contains&systemsearch-0.value=bluefield-2)
* [BlueField-3](https://beaker.engineering.redhat.com/free/?systemsearch-0.table=Devices%2FDescription&systemsearch-0.keyvalue=&systemsearch-0.operation=contains&systemsearch-0.value=bluefield-3)

## Additional information

* [BlueField DPU Update Script (out-of-band)](https://github.com/Mellanox/dpu-update)
* [Upgrading BlueField Software Components Using PLDM](https://docs.nvidia.com/networking/display/bluefieldbsp491/upgrading+bluefield+software+components+using+pldm)
  (part of [NVIDIA BlueField BSP v4.9.1 LTS](https://docs.nvidia.com/networking/display/bluefieldbsp491))
  > The PLDM firmware update is a standardized protocol that enables out-of-band (OOO) firmware upgrades for devices by transferring firmware images between an update agent (e.g., the server's platform BMC) and target devices (i.e., NVIDIA® BlueField®-3). BlueField-3 firmware components can be upgraded using this method.

  > The BlueField-3 PLDM firmware image includes the following components: NIC firmware, ATF/UEFI, BMC firmware, and CEC firmware (i.e., does not include Arm OS or DOCA software). The BlueField-3 PLDM image is specific to each BlueField-3 SKU and can be downloaded from the DOCA download page.
* [Upgrading Firmware via DOCA](https://docs.nvidia.com/doca/sdk/nvidia+doca+installation+guide+for+linux/index.html#src-3416367849_id-.NVIDIADOCAInstallationGuideforLinuxv2.9.1-UpgradingFirmware) (part of [DOCA Documentation v2.9.1 LTS](https://docs.nvidia.com/doca/sdk/index.html))
  > Added support for PLDM firmware update – BlueField-3 devices now support firmware updates
via the PLDM type-5 protocol over MCTP over PCIe, allowing future updates to be performed
through the platform's BMC. This implementation complies with PLDM specification version
1.0.

  > This section explains how to update the NIC firmware on a DOCA installed BlueField OS.
