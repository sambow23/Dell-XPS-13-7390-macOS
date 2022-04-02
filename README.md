Dell XPS 13 7390
---
<img src="https://i.imgur.com/R0RSyT7.jpg" width="1000">



Specs:
---

- **CPU** : Intel Core i7-10710U 6C 12T 1.10GHz (4.70GHz turbo)
- **RAM** : 8GB dual-channel 2133MHz LPDDR3
- **GPU** : Intel UHD 630
- **Storage** : WDC PC SN520 256GB NVMe M.2
- **Screen** : 13.3" 1920x1080
- **WiFi** : Intel AX200 (Soldered)
- **Soundcard** : Realtek ALC299
- **Battery** : 52WHr

## Hardware compatibility

<details><summary>What works</summary>

- CPU power management
- Readng CPU temperature
- GPU acceleration and video codecs
- SSD
- Wireless (Bluetooth and Wi-Fi) (itlwm)
- Bluetooth Audio
- Sleep, lid sleep and lid wake
- Trackpad including gestures
- Internal Speakers
- Internal microphone
- All USB ports
- Screen full resolution, brightness
- Battery precentage, charging
- iMessage and iCloud
- AirPlay
- USB-C functionality on the TB3 ports
- HDMI/DP via USB-C
- Internal Webcam
- Virtualization (VT-x)
- SideCar over USB
  
 </details>

<details><summary>What does not work/disabled</summary>
  
- Thunderbolt 3
- Apple Watch Unlock
- SideCar over wireless
  
</details>

<details><summary>Things to fix</summary>
  
- Combo Jack / AppleALC high `kernel_task` CPU usage
- `ACPI interrupt gpe6F` needs to be disabled somehow
  
<img src="https://i.imgur.com/8U6gjAX.png" width="300">

</details>

EFI Folders
----
| Version                   | Description                      
| --------------------------| --------------------------|
| `Big Sur`                 | Is mostly deprecated and will not be provided any support.
| `Monterey`                | Stable and up-to-date version of this EFI, provides a decent out-of-the-box experience.
| `Monterey - Experimental` | Mostly for testing, should not be used as the daily driver. But can provide fixes for issues that may be in the stable build.


CFG-Lock
----
[You need to disable CFG-Lock in order to boot this EFI!](https://github.com/dreamwhite/bios-extraction-guide/tree/master/Dell)

[Use this specific version of `grub_setup_var`](https://github.com/XDleader555/grub_setup_var/releases/tag/v1.0-alpha)


| BIOS              | CFG Lock                      | Overclock Lock
| ----------------- | ------------------------------| -----------------------------
| `1.9.0`, `1.12.1`, `1.13.0`     | `setup_var CpuSetup 0x3E 0x0` | `setup_var CpuSetup 0xDA 0x0`



CPU
-----
The [i7-10710U](https://ark.intel.com/content/www/us/en/ark/products/196448/intel-core-i710710u-processor-12m-cache-up-to-4-70-ghz.html) needs `Cpuid1Data` and `Cpuid1Mask` patches in OpenCore to prevent very early kernel panics. Power managment works as expected after using [CPUFriend](https://github.com/acidanthera/CPUFriend) and [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), with a base clock of 1.1 GHz and can boost all the way up to 4.7 GHz (idles at 800 MHz).

<img src="https://i.imgur.com/K2Ni540.png" width="410">

It is recommended to cap the TDP to 10W with `VoltageShift` as it tends to boost more than it should during idle loads, can help with temperatures and battery life.

`voltageshift powerlimit 10 10` 

GPU/Display
-----
Getting the Intel UHD Graphics 630 took quite a bit of effort to work properly, It uses the natively supported `device-id` `ig-platform-id`, but required framebuffer patches in order for the internal display and HDMI over USB-C to function correctly.
The 4K version of this laptop should work fine without further patches.

The internal display brightness controls worked out of box when using `SSDT-PNLF-CFL`.

<img src="https://i.imgur.com/xXrewwg.png" width="700">

SSD
-----
The `WDC PC SN520` NVME SSD worked out of the box without having to reformat with 4K sectors.
Power management is working with `NVMeFix.kext` 

<img src="https://i.imgur.com/Su6ylau.png" width="700">

Wifi and Bluetooth
-----
Sadly the Intel AX200 is soldered to the motherboard, but does work thanks to the work of [OpenIntelWireless](https://github.com/OpenIntelWireless/itlwm),
Wi-Fi and and Bluetooth work out of the box provided you use `AirportItlwm.kext`, `IntelBluetoothFirmware.kext`, and `BlueToolFixup.kext`

<img src="https://i.imgur.com/RVzkHnQ.png" width="700">
<img src="https://i.imgur.com/WKdEeq8.png" width="410">

Sleep
-----
Sleep works out of the box after mapping USB ports with `USBMap.kext`, disabling any unused ports/controllers.

Trackpad
-----
Using `VoodooI2C.kext` and `VoodooI2CHID.kext`, the Trackpad works out of the box, including all gestures.
<img src="https://i.imgur.com/X36iSI7.png" width="700">


Sound
-----
Sound works for the most part, but needs [CodecCommander](https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/RehabMan-CodecCommander-2018-1003.zip)
Install `CodecCommander.kext` into `/OC/Kexts/` (if it does not already exist), then copy `hda-verb` to `/usr/local/bin` (Create the folder if it does not exist)
<img src="https://i.imgur.com/xDZzmB3.png" width="700">
<img src="https://i.imgur.com/40KWQdo.png" width="700">

Combo Jack currently does not work (will be fixed sooner or later)

AppleALC might cause high `kernel_task` CPU usage for some people, it is under investigation. If you are encountering this issue, please disable AppleALC in your OpenCore config. 

USB
-----
USB works out of the box

<img src="https://i.imgur.com/k6cpFhS.png" width="700">

Display Outs
-----
USB-C to HDMI/DP works out of the box once GPU is properly patched
<img src="https://i.imgur.com/H1rUxtb.png" width="700">

Thunderbolt
-----
Currently does not work, the controller is most likely causing severe idle power drain but should be disabled as of [27a4fcb](https://github.com/sambow23/Dell-XPS-13-7390-macOS/commit/27a4fcb40c00245ec281c9767fa172c81877af1b)

Battery
-----
Battery Capacity works when using proper SSDT hot-patching and `SMCBatteryManager.kext`
<img src="https://i.imgur.com/ZITbTK2.png" width="700">
<img src="https://i.imgur.com/KEqblVy.png" width="300">

~~The battery drain should be fixed as of commit [a6340b0](https://github.com/sambow23/Dell-XPS-13-7390-macOS/commit/a6340b06d363af5b0818fd45c757564282bfdf6a)~~

There is some drain compared to Windows/Linux, A non-working ACPI function GPE_L6F is eating CPU cycles for no reason, it can be easily fixed on linux with a one-line command
`echo "disable" > /sys/firmware/acpi/interrupts/gpe6F`. It seems to be much more complicated on macOS, I tried using a ACPI hotpatch to rename `_L6F` to `XXXX` but it did not seem to have any effect on power usage.

A decent workaround for now is to try undervolting with [VoltageShift](https://github.com/sicreative/VoltageShift)

I use [Power Manager](https://www.dssw.co.uk/powermanager/) to apply these offsets on boot and wake from sleep (these offsets may not work for every configuration, take caution and ymmv.)

`voltageshift offset -100 -100 -100 -100 -100 -100 -100`


Special Thanks
-----
- [OSXLatitude](https://osxlatitude.com/)
- [Baio77 @ osxlatitude.com](https://osxlatitude.com/)
- [Jake Lo @ osxlatitude.com](https://osxlatitude.com/)
- [red-green](https://github.com/red-green)
- [Hervé @ osxlatitude.com](https://osxlatitude.com/)
- Apple for macOS

License
-----

Work in this repo, including previous commits (SSDTs, config files, etc) is distributed under a [Creative Commons Attribution-NonCommercial-ShareAlike](https://creativecommons.org/licenses/by-nc-sa/3.0/) (CC BY-NC-SA) 3.0 License. All others' work (clover, kexts, drivers) retains the original license it was distributed with.

This readme is based on [red-green's Blade Stealth hackintosh GitHub repo](https://github.com/red-green/razer_blade_stealth_hackintosh)

