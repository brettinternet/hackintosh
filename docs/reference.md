---
- [Updating macOS](#updating-macos)
- [Clover Theme](#clover-theme)
- [SMBIOS](#smbios)
- [Kexts](#kexts)
- [Backup](#backup)
- [DSDT](#dsdt)
- [SSDT](#ssdt)
- [NVRAM](#nvram)
- [BIOS settings](#bios-settings)
---

# Updating macOS
1. Checkout release notes on tonymacx86.com and read comments to see general issues with upgrading macOS versions.
1. Upgrade [Clover EFI Bootloader Installer](https://sourceforge.net/projects/cloverefiboot/)
1. Download and replace the latest [kexts](./setup.md#what-you-need).
    1. Especially make sure the [NVMe patch](https://github.com/RehabMan/patch-nvme) is supported
    1. Delete the old system kext with `sudo rm -Rf /Library/Extensions/HackrNVMeFamily-10_12_5.kext`
    1. [Copy](#ssdt) the new kext over
1. Be prepared to troubleshoot
1. Upgrade in the App Store


# Clover Theme
- [Minimal Clover Theme](https://github.com/theracermaster/clover-minimal)
![Screenshot of the theme](http://i.imgbox.com/4gssLdSI.png)

# SMBIOS

Generation | iMac | Notes
:----|:----|:----
Sandy Bridge | 12,2 |
Ivy | 13,2 | stable
Haswell | 14,2 | best NVIDIA support, stable
Haswell Refresh | 15,1 |
Skylake | 17,1 | best skylake/kaby support, somewhat stable

# Kexts

### Installing Kexts
Most kexts _should_ be put in `/EFI/Clover/kexts/other` but some kexts might need to be installed to `/Library/Extensions`. Putting kexts in `/L/E` is preferred over `/System/Library/Extensions`, although both would work on the system.

#### Repairing Permissions and Rebuilding Kext Cache
These are good maintenance tools - they ensure permissions are correct on the boot drive, and that the kext cache is not populated with old, or unused kexts. They are all entered into the Terminal.
##### For repairing permissions:
```
sudo /usr/libexec/repair_packages --repair --standard-pkgs --volume /
```

##### For rebuilding kext cache:
```
sudo rm -r /System/Library/Caches/com.apple.kext.caches
sudo touch /System/Library/Extensions
sudo kextcache -update-volume /
```

### Delete disfunctional kexts from Windows
1. Run diskpart as admin
1. `list disk` and `select disk X` (check disk management for disk number)
1. `list partition` and `select partition X` (select the EFI partition)
1. `assign letter=x` (choose a letter not in use)
1. Windows rights prevent the drive from showing in File Explorer, but a good hack is to open Notepad as admin and do `File` > `open` and navigate to the drive's `EFI/Clover/kexts` to trick the Windows rights
1. Delete the kext files from the open window

# Backup
[Backup/restore script here](https://github.com/corpnewt/EFI-Backup-Restore)

### EFI
```
dd if=/dev/diskXsY of=/path/to/backup/efi.img
```

### HFS
Time Machine to a separate drive. If that drive is a bootable then you can restore the EFI partition with `dd if=/path/to/backup/efi.img of=/dev/diskXsY` from another linux/macOS on another drive on the machine.


### Create bootable backups or clone your drive

1. Use [Carbon Copy Cloner](https://bombich.com/) to clone the drive to the new location.
1. Install an EFI partition to the new drive with Clover Bootloader.
1. Copy the EFI folder on the older drive to the new drive's EFI partition and replace the folder.


# DSDT
[What is DSDT?](http://wiki.osx86project.org/wiki/index.php/DSDT) [This](https://clover-wiki.zetam.org/Fixing-DSDT) is also a good reference for understanding DSDT.

Here's some good [introductory steps to DSDT](http://www.macbreaker.com/2014/03/how-to-edit-your-own-dsdt-with-maciasl.html) with maciASL.


# SSDT

## Create an SSDT for an NVMe device

1. Install Windows on the NVMe device to locate the ACPI path of the device in the ACPI namespace.
1. Read the BIOS device name of the NVM Express Controller in `Storage controllers`.
1. Back in macOS, use MaciASL to create an `.aml` for your own SSDT using the code provided [here](https://www.tonymacx86.com/threads/guide-hackrnvmefamily-co-existence-with-ionvmefamily-using-class-code-spoof.210316/).
1. Use [patch-nvme](https://github.com/RehabMan/patch-nvme) to install the `HackrNVMeFamily*.kext` to system kexts
    1. `./patch_nvme.sh --spoof` to run the script
    1. `sudo cp -R HackrNVMeFamily-10_12_5.kext /Library/Extensions`
    1. `sudo kextcache -i /` to rebuild the kext cache

# NVRAM

### Testing for working NVRAM:
1. Open the terminal
1. Clear nvram:
```
sudo nvram -c
```
1. Write in a nonsense var (ensures that nvram is actually cleared - and gives us something to check):
```
sudo nvram myVar=Test
```
1. Reboot
1. Check for myVar in nvram:
```
nvram -p
```
this will output the contents of NVRAM, if myVar is in there - then it's working.
  - If it's not working - and you don't see myVar in NVRAM - try adding `EmuVariableUefi-64.efi` to `EFI -> CLOVER -> drivers64UEFI` (or if it's already there, try removing it), and then run the above steps again to test.


# BIOS settings
+ Load Optimized Defaults
+ If your BIOS has a VT-d setting, disable it
+ If your system has CFG-Lock, disable it
+ If your system has Secure Boot Mode, disable it
+ Set OS Type to Other OS
+ Set XHCI Handoff to Enabled
+ If you have a Serial port, disable it
+ Make sure your BIOS time is accurate
+ set the display to `iGPU` for onboard graphics or `PCI` for a GPU.
