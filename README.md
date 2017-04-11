# A setup reference for my Hackintosh build
* [Hardware](./docs/hardware.md)
* [Setup](./docs/setup.md)
* [Troubleshoot](./docs/troubleshoot.md)
* [Reference](./docs/reference.md)

# Hackintosh?
Apple's overpriced offering is moving away from modular, upgradable hardware. Some recent unveils of the iMac, Mac Pro, and MacBook Pro failed to meet the expectations of many professionals that demand improved computer specs. Using the more powerful hardware available with macOS can be the ultimate environment for a developer--unix coupled with strong machinery. For example, at the time of writing this, I'm on my Kaby Lake build, but current Apple hardware doesn't offer the newest Intel generation.

Should you build a hackintosh? Probably not. There's a lot of troubleshooting and tinkering involved. But if you're okay with that, then try it out! Check out reddit.com/r/hackintosh, tonymacx86.com and insanelymac.com to start.

![tosh](./SystemInfo.png)

# Backup

### EFI
```
dd if=/dev/diskXsY of=/path/to/backup/efi.img
```

### HFS
Time Machine to a separate drive. If that drive is a bootable then you can restore the EFI partition with `dd if=/path/to/backup/efi.img of=/dev/diskXsY`.

You may also consider something like CCC which creates a bootable backup.


# Quick Reference
##### Repair permissions:
```
sudo /usr/libexec/repair_packages --repair --standard-pkgs --volume /
```

##### Rebuild kext cache:
```
sudo rm -r /System/Library/Caches/com.apple.kext.caches
sudo touch /System/Library/Extensions
sudo kextcache -update-volume /
```
