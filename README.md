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

Hardware compatibility
---

**What works:**

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
- SideCar over USB and wireless
- iMessage and iCloud
- AirPlay
- USB-C functionality on the TB3 ports
- HDMI/DP via USB-C
- Internal Webcam

**What does not work/disabled:**
- Thunderbolt 3

**Not tested:**
- Virtualization (VT-x)
- Apple Watch Unlock 

**Things to fix**
- High Temps/CPU Power Hog (ACPI gpe6F to blame)
- Combo Jack

Monterey
----
Please use the EFI inside the `Monterey` folder for macOS Monterey

Everything that worked in macOS Big Sur seems to work perfectly fine here.

CFG-Lock
----
[You need to disable CFG-Lock in order to boot this EFI!](https://dortania.github.io/OpenCore-Post-Install/misc/msr-lock.html#disabling-cfg-lock)

`setup_var 0x3e 0x0` (for BIOS 1.9.0)

CPU
-----
The [i7-10710U](https://ark.intel.com/content/www/us/en/ark/products/196448/intel-core-i710710u-processor-12m-cache-up-to-4-70-ghz.html) needs `Cpuid1Data` and `Cpuid1Mask` patches in OpenCore to prevent very early kernel panics. Power managment works as expected after using [CPUFriend](https://github.com/acidanthera/CPUFriend) and [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), with a base clock of 1.1 GHz and can boost all the way up to 4.7 GHz (idles at 800 MHz).

<img src="https://i.imgur.com/K2Ni540.png" width="410">


GPU/Display
-----
Getting the Intel UHD Graphics 630 took quite a bit of effort to work properly, It uses the natively supported `device-id` `ig-platform-id`, but required framebuffer patches in order for the internal display and HDMI over USB-C to function correctly.

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
Wi-Fi and and Bluetooth work out of the box provided you use `AirportItlwm.kext`, `IntelBluetoothFirmware.kext`, and `IntelBluetoothInjector.kext`

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
Install `CodecCommander.kext` into `/OC/Kexts/`, then copy `hda-verb` to `/usr/local/bin` (Create the folder if it does not exist)
<img src="https://i.imgur.com/xDZzmB3.png" width="700">
<img src="https://i.imgur.com/40KWQdo.png" width="700">

Combo Jack currently does not work (will be fixed sooner or later)

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
Not tested

Battery
-----
Battery Capacity works when using proper SSDT hot-patching and `SMCBatteryManager.kext`
<img src="https://i.imgur.com/ZITbTK2.png" width="700">
<img src="https://i.imgur.com/KEqblVy.png" width="300">

There is some drain compared to Windows/Linux, I have not found the cause but the CPU Package Power is always at 2W with 0% CPU Usage, resulting in much worse battery-life.
You can save some power by undervolting with [VoltageShift](https://github.com/sicreative/VoltageShift)


I use [Power Manager](https://www.dssw.co.uk/powermanager/) to apply these offsets on boot and wake from sleep (these offsets may not work for every configuration, take caution and ymmv.)

`voltageshift offset -100 -100 -100 -100 -100 -100 -100`

`voltageshift powerlimit 10 15`

Special Thanks
-----
- [OSXLatitude](https://osxlatitude.com/)
- [Baio77 @ osxlatitude.com](https://osxlatitude.com/)
- [Jake Lo @ osxlatitude.com](https://osxlatitude.com/)
- [red-green](https://github.com/red-green)
- Apple for macOS

License
-----

Work in this repo, including previous commits (SSDTs, config files, etc) is distributed under a [Creative Commons Attribution-NonCommercial-ShareAlike](https://creativecommons.org/licenses/by-nc-sa/3.0/) (CC BY-NC-SA) 3.0 License. All others' work (clover, kexts, drivers) retains the original license it was distributed with.

This readme is based on [red-green's Blade Stealth hackintosh GitHub repo](https://github.com/red-green/razer_blade_stealth_hackintosh)

