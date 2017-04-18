# Troubleshoot
Search tonymacx86.com with Google `site:tonymacx86.com [error string or search text]`

Type|Issue|Solution
:----|:----|:----
Audio | Onboard audio through the mobo's HDMI doesn't work |
Install | Error from Clover boot about not being a compatible Mac | Use clover to redefine SMBIOS to a more recent iMac like 14,2 or 17,1
Install | Kernel panic at start with "system uptime.." | use clover to fix
Boot | panic at graphics load in verbose boot | Lift your pre-allocated DVMT - Needs to be 64 or 96MB in BIOS. 64MB was recommended
Install | Installer error: `"This copy... is damaged, and can't be used...."` | I had this error with 10.12.4, install the 10.12.3 image instead (get DL link from CorpNewt group)
Install | `"The installer payload failed signature check"` just before the installer finishes | use a different usb port OR you may need to fix your bios time
Audio | various audio issues | answered [here](https://www.reddit.com/r/hackintosh/comments/4sil5p/audio_mechanic_old_patchfix_removal_applealc/)


# Guides
- https://www.tonymacx86.com/threads/kaby-lake-hd620-working.209598/
- https://www.tonymacx86.com/threads/success-sierra-win10-i5-7500-ga-h270n-wifi-quadro-k1200-samsung-960pro-nvme-imac17-1.215787/
- https://www.tonymacx86.com/threads/new-testing-intel-7th-generation-kaby-lake-cpus-200-series-motherboards-in-macos.211743/
- [CorpNewt Guides](https://corpnewt.com/category/7/guides)
- [HDMI Audio](https://www.tonymacx86.com/threads/audio-hdmi-audio-applehda-guide.143760/)
  - Use [this](https://github.com/toleda/audio_hdmi_100series/blob/master/%5BGuide%5D_HD5x0-hdmi_audio_(clover_or_ssdt).pdf) which has links to the `config.plist` that contains patches.
  - You'll have to add 3 `ACPI` edits and some `Devices -> Arbitrary` patches.
  - Then you need to add this [SSDT](https://github.com/toleda/audio_hdmi_100series/blob/master/ssdt_hdmi_hd5x0/ssdt_hdmi-hd530.zip) to `EFI -> CLOVER -> ACPI -> patched`.

# Additional Guides
- Replace System Info image - `/Applications/Utilities/System Information.app/Contents/Resources/SystemLogo.tiff` (see [here](http://www.idownloadblog.com/2017/01/13/how-to-modify-about-this-mac-hackintosh/))
- [Making a copy of IOReg](https://www.tonymacx86.com/threads/guide-how-to-make-a-copy-of-ioreg.58368/)
