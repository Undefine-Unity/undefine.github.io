---
layout: post
title: "Complete guide to flashing Samsung phones"
date: 2023-07-12 18:46:00 +0200
categories: phones
---

# The complete guide to flashing Samsung phones
This going to be a complete guide to flashing Samsung phones, this guide uses Samsung Galaxy S9 as an example, but it *should* work on other phones. I'm writing this because there are a lot of weird things / security mechanisms that could kick in and if you are very unlucky it can cost you a few hours just trying to find a solution, so I wanted to cover all of the weird things that I have encountered along the way.

# Prerequisites
Before we start you will need a couple of thing:
- The phone, preferably fully charged
- A **Windows** PC, Linux and MacOS won't work because we need Odin which only has a Windows version (you *might* have some luck running Odin with wine on Linux or with Heimdall, but the guide focuses on Windows)
- A working USB cable

# Preparation
Before we start you will have to install a few tools on you PC:
- [Samsung USB drivers](https://developer.samsung.com/android-usb-driver)
- [Platform Tools aka. ADB](https://developer.android.com/tools/releases/platform-tools) (please download from the official source)
- [Odin](https://samsungodin.com/) (if you know what you're doing you can try using Heimdall, but I had no luck with it, it kept throwing errors) 

# Unlocking the bootloader
This is a quite simple process, but at least on the S9 it was different than on many other phones. If you're reading this you probably already have already enabled developer settings, but if you haven't go to the device info in the settings and just click a few times the build number until it shows that developer settings have been enabled. Now that you have the developer settings enabled, go inside them and check `Unlock OEM`. If you don't have that option go to [this section](#no-oem-unlock-option--kg-state-personal-or-locked). After checking that you will be prompted to factory reset the phone. **That is all you have to do.** You do not have to reboot to bootloader and use fastboot. After the factory reset the phone should be already unlocked.

# Don't try to use fastboot
"Just `adb reboot bootloader` and `fastboot flash ...`", not so fast. A lot of Samsung phones do not have fastboot support which makes the job way harder. We work around that later using Odin and `adb sideload` to flash later.

# Ensuring you can flash custom ROMs
*If you want to know why this necessary read [this](#no-oem-unlock-option--kg-state-personal-or-locked).* If you go to the developer settings and you see the `Unlock OEM` option and under it it shows `Bootloader already unlocked` this step might not be necessary for you, but doing it does not harm and makes sure everything is set up correctly. So we need to get to the Donwload Mode. You can get into it with `adb reboot bootloader`, but going into it without adb will give some information that we need for this step. So turn of your phone, and turn it back on while holding the power button, the bixby/home button and the volume down button. You should see a screen with a warning sign, press volume up to the go the download mode. If you did everything right, you should see `ODIN MODE` in red in the left top corner. You should see `OEM LOCK: OFF`, if it's on, you did not do the [bootloader unlocking step](#unlocking-the-bootloader). If you see a line starting with `KG State:` and its value is not `Checking` see [this](#no-oem-unlock-option--kg-state-personal-or-locked). If everything is correct you can continue to the next step.

# Flashing TWRP
Because it is a Samsung phone we are going to have to use Odin. Unfortunately you can't just flash any image with Odin as you would with fastboot so to make out job easier, we **have to install TWRP**. You will need to download the latest version of [TWRP](https://twrp.me/Devices/Samsung/), on this website choose your phone and then choose one of the primary download links, choose the latest version **that ends with .tar**. Once you downloader the file open up Odin and plug you phone if you haven't already done that. In the Odin log you should see added and above that you should see a new COM device show up. If no devices show up see [this](#device-not-detected-in-odin). Now press the AP button and choose the TWRP image you just downloaded. **Make sure Auto-Reboot is unchecked**, at least for me it didn't work if this was checked. Now just press `Start` and cross your fingers that it will work. Above you COM device in Odin it should show pass after everything is finished, if it shows fail and you see a red error about non official images on your phone you skipped the [previous step](#ensuring-you-can-flash-custom-roms) and have to go back. If it does show pass, congrats you have successfully flashed TWRP to your phone. Now if you reboot your phone and enter recovery (while powering up hold the power button, bixby/home button and volume up) you should be greeted with TWRP.

# Flashing a custom ROM
TODO: I haven't yet written this part of the guide, expect it to be done soon, for now google flashing with TWRP and every generic method should work. 

# Troubleshooting
## No OEM unlock option / KG State: Personal or Locked
Samsung has a really weird "security" mechanism which prevents you from flashing anything or unlocking your OEM for 7 days after first use / factory reset. You *can* just wait the 7 days, but let's be honest nobody wants to wait that long. Fortunately there's a really easy way to bypass this:
- Turn off wi-fi and eject you sim card
- Disable automatic date and set the date to a 1-2 months back
- Reboot your phone
- Re-enable the wi-fi and/or insert your sim card
- Turn on the automatic date again
- Reboot your phone once again
Now if you go to developer settings the OEM unlock option should be back. If it doesn't appear close the settings app and try again.

Credit: https://forum.xda-developers.com/t/how-to-bypass-kg-state-and-or-rmm-state-prenormal.3891193/post-85552805

## Device not detected in Odin
That might happen if you don't have the correct driver installed for you device in the Download Mode (that might happen even if you installed the Samsung USB drivers). You will have to find the device in the device manager. There's a high chance you will see it as a serial device with the name `Gadget Serial`. Right click it a choose properties, then click update driver and choose `Samsung CDC...`. This option should be available if you have installed the USB drivers.
