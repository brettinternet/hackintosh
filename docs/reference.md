- [SMBIOS](#smbios)
- [Kexts](#kexts)
- [Backup](#backup)
- [NVRAM](#nvram)

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
Most kexts should be put in `/EFI/Clover/kexts/other` but some kexts might need to be installed to `/Library/Extensions`. This is preferred over `/System/Library/Extensions`, although both would work on the system.

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

# Backup
[Backup/restore script here](https://github.com/corpnewt/EFI-Backup-Restore)

### EFI
```
dd if=/dev/diskXsY of=/path/to/backup/efi.img
```

### HFS
Time Machine to a separate drive. If that drive is a bootable then you can restore the EFI partition with `dd if=/path/to/backup/efi.img of=/dev/diskXsY` from another linux/macOS on another drive on the machine.

You may also consider something like CCC which creates a bootable backup.


# DSDT
[What is DSDT?](http://wiki.osx86project.org/wiki/index.php/DSDT) This [here](https://clover-wiki.zetam.org/Fixing-DSDT) is also a good reference for understanding DSDT.

Here's some good [introductory steps to DSDT](http://www.macbreaker.com/2014/03/how-to-edit-your-own-dsdt-with-maciasl.html) with maciASL.


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
