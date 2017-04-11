# My Kaby Lake Build
This guide a work in progress. Your setup depends on your hardware. Please refer to reddit.com/r/hackintosh or tonymacx86.com for help.

## Installation
Adapted from CorpNewt's Skylake guide: https://www.reddit.com/r/hackintosh/comments/4pgfmn/does_my_build_look_okay/

##### Additional Kaby Lake support here:
- https://www.tonymacx86.com/threads/success-sierra-win10-i5-7500-ga-h270n-wifi-quadro-k1200-samsung-960pro-nvme-imac17-1.215787/
- https://www.tonymacx86.com/threads/kaby-lake-hd620-working.209598/

### What you need
- 8+ GB Flash drive - choose a good one, drive failure is real
- Access to a Mac running macOS - or a VM with macOS
  - Install macOS Sierra.app to `/Applications/`
- [Clover EFI Bootloader Installer](https://sourceforge.net/projects/cloverefiboot/)
- [Clover Configurator](https://www.tonymacx86.com/resources/clover-configurator.328/)
- [maciASL](https://bitbucket.org/RehabMan/os-x-maciasl-patchmatic/downloads/)


- [FakeSMC.kext](https://www.tonymacx86.com/resources/fakesmc.325/) - to convince the OS you have a "real" mac
- [IntelMausiEthernet.kext](https://bitbucket.org/RehabMan/os-x-intel-network/downloads/) - LAN
- [AppleALC.kext](https://github.com/vit9696/AppleALC/releases) - onboard Audio
- [Lilu.kext](https://github.com/vit9696/Lilu/releases) - Audio
- [USBInjectAll.kext](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/) - to inject your USB ports - part 1 of the 15 port limit increase to 30
- [FakePCIID.kext](https://bitbucket.org/RehabMan/os-x-fake-pci-id/downloads/) - for onboard kabylake graphics (also FakePCIID_Intel_HD_Graphics.kext)
- [HFSPlus.efi](https://github.com/JrCs/CloverGrowerPro/tree/master/Files/HFSPlus/X64) - better than the default VBoxHfs-64.efi
- [config.plist](http://www.tonymacx86.com/attachments/config-plist.163681/) - from the Skylake Starter Guide - bare-bones config that we will build on or find mine in this repo
- If you have a an NVIDIA GPU: [NVIDIA Web Driver](http://www.insanelymac.com/forum/topic/306535-nvidia-web-driver-updates-for-el-capitan-update-06062016/) - to enable your GTX card - You must get the correct version for your macOS build version



### Pre-install - do on your _real_ Mac
1. Download macOS `Sierra` from the App Store.

2. `diskutil list` to see which disk# your USB flash drive is. Then,
```
diskutil partitionDisk /dev/diskX GPT JHFS+ "USB" 100%
```
but replace "X" with your disk #

3. [USB install creator](https://github.com/corpnewt/USB-Installer-Creator) - this is a script that does step 2 for you. I still prefer to do it manually.
```
sudo "/Applications/Install macOS Sierra.app/Contents/Resources/createinstallmedia" --volume "/Volumes/USB" --applicationpath "/Applications/Install macOS Sierra.app" --nointeraction
```
sudo "/Users/Elk/Desktop/Install macOS Sierra.app/Contents/Resources/createinstallmedia" --volume "/Volumes/USB2" --applicationpath "/Users/Elk/Desktop/Install macOS Sierra.app" --nointeraction

4. Use edited `example-config.plist` (some edits suggested by CorpNewt for Skylake build) and rename it to `config.plist`. You will need to use Clover Configurator to add iMac 7,1 to the SMBIOS.
  - Copy config to `EFI` partition and replace the default `config.plist` in `/EFI/Clover/`. Some key components of the config are highlighted in this [Skylake guide](https://www.reddit.com/r/hackintosh/comments/4pgfmn/does_my_build_look_okay/), but they also work for Kaby Lake.

5. Install Clover to USB with these options:
```
Install for UEFI booting only
Install Clover in the ESP
Drivers64-UEFI
  OsxAptioFix2Drv-64
  PartitionDxe-64
```

6. Add the following kexts to `EFI/Clover/kexts/other`:
- FakeSMC.kext
- IntelMausiEthernet.kext
- USBInjectAll.kext
- AppleALC.kext
- FakePCIID
- FakePCIID_Intel_HD_Graphics.kext

USB setup is done. Make a folder called `Post_Install` and drop everything we downloaded into this folder. Put the folder in the USB to be used on the hackintosh.

7. BIOS: These are the suggested settings for Skylake mobo BIOS:
BIOS Settings
>Load Optimized Defaults
>If your BIOS has a VT-d setting, disable it
>If your system has CFG-Lock, disable it
>If your system has Secure Boot Mode, disable it
>Set OS Type to Other OS
>Set XHCI Handoff to Enabled
>If you have a Serial port, disable it
>Make sure your BIOS time is accurate

Also, set the display to iGPU for onboard graphics or PCI for a GPU.



### Install
1. Boot from the USB. You may need to change the boot order in the BIOS or choose the USB from the boot menu.
1. In the Clover boot menu, select `Boot macOS from Install macOS Sierra`.
1. From within the macOS install, use Disk Utility to format your SSD as `Journaled HFS+`` with a `GUID Partition Table`.
1. Install macOS to the formatted drive. **Note:** Some installations are two parts and require a restart. Simply boot from the USB as you did before.
1. Once installed, boot from the USB again but select macOS HFS drive.
1. Walk through the setup. Once at the desktop, run the Clover installer package again with the same settings except this time you'll run the installer on the SSD.
1. If you have a GPU, download the correct Web Driver for your build. This will require a reboot, but don't reboot yet. Make your `arguments` portion of the `config.plist` read like this:
```
<key>Arguments</key>
<string>dart=0 nvda_drv=1 -v</string>
```
1. Add the kexts that you added to your flash drive's `EFI` partition to your new SSD's `EFI` partition.



### Post-Install
1. **General Config** - [Clover Configure](https://corpnewt.com/topic/46/how-to-configure-the-clover-config)
1. **Graphics** - change the following portion of the `config.plist`:
```
<key>Graphics</key>
<dict>
    <key>Inject</key>
    <dict>
        <key>ATI</key>
        <false/>
        <key>Intel</key>
        <true/>
        <key>NVidia</key>
        <false/>
    </dict>
    <key>NvidiaSingle</key>
    <false/>
    <key>ig-platform-id</key>
    <string>0x19160000</string>
</dict>
```
I also had to add the `FakePCIID.kext` and `FakePCIID_Intel_HD_Graphics.kext` to improve the Intel graphics.
1. **Display** - Follow instructions for [Pixel Clock Patch](https://github.com/Floris497/mac-pixel-clock-patch-V2) and install SwitchResX for custom resolutions/refresh rate. You'll also need FakePCIID and FakePCIID_Intel_HD_Graphics.kext along with the GFX spoof.
1. **Aux Audio** - For Realtek ALC1220 audio, add `AppleALC.kext` and `Lilu.kext` to the EFI partition.
1. **Onboard HDMI Audio** - No fix yet
1. **SSDT** - use this script for power management [here](https://github.com/Piker-Alpha/ssdtPRGen.sh)
1. **iMessage** - [this guide](https://www.tonymacx86.com/threads/an-idiots-guide-to-imessage.196827/) still works
