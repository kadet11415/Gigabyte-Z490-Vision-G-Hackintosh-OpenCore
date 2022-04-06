# Gigabyte Z490 Vision G Intel I225-V Fix for macOS 12+
 
> **Disclaimer**: This fix requires flashing a custom EEPROM onto the Intel I225-V controller. I am not responsible for any hardware failures that might occur during the process – execute the following guide at your own risk!

## About
On the Z490 Vision G (and other Gigabyte boards potentielly), the Intel I225-V 2.5 Gbit Ethernet Controller stopped working soon after the first betas of macOS Monterey were released. A lot of things were tried to fix it: assigning manual network addresses or replacing network drivers of previously working builds, overwriting newer ones, which requires a special tool and could corrupt macOS, leaving it in an unbootable state.

Until now, the only reliable way to solution was to just buy a third party network card supported by macOS 12.

Fortunately, a new method to get the I225 working again was discovered. But it requires flashing a modified EEPROM on it…

## Preparations

- **BIOS**: enable `VT-d` if it isn't already
- Save and reboot into macOS
- Open **Network Settings**: set Ethernet > IPv4 to `DHCP` and Advanced… > Hardware > Configuration to `Automatic`.
- **OpenCore**:
	- Mount EFI
	- Add `OpenShell` to OC/Tools and `config.plist`
	- Disable/remove `DeviceProperties` for `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` and boot-arg `dk.e1000=0`  for the I225-V from `config.plist` 
	- Unselect Kernel > Quirks > `DisableIOMapper` if activated
	- Drop `DMAR`table (optional)
	- Save your `config.plist`
- Download `I225-Vmod.zip` from [here](https://www.hackintosh-forum.de/forum/thread/56123-l%C3%B6sung-f%C3%BCr-i225-v-v2-problem-auf-z490-plattform-vornehmlich-gigabyte-boards-unte/) and extract it
- Copy `eeupdate64.efi` and `I225MOD` from the to the root folder of a FAT32 formatted USB Flash Drive.
- Restart the system

## Flashing the I225-V EEPROM via OpenShell	
- From the OpenCore GUI, select `OpenShell`
- Type `fs0:` and hit `Enter` to change the workung drive (`fs:0` is most likely your USB flash drive)
- Type `ls` to list the content of the drive. In this cas `ls0` is correct drive letter:</br>![06143142](https://user-images.githubusercontent.com/76865553/162021483-39a7d188-5b96-4607-a1cd-a550dd1560d5.png)
- Next, run eeupdate64e.efi in gui mode. Type `eeupdate64e /gui` and hit `Enter`
- In the next screen, select the Intel I225 Controler and hit `Enter`:</br>![06143155](https://user-images.githubusercontent.com/76865553/162020889-a98abf45-6f58-4c96-a7d3-ffb743895b16.png)
- In the next screen, select "Raw EEPROM - Extended":</br>![06143203](https://user-images.githubusercontent.com/76865553/162020929-65ff5300-0838-4b6f-a26c-2401274b6b10.png)
- Next, press `F3` to dump the original EEPROM to your Flash Drive:</br>![06143217_01](https://user-images.githubusercontent.com/76865553/162021033-ec75129f-4f4b-48f6-8403-2fc37f75446d.png)
- Enter a name for the backup and confirm it with "OK"
- Next, press `F4` to load the new EEPROM:</br>![06143217_02](https://user-images.githubusercontent.com/76865553/162021068-d4102c40-94e8-42f5-bc83-85605019ae0c.png)
- Now type `I225MOD`, press `ENTER` and confirm loading the file. :warning: Keep the original MAC Address when importing the EEPROM (you will be asked).
-  Press `ESC` to exit and confirm saving.
-  Shutdown the system
-  Boot into macOS - you may need to remove and add the network adapter again in preferences.

In my case, the I225 worked immediately

## Credits and Resources
[**Original Guide**](https://www.hackintosh-forum.de/forum/thread/56123-l%C3%B6sung-f%C3%BCr-i225-v-v2-problem-auf-z490-plattform-vornehmlich-gigabyte-boards-unte/) by badbrain. Translated from german and modified by me.