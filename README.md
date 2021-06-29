# Big-Sur-Hackintosh-Gigabyte-Z390M-mATX-GAMING-i5-9600K-PowerColor Radeon-RX-5700

------

***WORK IN PROGRESS***

------

<img src="https://i.postimg.cc/bwjmYFfv/image.png" alt="z390m Gaming" style="zoom: 50%;" />



<img src="https://i.postimg.cc/TY7zp01v/Untitled.png" alt="About This Mac" style="zoom: 67%;" />

 

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

| Kexts                                                        | Last Version | Use                        | Comments                                                     |
| ------------------------------------------------------------ | ------------ | -------------------------- | ------------------------------------------------------------ |
| [Lilu](https://github.com/acidanthera/Lilu/releases)         | 1.5.3        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases) | 1.2.4        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [WhateverGreen](https://github.com/acidanthera/whatevergreen/releases) | 1.5.0        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [NVMeFix](https://github.com/acidanthera/NVMeFix/releases)   | 1.0.8        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [IntelMausi](https://github.com/acidanthera/IntelMausi/releases) | 1.0.6        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [RadeonBoost](https://forums.macrumors.com/threads/tired-of-low-geekbench-scores-use-radeonboost.2231366/) | 1.6          | /Volumes/EFI/EFI/OC/Kexts/ | Not Currently in use (Sunday, June 27, 2021)                 |
| [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector/releases) | 3.3.4        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [USBToolBox kext](https://github.com/USBToolBox/kext)        | 1.1.0        | /Volumes/EFI/EFI/OC/Kexts/ |                                                              |
| [UTBMap](https://github.com/USBToolBox/tool)*                | 0.0.9        | /Volumes/EFI/EFI/OC/Kexts/ | Generated using the [ToolBox tool](https://github.com/USBToolBox/tool) |

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
| [OpenCore](https://github.com/acidanthera/OpenCorePkg/releases) | 0.7.0        | /Volumes/EFI/ |          |

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

# Usefull Links

# Questions?

Please post on [/r/Hackintosh](http://hackintosh.reddit.com/) on reddit as they heavily push the vanilla hackintosh method and have been great help in the past

