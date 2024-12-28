# Big-Sur-Hackintosh-Gigabyte-Z390M-mATX-GAMING-i5-9600K-PowerColor Radeon-RX-5700

<img src="https://i.postimg.cc/8zgH3gfG/image.png" alt="About This Mac" style="zoom: 67%;" />

## Table of Contents
- [Introduction](#introduction)
- [Wi-Fi Card BCM943602CDP Doesn't Work](#wi-fi-card-bcm943602cdp-doesnt-work)
- [Quick Overview on How I Upgraded](#quick-overview-on-how-i-upgraded)
- [Key Differences from Gilberto-Mascena's Guide](#key-differences-from-gilberto-mascenas-guide)
- [Installation Process](#installation-process)
- [Credits](#credits)
- [Questions or Issues](#questions-or-issues)
- [Detailed Guide](#detailed-guide)
  - [What's Working/What's Not](#whats-workingwhats-not)
  - [Hardware](#hardware)
  - [Process](#process)
  - [Preparing your Hackintosh](#preparing-your-hackintosh)
    - [BIOS Configuration](#bios-configuration)
    - [Gathering Files](#gathering-files)
    - [USB Mapping Recognized](#usb-mapping-recognized)
    - [Creating your SSDT/DSDT](#creating-your-ssdt-dsdt)
  - [Navigating your OpenCore config.plist](#navigating-your-opencore-configplist)
    - [Good Practices / plist Workflow](#good-practices--plist-workflow)
    - [Starting Clean](#starting-clean)
    - [config.plist contents](#configplist-contents)
      - [Booter](#booter)
      - [DeviceProperties](#deviceproperties)
      - [SMBIOS](#smbios)
      - [Kernel](#kernel)
      - [Misc](#misc)
      - [NVRAM](#nvram)
  - [MacOS Installer](#macos-installer)
  - [Updating your EFI](#updating-your-efi)

---

## Introduction
This guide is an update to the previous Hackintosh guide for the Gigabyte Z390M GAMING mATX motherboard. It now targets macOS Sequoia (15.1.1 as of Dec 2, 2024). The majority of the credit for this update goes to [Gilberto-Mascena's Z390M-GAMING EFI].

[Continue with the rest of your content as it is...]

---

The Table of Contents links directly to each section, making it easier for readers to navigate through your guide.

Citations:
[1] https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/14887947/5234e787-ce83-4c4f-b9ac-4a56cfbdfca6/paste.txt

## **Wi-Fi Card BCM943602CDP Doesn't Work**
The Wi-Fi functionality of the BCM943602CDP card is not supported, though Bluetooth works. For a workaround, refer to [this guide](https://github.com/5T33Z0/OC-Little-Translated/blob/main/14_OCLP_Wintel/Enable_Features/WiFi_Sonoma.md).

## Quick Overview on How I Upgraded

- Backup EFI: Ensure you have a backup of your current EFI folder.
  
- Update OpenCore:
  - Replace old OpenCore files with the latest release and update `config.plist`.

- Update Kexts and Drivers: Download and replace with the latest versions.

- Boot the current macOS version to validate updated EFI

- Prepare macOS Installer:
  - Use a tool to download the full macOS installer and create a bootable USB.

- Copy Updated EFI to USB: Mount and copy the updated EFI to the USB's EFI partition.

- Boot into Install Media: Restart and boot from the newly created USB drive.

## Key Differences from Gilberto-Mascena's Guide

- This guide uses an Intel Core i5-9600K CPU, while Gilberto-Mascena uses an i7-9700F
- The boot argument `agdpmod=pikera` is added to prevent black screen issues with the RX 5700 GPU
- [USBToolBox](https://github.com/USBToolBox/tool) was used to create a custom USB port map. The resulting `UTBMap.kext` and `USBToolBox.kext` are included in the EFI/OC/Kexts directory.
- Verbose `-v` boot flag is set for detailed startup messages



## Installation Process

The general installation process remains the same as the original guide:

1. Build USB Map using USBToolBox
2. Configure ACPI
3. Gather required Kexts, Drivers and Tools 
4. Configure OpenCore config.plist
5. Update BIOS settings
6. Install macOS Sequoia

Refer to the original guide for detailed steps on each part of the process. Use the updated kext and driver versions mentioned in this guide.

## Credits

- [Gilberto-Mascena](https://github.com/Gilberto-Mascena) for the Z390M-GAMING EFI update that inspired this guide
- The hackintosh community on [r/hackintosh](https://www.reddit.com/r/hackintosh/) for their support and troubleshooting help

## Questions or Issues

If you encounter any problems following this guide, please post your questions on the [r/hackintosh subreddit](https://www.reddit.com/r/hackintosh/) where there is a very knowledgeable and helpful hackintosh community.


---
# Detailed Guide
---

------

This guide aims to build a parallel to the Apple iMac 27-Inch "Core i5" 3.7 (5K, 2019). This is a vanilla install guide and I reccomend against using any pre-packaged images or distrobutions. 

# Details

### What's Working/What's Not

#### Working

- Ethernet
- IGPU in headless mode
- App Store
- Wake/Sleep
- Restart
- Shutdown
- USB (Correct SS/HS speed)
- All DP and HDMI port
- Dual monitor from boot
- Apple Music (iTunes)
- iMessage
- Facetime
- Handoff
- Airdrop
- Continuity
- AirPlay
- Apple TV+ 
- Netflix/Prime in Safari 
- Docker
- VMware Fusion

#### Not Tested

- FileVault
- Power Nap
- Built-in WiFi and Bluetooth. May work with [itlwm](https://openintelwireless.github.io/). If you need stable WiFi+BT, use native apple card

### Hardware

#### In the Case

| Components    | SKU                                            | Comments                                                     |
| ------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| CPU           | Intel Core i5-9600K                            | Coffee Lake - 3.70 GHz Hexa-Core <br />Base Frequency 3.70 GHz <br />Max Turbo Frequency 4.60 GHz<br />(BX80684I59600K) |
| iGPU          | Intel UHD Graphics 630                         | Only for DRM support (Apple TV+)                             |
| Motherboard   | Gigabyte Z390M Gaming mATX                     | Not interchangeable with other SKUs                          |
| WiFi Adapter  | BCM943602CDP (4 antennas)                      | Bluetooth 4.2 (Including NGFF to M.2 Adapter)                |
| Graphics Card | PowerColor Radeon RX 5700 8 GB Video Card      |                                                              |
| HDD - NVMe    | WD Black SN750 NVMe SSD 2TB                    | MacOS boot drive                                             |
| HDD - NVMe    | WD Black SN750 NVMe SSD 1TB                    | Win10 boot drive                                             |
| HDD - NVMe    | WD Black SN750 NVMe SSD 1TB                    | Shared drive (games + vm storage)                            |
| RAM           | Corsair Vengeance LPX 32GB (2x16GB) DDR4 X.M.P | Recommend 3200MHz / Better 3600MHz                           |
| PSU           | Seasonic FOCUS PX-550                          | Full-Modular, Fan Control, Silent                            |
| PC Case       | Goodisory S200                                 | SFF                                                          |
| CPU Cooler    | Noctua NH-L9x65                                | Low-Profile CPU Cooler                                       |
| Cooling Fan   | Noctua NF-F12 PWM                              | 12cm                                                         |



#### On my Desk

| Components | SKU                                                          | Comments                                                  |
| ---------- | ------------------------------------------------------------ | --------------------------------------------------------- |
| Monitor    | Dell UltraSharp U2718Q                                       | 27" 16:9 4K HDR IPS                                       |
| Monitor    | Dell Gaming Monitor S2721DGF                                 | 27″ 1440p 165Hz HDR Nano IPS panel                        |
| Audio      | Firestone Audio Bravo 24/96 Digital Processor                | 24-bit audio via USB 2.0<br />Does not require drivers    |
| Keyboard   | [Leopold FC660C](https://deskthority.net/wiki/Leopold_FC660C) | [Topre switch](https://deskthority.net/wiki/Topre_switch) |
| Mouse      | Logitech MX Master 2                                         | Logitech Unifying Receiver - RF Wireless                  |
| Mouse      | Logitech G604                                                | Lightspeed Wireless                                       |



# Process 

1. Build USB Map 
2. Build SSDT/DSDT
3. Gather Files
4. Configure OpenCore
5. Configure BIOS

# Preparing your Hackintosh

### BIOS Configuration

| Items                                                        | Version | Date                  |
| ------------------------------------------------------------ | :------ | --------------------- |
| [BIOS](https://www.gigabyte.com/Motherboard/Z390-M-GAMING-rev-10/support#support-dl-bios) | F9m     | Sunday, June 27, 2021 |

- [ ] Make sure the IGP is set to Enabled for the Framebuffer to be recognized (Auto will not work)
- [ ] Disable CFG Lock
- [ ] Disable Serial Port
- [ ] Advanced Mode > Settings > Above 4G Decoding > Enabled
- [ ] Advanced Mode > Settings > USB Configuration > XHCI Hand-off > Enabled
- [ ] Advanced Mode > Boot > CSM Support > Disabled


### Gathering Files

| Kexts                                                        | Last Version | Use                            | Comments                                                     |
| ------------------------------------------------------------ | ------------ | ------------------------------ | ------------------------------------------------------------ |
| [Lilu](https://github.com/acidanthera/Lilu/releases)         | 1.5.5        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases) | 1.2.6        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| [WhateverGreen](https://github.com/acidanthera/whatevergreen/releases) | 1.5.2        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)   | 1.0.9        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| [IntelMausi](https://github.com/acidanthera/IntelMausi/releases) | 1.0.7        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| ~~[RadeonBoost](https://forums.macrumors.com/threads/tired-of-low-geekbench-scores-use-radeonboost.2231366/)~~ | ~~1.6~~      | ~~/Volumes/EFI/EFI/OC/Kexts/~~ | Not Currently in use (Sunday, June 27, 2021)                 |
| [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector/releases) | 3.3.4        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| [USBToolBox kext](https://github.com/USBToolBox/kext)        | 1.1.0        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |
| [UTBMap](https://github.com/USBToolBox/tool)*                | 0.0.9        | /Volumes/EFI/EFI/OC/Kexts/     | Generated using the [ToolBox tool](https://github.com/USBToolBox/tool) |
| [CPUFriend](https://github.com/acidanthera/CPUFriend/releases) | 1.2.4        | /Volumes/EFI/EFI/OC/Kexts/     |                                                              |

*USB Map must be generated. See USB section below. 

| Drivers         | Use                          | Comments                                                     |
| --------------- | ---------------------------- | ------------------------------------------------------------ |
| HfsPlus.efi     | /Volumes/EFI/EFI/OC/Drivers/ |                                                              |
| OpenRuntime.efi | /Volumes/EFI/EFI/OC/Drivers/ | From [OpenCore](https://github.com/acidanthera/OpenCorePkg/releases) download |



| Tools (non-EFI)**                                     | Last Version | Use                  |
| ----------------------------------------------------- | ------------ | -------------------- |
| [USBToolBox tool](https://github.com/USBToolBox/tool) | 0.0.9        | Generating USB Map   |
| [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)    |              | Generate SMBIOS Info |
| [gibMacOS](https://github.com/corpnewt/gibMacOS)      | 9a683e7      | Download MacOS       |
| [ProperTree](https://github.com/corpnewt/ProperTree)  | d9bbf09      | .plist editor        |

**Do not add to EFI/OC/Tools directory

### USB Mapping 

Recognized Properly maping your USB ports is critical for this guide to work. 

#### USBToolBox Process

1. Launch a Windows 10 Live CD
2. Download and run [USBToolBox](https://github.com/USBToolBox?type=source)/**[tool](https://github.com/USBToolBox/tool)**
3. Plug USB devices in each of the root USB ports on your motherboard 
4. Select Discover Ports and wait for the listing to populate
   1. Do not connect hubs
5. Enter Select Ports screen
6. Select the ports that are populated and their associated ports - **Keep this below 15 total ports**
7. Press 'k' to build your kext 
8. Move the generated kext to your '/Volumes/EFI/EFI/OC/Kexts/'
9. Also place the [USBToolBox](https://github.com/USBToolBox?type=source)/**[kext](https://github.com/USBToolBox/kext)** in the same folder - '/Volumes/EFI/EFI/OC/Kexts/'

### Creating your  SSDT/DSDT

Two options: 

##### Create your own SSDT/DSDT using SSDTTime (Prefered) 

The end result should look similar to this: 

<img src="https://i.postimg.cc/0y0BYqsh/image.png" style="zoom:50%;" />

| ACPI FILE     | SOURCE                                                       | Use               | Guide                                                        |
| ------------- | ------------------------------------------------------------ | ----------------- | ------------------------------------------------------------ |
| SSDT-EC.aml   | Generate using  [SSDTTime](https://github.com/corpnewt/SSDTTime) | /EFI/EFI/OC/ACPI/ | [Dortania Guide](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-methods/ssdttime.html#wrapping-up) |
| SSDT-PLUG.aml | Generate using  [SSDTTime](https://github.com/corpnewt/SSDTTime) | /EFI/EFI/OC/ACPI/ | [Dortania Guide](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug-methods/ssdttime.html) |
| SSDT-PMC.aml  | Generate using  [SSDTTime](https://github.com/corpnewt/SSDTTime) | /EFI/EFI/OC/ACPI/ | [Dortania Guide](https://dortania.github.io/Getting-Started-With-ACPI/Universal/nvram-methods/ssdttime.html) |
| SSDT-USBX.aml | [Download](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/SSDT-USBX.aml) | /EFI/EFI/OC/ACPI/ | [Dortania Notes](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-methods/ssdttime.html) |



##### Download my ACPI folder 

From the files provided you can use the ACPI section with limited success. It's a reasonable place to start but you should eventually generate your own SSDT/DSDT. Unless you are using the exact same CPU/Motherboard combination you will have to [swap in some generic prebuilt ACPI](https://dortania.github.io/Getting-Started-With-ACPI/ssdt-methods/ssdt-prebuilt.html) files. 



# Navigating your OpenCore config.plist



### Good Practices / plist Workflow

- Use versioning! (Git repo or iCloud Drive - Use something!)
- Use [ProperTree](https://github.com/corpnewt/ProperTree)
  - First place all the files properly in your EFI folder
  - After the config is opened, press **Cmd/Ctrl + Shift + R** and point it at your EFI/OC folder to perform a "Clean Snapshot":
    - This will remove all the entries from the config.plist and then adds all contents of your /EFI/ folder content to the config.plist
  - Then go through and make your plist changes 

### Starting Clean

When creating a new hackintosh or if you're updating OpenCore start with a fresh conflig.plist to make sure you're working with what is compatible with the latest version of OpenCore. Things frequently move around - You'll have to pay attention. 

| Items                                                        | Last Version | Use           | Comments |
| ------------------------------------------------------------ | ------------ | ------------- | -------- |
| [OpenCore](https://github.com/acidanthera/OpenCorePkg/releases) | 0.7.2        | /Volumes/EFI/ |          |

Follow the [offical guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/opencore-efi.html) for setting up your installer EFI 

- Sample/config.plist(opens new window)
  - See previous section on how to obtain: [config.plist Setup](https://dortania.github.io/OpenCore-Install-Guide/config.plist/)

# config.plist contents

#### Booter

| Quirk                  | Enabled | Comment               |
| :--------------------- | :------ | :-------------------- |
| DevirtualiseMmio       | YES     |                       |
| EnableWriteUnprotector | NO      |                       |
| ProtectUefiServices    | YES     | Needed on Z390 system |
| RebuildAppleMemoryMap  | YES     |                       |
| SyncRuntimePermissions | YES     |                       |

#### DeviceProperties

| AAPL,ig-platform-id | Comment                                                      | Enabled? |
| :------------------ | :----------------------------------------------------------- | -------- |
| **`07009B3E`**      | Used when the Desktop iGPU is used to drive a display        | Yes      |
| **`00009B3E`**      | Alternative to `07009B3E` if it doesn't work                 |          |
| **`0300913E`**      | Used when the Desktop iGPU is only used for computing tasks and doesn't drive a display |          |

##### PciRoot(0x0)/Pci(0x1b,0x0)

This was removed as I am using USB devices for audio



#### SMBIOS

**YOU MUST UPDATE SMBIOS INFO for iServices to work**

![Update SMBIOS](https://i.postimg.cc/bNxmrDfP/image.png)

If you use the config.plist from this project you must update the following. 

| Key                | Config.plist            | Tool      | Comments                                                     |
| ------------------ | ----------------------- | --------- | ------------------------------------------------------------ |
| MLB                | PlatformInfo -> Generic | GenSMBIOS |                                                              |
| ROM                | PlatformInfo -> Generic |           | [This tutorial](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#choose-a-mac-address) then this [next step](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html#derive-the-corresponding-rom-value) |
| SystemProductName  | PlatformInfo -> Generic | GenSMBIOS |                                                              |
| SystemSerialNumber | PlatformInfo -> Generic | GenSMBIOS |                                                              |
| SystemUUID         | PlatformInfo -> Generic | GenSMBIOS |                                                              |

| SMBIOS   | Hardware                                |
| :------- | :-------------------------------------- |
| iMac19,2 | Currently using (Sunday, June 27, 2021) |
| iMac19,1 | For Mojave and newer                    |

####  [Kernel](https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html#kernel)

Follow the [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html#kernel) for this section 

#### Misc

Follow the [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html#misc) for this section 

#### NVRAM

##### boot-args

Follow the [official guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/coffee-lake.html#nvram) for this section 

```
keepsyms=1 debug=0x100 alcid=1 agdpmod=pikera -v
```

| Boot arg       | Use                                                          |      |      |
| -------------- | ------------------------------------------------------------ | ---- | ---- |
| keepsyms=1     | This is a companion setting to debug=0x100 that tells the OS to also print the symbols on a kernel panic. |      |      |
| debug=0x100    | This disables macOS's watchdog which helps prevents a reboot on a kernel panic. |      |      |
| alcid=1        | **Unused for this guide** - Used for setting layout-id for AppleALC |      |      |
| agdpmod=pikera | Used for disabling board ID checks on Navi GPUs(RX 5000 series), without this you'll get a black screen. **Don't use if you don't have Navi**(ie. Polaris and Vega cards shouldn't use this) |      |      |
| -v             | This enables verbose mode                                    |      |      |

# MacOS Installer

# Updating your EFI

You'll want to regularly update your EFI to keep the process simple. I typically wait for the latest release (11.x.x) of MacOS to mature a few weeks before attempting this procedure as OpenCore and the Kext developers will have time to catch up. 

1. Download the needed files

   - See the [gathering files section](#gathering-files).

2. Download the latest OpenCore [Releases](https://github.com/acidanthera/OpenCorePkg/releases) - Typically the first Monday of every month

3. In the download file you'll find the differences.pdf

   - /OpenCore-0.*.*-RELEASE/Docs/Differences.pdf
   - Review the document and see if any of the OC features you depend on have any updates. This is where you will find what is incompatible between versions. 
     - [ ] Many OC updates and incompatibilities between versions stem from using ALC audio. I forgo the on-board audio and go with an external USB DAC which is fully compatible with MacOS.

   



# Useful Links

# Questions?

Please post on [/r/Hackintosh](http://hackintosh.reddit.com/) on reddit as they heavily push the vanilla hackintosh method and have been great help in the past
