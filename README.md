# Thiankpad-T430-Hackinosh-OpenCore-bigsur
## SUMMARY

This EFI Folders for running macOS on a Lenovo T430 Laptop using OpenCore 0.6.5(do not update to latest version). Compatible and tested with:  Big Sur

**NOTE**: Read and follow the install instruction carefully and thoroughly!

<summary><strong>EFI Folder Content</strong></summary>

### EFI Folder Structure

EFI
├── BOOT
│   └── BOOTx64.efi
└── OC
    ├── ACPI
    │   ├── DSDT.aml
    │   ├── SSDT-EC.aml
    │   ├── SSDT-Fans-TP.aml
    │   ├── SSDT-HPET.aml
    │   ├── SSDT-PM.aml
    │   ├── SSDT-PNLF.aml
    │   └── SSDT-XOSI.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── AudioDxe.efi
    │   ├── NTFS.efi
    │   ├── OpenCanopy.efi
    │   └── OpenRuntime.efi
    ├── Kexts
    │   ├── AirportBrcmFixup.kext
    │   ├── AppleALC.kext
    │   ├── AppleBacklightInjector.kext
    │   ├── BrcmBluetoothInjector.kext
    │   ├── BrcmFirmwareData.kext
    │   ├── BrcmPatchRAM3.kext
    │   ├── IntelMausi.kext
    │   ├── CPUFriend.kext
    │   ├── SMCProcessor.kext
    │   ├── YogaSMC.kext
    │   ├── Lilu.kext
    │   ├── SMCBatteryManager.kext
    │   ├── VirtualSMC.kext
    │   ├── VoodooPS2Controller.kext
    │   ├── VoodooSDHC.kext
    │   └── WhateverGreen.kext
    ├── OpenCore.efi
    ├── Resources 
    │   ├── Audio
    │   ├── Font
    │   ├── Image
    │   └── Label
    ├── Tools
    │   └── CleanNvram.efi
    └── config.plist


</details>

## HARDWARE SPECS
<details>
<summary><strong>ThinkPad Model</strong></summary>

### ThinkPad T430 Specs 
| Component           | Details                                       |
| ------------------: | :-------------------------------------------- |
| Model               | Lenovo ThinkPad T430.                         |
| BIOS Version        | 2.86, unlocked with 1vyRain                   |
| Processor           | Intel(r) Core i7 3720QM                       |
| Memory              | 16GB Samsung DDR3 1600MHz, Dual-Channel       |
| Hard Disk           | Toshiba 250GB                                 |
| Integrated Graphics | Intel(r) HD Graphics 4000                     |
| Display             | 14.3"TFT Display (1600x900 px)                |
| Audio               | Realtek ALC269VC Rev.3 (Layout-id:`29`)       |
| Ethernet            | Intel(r) 82579LM Gigabit Network Connection   |
| WIFI+BT             | Broadcom BCM94352HMB DW1550, 802.11 a/b/g/n/ac|
</details>
<details>
<summary><strong>Incompatible Components</strong></summary>

###Working
wifi/ethernet
bluetooth
power on/off
USB (all 4!)
Audio
Microphone
Mouse
App Store
Brightness
Webcam
miniDP output is working perfectly (need change ig-platform id from 0x01660004 to 0x01660003 in DSDT.aml and config.list)  
  
###Not working
  
- [ ] SD card reader
- [ ] Fingerprint Reader - model not supported by macOS
- [ ] VGA Port – not supported since macOS Mountain Lion: [Intel HD Graphics VGA Support](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.IntelHD.en.md#vga-support)
</details>

## INSTALLATION
<details>
<summary><strong>Preparation: Dos and Don'ts</strong></summary>

### Dos and Don'ts
Before you copy the EFI onto your system SSD/HDD, you should do the following:

	`AAPL,ig-platform-id 04006601` = HD+/FullHD ≥ 1600x900 px </br>
  
	**HINT**: If your screen turns off during boot, you are using the wrong Framebuffer-Patch!
- **CPU**: The `SSDT-PM.aml` inside the ACPI Folder is for an **Intel i7 3720QM**. If you use a differnt CPU, disable it for now and create your own using `ssdtPRGEN` in Post-Install. (See 'Fixing CPU Power Management' in the 'Post-Install Section')
- **SMBIOS**: Create SMBIOS infos using GenSMBIOS and add the data to `PlatformInfo > Generic`. 
	- Big Sur requires `MaBookPro15,3`
- **Disabling System Integrity Protection (SIP)**: to Disable SIP, go to `NVRAM` > `Add` > `7C436110-AB2A-4BBB-A880-FE41995C9F82 `and change the value of `csr-active-config` according to the installes version of macOS
 - For Big Sur: `67080000`

- **Wifi/Bluetooth**
  - Built-in Intel Wifi/Bluetooth may work. Have a look at [OpenIntelWireless](https://github.com/OpenIntelWireless) to check if your card is supported yet.
  - 3rd Party cards require the `1vyrain` jailbreak to unlock the BIOS in order to disable WLAN Whitelist (unless the 3rd party card is whitelisted)
  - Broadcom cards require an additional kext for Bluetooth. Either `BrcmFirmwareData.kext` in EFI > OC > Kexts which will be injected by OpenCore/Clover or
    `BrcmFirmwareRepo.kext` which needs to be installed into S/L/E since it cannot be injected by Bootloaders, but is a bit more efficient according to the documentation. But it also requires more effort to install when updating Kexts (that's why I am using `BrcmFirmwareData.kext` instead). 
  - If you use a Wif/BT card from a different vendor than Broadcom replace the Kext(s) for networking for your device and update your config before trying to boot with this EFI.
- **Editing/Updating config files**: If you create Snapshots for the included config.plists using `ProperTree`, make sure to double-check the `Kernel` > `Add` Section afterwards for the following:
	- `config.plist` must not include `BlueToolFixup.kext`. If it is present after generating a Snapshot, disable it. 
- **Kexts**: 
	- `NoTouchID.kext` is no longer required for macOS 10.15.7 and beyond, so you can disable it (it's excluded from current releases anyway).
	- If you need additional Features like Sidecar, NighShift, Airplay to Mac or Universal Control you can try adding [Feature Unlock](https://github.com/acidanthera/FeatureUnlock) Kext.
- **Backlight Brightness Level tweaks**: 
  - Set boot-arg `applbkl=1` for reasonable maximum brightness level controlled by `WhateverGreen`. 
  - Set boot-arg `applbkl=0` for increased maximum brightness as defined in `SSDT-PNLF.aml`

</details>
<details>
<summary><strong>EFI Handling</strong></summary>

### EFI How To
0. Download the EFI Folder from the `Releases` Section on the right and unpack it
1. Read "Preparations" Section first
2. Rename the config file of your choice to "config.plist"
3. Mount the EFI
4. Replace EFI Folder
5. Restart
6. **IMPORTANT**: Perform a NVRAM Reset (in Bootpicker, hit Space Bar and select "Clean NVRAM")
7. Reboot again
8. Select macOS to boot. It's currently configured for running macOS Mojave/Catalina but there's an extra config file for running Monterey included. If you want to run Big Sur, use the default config, change `SystemProductName` to `MacBookPro15,3` and `csr-active-config` value accordingly. You can research a suitable/matching SMBIOS for your CPU on everymac.com.

</details>
<details>
<summary><strong>BIOS Settings</strong></summary>

### BIOS Settings
**Latest BIOS Version:** `2.86`
[**DOWNLOAD**](https://pcsupport.lenovo.com/us/en/products/laptops-and-netbooks/thinkpad-t-series-laptops/thinkpad-t430/downloads/ds029246?clickid=RhAUWZ1-exyLRCuwUx0Mo3ELUkERY-RmHTlwSg0&Program=3786&pid=269814&acid=ww%3Aaffiliate%3A74clty&cid=de%3Aaffiliate%3Axg02ds)

**CONFIG [TAB]**

* USB UEFI BIOS Support: `Enabled`
* USB 3.0 Mode: `Enabled`
* Display > Boot Display Device: `ThinkPad LCD`
* Display > OS Detection for NVIDIA Optimus: `Disabled`
* SATA > SATA Controller Mode: `XHCI`
* CPU > Core Multi-Processing: `Enabled`
* CPU > Intel (R) Hyper-Threading: `Enabled` (CPU must support it)

**SECURITY [TAB]**

* Security Chip: `Disabled`
* UEFI BIOS Update Options > Flash BIOS Updating by End-Users: `Enabled`
* UEFI BIOS Update Options > Secure Rollback Prevention: `Enabled`
* Memory Protection: `Enabled`
* Virtualization > Intel (R) Virtualization Technology: `Enabled` (Relevant for Windows only, disabled in macOS via `DisableIOMapper` Quirk)
* I/O Port Access (`Disable` the following devices/features):
	* Wireless WAN
	* ExpressCard Slot
	* eSATA Port
	* Fingerprint Reader
	* Antitheft and Computrace
	* Secure Boot: `Disabled`

**STARTUP [TAB]**

* Boot (Set the Order of Boot devices. Set HDD/SSD as first device)
* UEFI/Legacy Boot: `UEFI only`
* CSM Support: `Disabled`
* Boot Mode: `Quick`
* Boot Order Lock: `Enabled` Enable this *after* you've set-up the order of the Boot Drives. This prohibits `WindowsBootManager` from taking over the first slot of the boot drives.
</details>
<details>
<summary><strong>How to install macOS</strong></summary>

### Installing macOS
If you have already have macOS installed but want to perform a clean install, you can either download macOS from the App Store or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/). It's a hassle-free App than can download macOS High Sierra, Catalina, Big Sur and Monterey and also create a USB Installer for you.

If you are on Windows or Linux follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer)
</details>

## POST-INSTALL
<details>
<summary><strong>Strengthen Security</strong></summary>
Change the following settings to make your systm more secure:

- Change UEFI > APFS: `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for macOS High Sierra up to Big Sur can be found [here](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Library/OcApfsLib.h).</br>

	**BACKGROUND**: OpenCore 0.7.2 introduced a new security feature which prevents loading the APFS driver if it does not match a specific Date and Version. If these values are left at their default `0`, your macOS partition will not show up in the Boot Picker unless macOS Big Sur or newer is installed since the APFS driver will not be loaded. For ease of use (and since I don't know which macOS you will be using) I've deactivated this feature. If you plan to setup a multiboot system running various iterations of macOS you probably should leave it at `-1`. Otherwise you won't be able to boot older OSes.

**NOTE**: You should test this setting first, booting from a USB Stick since it can prevent the system from booting.
</details>
<details>
<summary><strong>Fixing CPU Power Management</strong></summary>

### Fixing CPU Power Management 
1. Open Config
2. Enable the 2 Patches under "ACPI > Delete" (`Drop CpuPm` and `Drop Cpu0Ist`)
3. Save config and reboot
4. Install [ssdtPRGen](https://github.com/Piker-Alpha/ssdtPRGen.sh)
5. Open Terminal and type: sudo /Users/YOURUSERNAME/ssdtPRGen.sh
6. Go to Users/YOURUSERNAME/Library/ssdtPRGen. There you'll find an ssdt.aml
7. Rename `ssdt.aml` to `SSDT-PM.aml` and replace the one in EFI > OC > ACPI with it
8. In config, go to ACPI > Add and re-enable `SSDT-PM.aml` if it is disabled.
9. Disable the two patches from step 2 again.
10. Save config and reboot. 

CPU Power Management should work fine after that. Optionally, you can install Intel Power Gadget to check if the CPU runs within it's specs.

**NOTE 1**: Only necessarry if you use a differnt CPU than i7 3720QM </br>
**NOTE 2**: You can also add modifiers to the terminal command for building the SSDT. You can - for example - drop the low frequency from their default 1200 MHz to 900 MHz in 100 mHz increments, but no lower than that. Otherwise the system crashes during boot. I suggests you experiement with the modifiers a bit.</br>
</details>
<details>
<summary><strong>Fixing Sleep Issues</strong></summary>

