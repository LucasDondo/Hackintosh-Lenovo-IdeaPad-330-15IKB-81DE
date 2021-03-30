# Everything (everything!? Well, no.) you need to know is right here!
However, I strongly suggest to use this EFI folder as a mere guide, and not as an easy-peasy-and-already-made-with-a-lot-of-love installation medium. No one should rely on this files. What I do suggest is to follow the [official OpenCore guides](https://dortania.github.io/getting-started/), and maybe then use this EFI folder as a guide for troubleshooting, but **you need to understand what you are doing**.
Everyone tells you to understand what you are doing when dealing with this kind of software, but many times we just go for it without thinking much. From my personal experience, there is no way to be successful in creating a hackintosh if you don't understand the values, files and problems you are dealing with.

Now, a bit of positivity: I got most stuff to work and, for my use case, this is a quite reliable machine with the awesome mac operating system! It is a nice experience and you deserve it, so go for it! And, if you find a way to make this EFI even better, just [create and issue](https://github.com/LucasDondo/Hackintosh-Lenovo-IdeaPad-330-15IKB-81DE/issues/new/choose) or a [pull request](https://github.com/LucasDondo/Hackintosh-Lenovo-IdeaPad-330-15IKB-81DE/compare), whatever suits the best!

![Booyah!](Resources/Booyah!.gif)

# Hardware specifications

**Laptop**: Lenovo IdeaPad 330-15IKB 81DE

**CPU**: Intel(R) Core(TM) i3-8130U CPU @ 2.20GHz (Kaby Lake Refresh)

**GPU**: Intel Corporation UHD Graphics 620

**HDD**: Seagate ST1000LM035-1RK1

	**Version**: LCM2

	**Serial**: WKP0ZZA9

**Ethernet chipset**: Realtek Semiconductor Co., Ltd. RTL8111/8168/8411 PCI Express Gigabit Ethernet Controller

**WLAN/Bluetooth chipset**: Intel Corporation Dual Band Wireless-AC 3165 Plus Bluetooth

**Motherboard**: Lenovo LNVNB161216

**Audio codec**: ALC3240, also known as ALC230, as mentioned [here](https://www.reddit.com/r/hackintosh/comments/hzjb44/codec_finder/).

**Touchpad**:

	**Vendor**: got from nano `/proc/bus/input/devices` is "Vendor=044e", so it is [ALPS](https://devicehunt.com/view/type/usb/vendor/044E).

	**Type**: USB, I2C

	**Model**: AUI1667:00 044E:121E

**Keyboard**: From the Linux command line instructions, it seemed to be the same as the touchpad (I2C), but AIDA64 told me it is **PS/2**, not I2C. That's why it works with VoodooPS2Controller.kext.

# Problems, and solutions!

## GPU and display

For DRM, we don't have support (check [this](https://dortania.github.io/OpenCore-Post-Install/universal/drm.html#fixing-drm)) to use with hardware, so for Netflix we need to use Google Chrome, which uses software-powered DRM.

When rebooting or waking the laptop while connected to an HDMI screen, we need to re-plug the HDMI cable or turn the external monitor off and on again. If you happen to find a solution, please create a pull request.

Do not use 00001B59 as AAPL,ig-platform-id since with that not even keyboard or mouse work.

To solve the pink screen on HDMI devices, set this ([the guide I followed](https://dortania.github.io/OpenCore-Post-Install/gpu-patching/intel-patching/connector.html)):

![Pink screen on HDMI - Solution](Resources/"Pink screen on HDMI - Solution.png")

## Networks

[Here](https://dortania.github.io/OpenCore-Install-Guide/ktext.html#ethernet) it says that by using RealtekRTL8111 v2.3.0 the kext may not work and even though I installed v2.4.0, it did not work. v2.2.2 did the work.

WiFi will just work with [itlwm](https://github.com/OpenIntelWireless/itlwm) (and [SecureBootModel](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/kaby-lake.html#misc) has to be in "Default", because using "Disabled" [this](https://github.com/OpenIntelWireless/itlwm/issues/301) happens). Bluetooth does not work with [this](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) kext, but it does need IntelBluetoothInjector (same link as before) to turn it on and off.

## Sleep

If when it goes to sleep, it continuously uses hardware to do tasks (partial wake, sleep, partial wake, sleep, and so on), do [this](https://dortania.github.io/OpenCore-Post-Install/usb/misc/instant-wake.html). That partial wake is called darkwake.

Another issue with sleep is that it requires two keypresses to wake completely. To solve this, I followed [this](https://dortania.github.io/OpenCore-Post-Install/usb/misc/keyboard.html) guide. The [third method](https://dortania.github.io/OpenCore-Post-Install/usb/misc/keyboard.html#method-3-configuring-darkwake) worked, using darkwake=0.

## Trackpad

There is no way to make it work since it is both I2C and ALPS. See [the issue I created in GitHub](https://github.com/VoodooI2C/VoodooI2C/issues/358#event-3638746641).

Since there is no way to make it work, we won't use SSDT-GPI0.aml (it's useless in our case).

# Notes

**Guide followed**: [Dortania's OpenCore](http://dortania.github.io).

I disabled SIP as said [here](https://dortania.github.io/OpenCore-Install-Guide/troubleshooting/extended/post-issues.html#disabling-sip).

To multiboot in the same drive with multiple OSs:

Note that this worked for me, that I had already installed Linux and Windows 10 in my machine. Linux boots into Grub Rescue, but I believe this is because of a error I committed during my first try installing macOS.

1. [Install macOS](https://dortania.github.io/OpenCore-Install-Guide/).
2. Mount your internal drive and USB's EFI partitions.
3. [Set up LauncherOption](https://dortania.github.io/OpenCore-Post-Install/multiboot/bootstrap.html) in your USB (this has already be done for you in this EFI).
4. From the internal drive, move everything to your desktop (even better if you back it up in a safer place, such as a server or external drive).
5. Move everything from the USB to the drive's EFI.
6. Disconnect the USB and reboot.
7. Move back from your desktop the folder used to boot other OSs (for example *Microsoft* or *ubuntu*).
8. You can now, happily, boot into other OSs as well as on your brand new macOS.
