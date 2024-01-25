# Legion Go Tricks

This document serves to provide information, workarounds, and tricks to improving day-to-day use of Linux on the Legion Go.

Note that while a lot of things are working, Linux support for this device is very much a work in progress, developers are working on improving the experience.

- [Current Status of Linux on the LGO](#current-status-of-linux-on-the-lenovo-legion-go)
- [What Works?](#what-works)
- [What has Workarounds?](#what-has-workarounds)
- [What has issues?](#what-has-issues)
- [Known Bugs](#known-bugs)
- [User Reported Bugs (needs verification)](#user-reported-bugs-needs-verification)
- [Which Linux Distro Should I install?](#which-linux-distro-should-i-install)
- [Resources](#resources)
- [CSS Loader Themes](#css-loader-plugin---themes)
- [Tutorial Videos](#tutorial-videos)
- [Guides + Small Fixes](#guides--small-fixes)
  - [NobaraOS](#nobaraos-guides)
  - [Bazzite Deck Edition](#bazzite-deck-edition-guides)
- [Emulator Info](#emulator-info)
- [TDP Control overview](#tdp-control)
- [Controller Support overview](#controller-support)
- [Quality of Life Fixes overview](#quality-of-life-fixes)
- [Resolved/won't fix bugs](#resolved-or-wont-fix-bugs-changelog-for-documentation-purposes)
- [3D prints](#3d-prints)

# Current Status of Linux on the Lenovo Legion Go

ChimeraOS testing branch, Nobara Deck Edition, and Bazzite Deck Edition, all have a bunch of fixes for the LGO. It's mostly working, but still has bugs that need to be addressed.

However, that being said, Linux is good enough to be a daily driver on the Legion Go.

- Using a PS5 Dualsense Edge Controller Emulator, you get access to the entire LGO controller (including gyro) via steam input
  - the entire controller works detached too
    - however gyro hardware is in the main body with the screen, so it won't really work detached
- TDP control can be done either via Decky Plugin or steam-patch
- RGB control works via Decky Plugin or Steam Input + Dualsense emulation
- suspend-resume works
- all standard hardware (wifi, bluetooth, sound, etc) works

Some of the things you find in this document may be unofficial changes to original software

Read further below for more details

## What Works?

At the moment, the following functions work out of the box

- Screen orientation (fixed in NobaraOS Deck Edition, ChimeraOS 45 unstable, Bazzite OS)
- suspend-resume functionality
  - suspend quirk: sound often is fuzzy on resume, usually clears up after 30 seconds or so.
    - sometimes using the [Pause Games plugin](https://github.com/popsUlfr/SDH-PauseGames) with `Pause on Suspend` enabled can help with this issue
- Wifi and Bluetooth
- Sound
- Controllers, both attached and detached
  - note, controllers work best in X-input mode. see [official Legion Go Userguide PDF](./legion_go_user_guide_en.pdf) to read more about controller modes
  - ChimeraOS, NobaraOS, BazziteOS all ship OOTB with basic controller support
  - some non-gaming distros don't include the udev rule for the controller, you can manually add it with [this script](./add-lgo-xpad-rule.sh)
- FPS/Mouse mode
- scroll wheel on controller works fine, scroll wheel press doesn't do anything. However, holding the scroll wheel for 5s will toggle the scroll wheel on/off
- trackpad works, but cannot tap-to-click in game mode.
  - Can tap to click on desktop mode, but must be enabled in the touchpad settings.
  - Can be used in steam input with a workaround.

## What Has Workarounds?

These functions are not working out of the box, but have workarounds

- Steam/QAM Buttons/Rear back buttons - all buttons can be used in Steam via Dualsense Edge Virtual/Emulated Controller [Video demo here](https://www.youtube.com/watch?v=uMiXNKES2LM).
- Gyro - uses the same fix as buttons fix
  - Gyro performance is best with hhd Dualsense Edge Emulator
- Trackpad - this hardware previously already worked, but was not usable in steam input.
  - With the latest version of the PS5 Dualsense edge emulators, it is now usable in steam input. [Video Demo here](https://www.youtube.com/watch?v=RuSboPkZob4)
- TDP - requires using either steam-patch or decky plugins
- Controller RGB Lights - requires decky plugin or HHD (HHD enables steam input RGB support) See [Video Demo here](https://youtu.be/HHubJ8AnkUk?si=oWLVultDKBMVOxlo&t=35)
- GPU Frequency control - via SimpleDeckyTDP plugin or steam-patch
- Custom Fan Curves - via LegionGoRemapper plugin with [experimental feature enabled](https://github.com/aarron-lee/LegionGoRemapper/#custom-fan-curves)
  - fan curves confirmed to work with bios v29
- Games can sometimes default to 800p, you will need to manually change the resolution per game in the `Steam Settings > Properties > Game Resolution` to either `Native` or other higher resolutions.
- v28 bios - STAMP mode is bugged on both Windows and Linux when setting high TDPs with 3rd party tools like ryzenadj and handheld companion
  - users reported that they were getting hard crashes at 30W TDP on both Windows and Linux
  - **Solution**: on STAMP mode, TDP must be set with a fan curve that will prevent thermal shutdown.
    - The best way to do so, currently, is via SimpleDeckyTDP with the [custom LGO TDP enabled](https://github.com/aarron-lee/SimpleDeckyTDP/tree/main/py_modules/devices#experimental-custom-tdp-method-for-the-legion-go).
    - Setting TDP this way will also set fan curves appropriately.
    - steam-patch should similarly work on the LGO
- Screen Refresh Rate - only refresh rates that work are 60Hz and 144Hz, everything else is not usable/has issues.
  - 144Hz additionally has bugs, but can be resolved
    - Instructions to do so for NobaraOS v39 can be found [here](#fix-60hz-and-144hz-only-for-nobaraos-v39)
    - Bazzite (testing branch) should be working with minimal changes
      - Bazzite will move their LGO fixes to their main branch very soon, probably around Jan 19th 2024
- adaptive/auto display brightness doesn't work yet
  - manual brightness slider in steam UI works without issues
  - there's work in progress from devs for to get this fully working

## What has issues

- **v29 bios - Update** seems to work so far on nobara v39, and also tested latest Bazzite. Fan curves, Auto VRAM, and battery indicator seems to be working or fixed. But more testing required.
- **IMPORTANT BUG:** This still requires confirmation, but it looks like you cannot set custom fan curves and use Lenovo's custom TDP mode for TDP control simultaneously
  - if you enable a custom fan curve, **make sure to disable Lenovo Custom TDP** if you are using the SimpleDeckyTDP plugin for TDP control
- Warning: there's user reports that there's screen flashing on bios v29.
- There's also been multiple confirmation that fan curves and battery indicator seem to be resolved and working

- Battery Indicator - It doesn't consistently work, but has a usable workaround
- Battery Estimated Time Remaining - It's often incorrect
- Adaptive Brightness sensor - hardware is detectedby the OS, but not used for auto-brightness yet
  - there's dev work in progress for auto-brightness

### Known bugs

- Occasionally the steam menus and mangohud performance overlay will flash white
  - mostly likely related to display refresh rate, but currently there's no known fix
- BazziteOS - after fresh install, sometimes you encounter a blank screen on reboot
  - permanent fix found [here](#blank-screen-on-first-reboot)
- User bug reports that 60Hz occasionally forces a 30fps cap on 60Hz
  - 144Hz is completely functional, you can use it to set a 72 or 36 fps cap if you want something lower than 144hz
  - if anybody can replicate, please report it
    - user reports:
      - Games that did not have the issue:
        - Diablo II: Resurrected
        - Bomb Rush Cyberfunk (Heroic Launcher)
        - Cyberpunk 2077
        - No Man Sky
        - Tales of Arise
        - Spiderman Remastered
      - Games tested with the 60hz problem:
        - "Fallout: New Vegas"
        - xemu emulator.
        - Portal 2 (Native Linux game)
        - Pizza Tower
      - Games that exhibited the issue for a short time, then never again:
        - Tony Hawk Pro Skater 1+2
- suspend-resume quirk: sound often is fuzzy on resume, usually clears up after 30 seconds or so, but not all the time.
  - sometimes using the [Pause Games plugin](https://github.com/popsUlfr/SDH-PauseGames) with `Pause on Suspend` enabled can help with this issue
- HHD (Dualsense Emulator) - It should now hide the Xbox controller in steam input, and only show the Dualsense Edge.
  - If you see an extra Xbox controller in steam input, you can flip the fps-mode switch on and off for to make it disappear. You can leave the controllers attached when you do this.
  - This should also fix any issues where emulators don't recognize the controller, since the emulator was latching onto the Xbox controller
    - if you still have a controller issue, reorder the controller from player 2 to player 1 in the QAM. sometimes it registers as player 2 even when no other controller is attached
- HHD PS5 Controller Emulator bug
  - If you hold an LGO joystick input while booting or resuming from suspend, the input may get stuck in whatever direction you were pointing
  - workaround: don't press anything for a few seconds, let the device register itself
- (Nobara) Fuzzy screen issue - this happens when an invalid refresh rate is used for your game. You can use the refresh rate slider in steam UI to revert back to either 60Hz or 144Hz
- controller is more buggy in desktop mode for desktop-related usage, should still work fine for gaming
  - nested desktop still seems fine
- nobara v39 - ryzenadj may not be working for TDP control
  - use custom Lenovo TDP mode via Decky plugin instead: [see here](https://github.com/aarron-lee/SimpleDeckyTDP/tree/main/py_modules/devices#experimental-custom-tdp-method-for-the-legion-go)
- user reports say wifi has lower download speeds on Linux vs Windows
- reports of handycon reactivating by itself, which interferes with hhd
  - you can disable it again with `sudo systemctl disable --now handycon.service`
    - you can also opt to remove it, the command should be `sudo dnf remove handygccs` (untested)

### User-reported bugs (needs verification)

- certain Decky plugins require `deck` as your username
  - deck username fixes Animation changer plugin and mangopeel plugins
- nobaraOS v39
  - game mode global FSR is not working

# Which Linux Distro should I Install?

If you want a SteamOS-like experience, there are 3 distros I would recommend

1. BazziteOS Deck Edition
2. ChimeraOS
3. Nobara Deck Edition

As for which one you should install, here's a breakdown of the benefits and drawbacks of each.

## BazziteOS Deck Edition

**Pros**

- Highly recommended for more casual users who don't want to tinker much
- Has the best out-of-box experience on the Legion Go
  - Tools such as Decky, Emudeck, HHD (for Controller Emulation), etc, are either pre-installed, or have an easy install process
  - Excellent support from the Bazzite Devs and community
    - Bazzite Discord is the place to go to for support and discussion
  - Very quick to fix issues and provide OS updates
- Read-only root filesystem helps with providing better security, more stability, and overall a very good stable console-like experience
- Can configure Secure Boot, which allows for disk encryption and other security benefits
  - Secure boot requires some additional configuration steps
- Has both a Gnome and KDE Desktop mode option
- supports distrobox for more flexibility in software install options

**Cons**

- Due to it's read-only root OS, it's harder to do more comprehensive tinkering
  - e.g. running a custom Linux kernel, etc

## ChimeraOS

**Pros**

- Aims to be a very streamlined console-like experience, doesn't include lots of extra software, etc
  - very minimalist, very stable
- Very easy to pin your device to a stable OS version
  - so once you get a good working setup, you can lock your OS version and then opt to manually update whenever you want
  - great if you want a console like experience where you can "set-it-up-and-forget-it"
- Has a read-only root filesystem, but can also be fully unlocked if necessary
  - note: filesystem unlock does not survive OS updates because it re-locks after an OS update.
- Excellent support for a variety of handhelds besides the Legion Go
- Good Dev and community support on their Discord
- Has it's own implementation of Emulator support, etc
- supports distrobox for more flexibility in software install options

**Cons**

- Legion Go Fixes will require v45 stable to release. Right now v45 stable is still being tested on their testing branch
- Installing some recommended tools, such as HHD for Dualsense Controller Emulation, requires unlocking the root filesystem
  - this means any changes you make will be wiped every OS update (OS update re-locks the system), and must be reinstalled after updating
- ChimeraOS's emulation implementation interferes with Emudeck, you'll need to manually disable the ChimeraOS implementation
- Only desktop option is Gnome, so anyone that prefers KDE will have to look elsewhere

## Nobara Deck Edition

**Pros**

- Recommended for those more familiar with Linux, and don't mind troubleshooting a lot or tinkering
- Nobara is the most similar to a standard Linux distro, and does not have a read-only root filesystem
- This provides the most flexibility for running custom kernels, modifying system files, etc
- Can setup most workarounds and tools for a good experience on the Legion Go

**Cons**

- Nobara tends to run cutting edge brand new kernels, and makes other frequent changes to the OS
  - This often leads to updates introducing bugs or breaking features on the Legion Go
- Due to no read-only root FS, easier to accidentally mess up your device and put it into a borked state
- Nobara is run by one dev, GloriousEggroll (same guy behind GE-Proton)
  - While GloriousEggroll does excellent work, Nobara is understaffed and it will sometimes be difficult to get help or support if you run into problems
- Only Desktop is KDE, so if you prefer Gnome, you'll have to look elsewhere or manually install + manage it.

# Resources

HHD - PS5 Dualsense Edge Emulator - https://github.com/hhd-dev/hhd

- has a Decky plugin available for changing hhd settings: https://github.com/hhd-dev/hhd-decky

RGB Decky Plugin - https://github.com/aarron-lee/LegionGoRemapper/

Simple Decky TDP Plugin - https://github.com/aarron-lee/SimpleDeckyTDP

Script that monitors CPU temps and blasts fans when temps are too high - see guide [here](#setup-monitor-script-that-blasts-fans-when-cpu-temps-climb-too-high-tested-on-nobaraos-only)

steam-patch (for TDP control, some steam glyphs, etc) - https://github.com/corando98/steam-patch

reverse engineering docs - https://github.com/antheas/hwinfo/tree/master/devices

rogue-enemy - PS5 Dualsense Edge Emulator - https://github.com/corando98/ROGueENEMY/

powerbutton fix when using rogue-enemy - https://github.com/aarron-lee/steam-powerbuttond

Pipewire sound EQ improvement files - https://github.com/matte-schwartz/device-quirks/tree/legion-go/rog-ally-audio-fixes/usr/share/device-quirks/scripts/lenovo/legion-go

(ChimeraOS only) Legion Go installer tool - https://github.com/linuxgamingcentral/legion-go-tools-for-linux

gyro increase sampling rate fix (advanced users only, not maintained) - https://github.com/antheas/llg_sfh

## CSS Loader Plugin - Themes

- note, requires `CSS Loader` Decky Plugin
- manually install by downloading the theme + placing in `$HOME/homebrew/themes/` folder
- these themes may require a reboot for them to work

Legion Go Theme - https://github.com/frazse/SBP-Legion-Go-Theme

PS5 to Xbox Controller Glyph Theme - https://github.com/frazse/PS5-to-Xbox-glyphs

- If you'd like to manually edit mappings, you can find glyphs at `$HOME/.local/share/Steam/controller_base/images/api/dark/`
  - manual mapping can be done by editing the css file with the svg/png paths you want

```
# quick install, CSS Loader Decky Plugin must already be installed and enabled

# Legion Go Theme Install
cd $HOME/homebrew/themes && git clone https://github.com/frazse/SBP-Legion-Go-Theme.git

# PS5 to Xbox Controller Glyph Theme
cd $HOME/homebrew/themes && git clone https://github.com/frazse/PS5-to-Xbox-glyphs
```

# Tutorial Videos

Dual Boot Tutorial Video (Bazzite + Windows) : https://www.youtube.com/watch?v=3jFnkcVBI_A

- Partition guide for Dual boot with Bazzite: [see here](/bazzite-dualboot-partition-guide.md)

Dual Boot Tutorial Video (Nobara + Windows): https://www.youtube.com/watch?v=anc7hyPU6Lk

# Guides + small fixes

## NobaraOS Guides

### Fix 60Hz and 144Hz (Only for NobaraOS v39)

Massive thanks to all the devs who helped diagnose, troubleshoot, and and investigate this issue.

Install Instructions:

1. update NobaraOS from the desktop mode via the `update system` app. then, after rebooting, run the [enable_60_144hz.sh script](./enable_60_144hz.sh) in terminal.

- This script will cleanup old files and setup some extra environment variables you need to enable 144hz

2. Go back to game mode, and in `Display` settings, and turn off `Unified Frame Limit Management`, also make sure you enable/turn on `Use Native Color Temperature` as well.

3. If this fixes your 144Hz, you can stop here

- you should see no artificial 72fps cap in games, and fps limiter should work
- swapping to 60hz should work, and fps limiter should similarly work here
  - note that steamUI forces 144hz, you won't see 60hz in steam UI
- **WARNING FOR THE REFRESH SLIDER: any values other than 60hz and 144hz is dangerous**, make sure to be careful when changing the screen refresh rate

4. If steps 1-3 didn't fix your 144hz, continue on to the following:

Download Valve's Neptune Kernel with acpi_call precompiled (thanks [@corando98](https://github.com/corando98/) for compiling the rpm!) [download link, should be the 1.51GB file](https://drive.filen.io/f/9271e6eb-95e7-4deb-bc80-a90a620ebf53#175zrewF3URWgsnNfQMzETlJA4Auy5xo)

```
# (optional) for those that want to verify the file integrity of the download, here's the md5sum
$ md5sum kernel-6.1.52_valve14_1_neptune_acpi_call.x86_64.rpm
bd51cbb23972171026b6219b705f2127  kernel-6.1.52_valve14_1_neptune_acpi_call.x86_64.rpm
```

5. Open the folder where your download is in terminal, and run:

```
sudo dnf install kernel-6.1.52_valve14_1_neptune_acpi_call.x86_64.rpm
```

After install is complete, reboot and go back to desktop mode

6. Run `uname -r` in terminal, and verify that you are running the valve kernel. You should see:

```
6.1.52-valve14-1-neptune-61
```

Also run `sudo modprobe acpi_call` in terminal, you should see no errors

7. Retest and see if you're seeing any issues on 144Hz

### Setup lock screen for desktop mode only (NobaraOS)

Currently, Desktop mode does not have a lock screen during suspend-resume cycles on NobaraOS.

To fix this, go into Desktop mode, then configure `Screen Locking` in KDE desktop settings. You can optionally configure it for `after waking from sleep`.

This should show a login screen for suspend/resume in desktop mode only. In game mode, you should still get the expected regular behavior.

### Setup Monitor script that blasts fans when CPU temps climb too high (tested on NobaraOS only)

The Legion Go bios currently has behavior where if temps get too high, it manually forces TDP values to lower values until temps cool off

To mitgate this issue, you can setup a monitoring script that will blast the fans at full speed whenever it sees temps that are too high.

Install Instructions:

1. run `sudo modprobe acpi_call`, and see if this command errors out for you; if it does display an error, you need to install `acpi_call` on your linux distro

If it did not error out for you, we need to enable acpi_call by default so that you don't need to manually run `sudo modprobe acpi_call` on every boot.

You can do so via running the following script: [enable-acpi-call.sh](./enable-acpi-call.sh)

2. Download the files required

```
cd $HOME && git clone https://github.com/corando98/LLG_Dev_scripts.git
```

3. before installing, you can edit the `$HOME/LLG_Dev_scripts/fan-helper_install.sh` script if you'd like to change what temperature will trigger the fan

- in the file, you can replace the `85` in the line with `--temp_high 85 --temp_low 80`

4. run the install script, it will ask for your sudo password

```
cd $HOME/LLG_Dev_scripts && chmod +x ./fan-helper_install.sh && sudo ./fan-helper_install.sh
```

5. To verify that it's working, you can type in the following: `sudo systemctl status legion_fan_helper.service`

The result should look something like this:

```
Jan 03 21:03:19 nobaraLGO systemd[1]: Started legion_fan_helper.service - Legion Go Fan helper method.
Jan 03 21:03:19 nobaraLGO python3[10905]: 2024-01-03 21:03:19,473 - INFO - CPU Temperature: 46°C
Jan 03 21:03:19 nobaraLGO python3[10905]: 2024-01-03 21:03:19,473 - INFO - AC Status: Plugged In
```

6. You can now quit/close the terminal, and reboot

7. If you ever want to uninstall this temperature monitoring script, run the following one line at a time:

```
sudo systemctl disable --now legion_fan_helper.service
sudo rm /etc/systemd/system/legion_fan_helper.service
sudo rm -rf $HOME/LLG_Dev_scripts
```

### Change default boot kernel on Nobara v39

This will let you change your default kernel.

using this command to figure out the kernels you have installed:

```bash
sudo grubby --info=ALL | grep kernel
```

Look for the version number you want to make the default, it should be a number, something like `6.7.0-204`

Then run the following

```bash
sudo grubby --info=ALL | grep -i REPLACE_THIS_WITH_KERNEL_VERSION_NUMBER -B 1 | grep index

# example: if you're looking for 6.6.9-203
sudo grubby --info=ALL | grep -i 6.6.9-203 -B 1 | grep index
```

You should get an index number that shows up after running the command.

After having index number of the kernel you want as the default, run:

```bash
sudo grubby --set-default THIS_INDEX_NUMBER

# example: if the number is 3
sudo grubby --set-default 3
```

Then reboot, and verify it's working:

```bash
# reboot, this should print out your kernel version
uname -r
```

Thanks @cox on discord for the info, and @megabadd for recommending some improvements to the instructions

### Fix display-out not working for a display/monitor that previously worked

`~/.config/gamescope/modes.cfg` contains resolutions for monitors that have been configured.

Sometimes monitor settings in the file are set to erroneous values that the display could not handle.

To fix this, removing the display from the file allows it to be reconfigured. thanks @braymur on discord

### NobaraOS Desktop Mode - automatically set desktop resolution scale

1. Save [this script](./desktopmode-autoscale.sh) somewhere and mark it as executable

- `chmod +x ./desktopmode-autoscale.sh`

2. (optional) edit script's `SCALE=1.5` value to whatever scale you want

3. Add it to your KDE Autostart config (Menu > search for Autostart > Add)

### Use MangoHud for battery indicator

Battery indicator is inconsistent on the Legion go. As a workaround, you can change mangohud to show just the battery on one of the presets.

example preset, file should be in the `$HOME/.config/MangoHud/presets.conf`

```
[preset 1]
battery=1
fps=0
cpu_stats=0
gpu_stats=0
frame_timing=0
```

### Fuzzy screen issue

If you're seeeing a fuzzy screen, it means that the you're somehow using an invalid refresh rate. The only valid refresh rates for a game are 60 and 144Hz.

You can work around this by force enabling 60Hz, see [fps fix](#fix-60hz-and-144hz-only-for-nobaraos-v39)

### Disable nobaraOS grub boot menu during boot

[Source](https://www.reddit.com/r/linux4noobs/comments/wzoiu4/comment/im5cfx7/?context=3)

Note, you should never change the content of `/boot/grub/grub.cfg`

What you probably want to do is to hide grub’s boot menu, you can do it two ways:

- By hiding the boot menu
  - for non-technical users:
    - go to your `/etc/default` folder, then open the `grub` file with kate.
    - Edit the file and add `GRUB_TIMEOUT_STYLE=hidden`, then save. It'll prompt for your password.
    - Afterwards, in terminal run `sudo update-grub`
  - for technical users: `sudo vim /etc/default/grub` and set `GRUB_TIMEOUT_STYLE=hidden`. save changes, then run `sudo update-grub`
- Or by making the boot menu timeout 0, to do that:
  - for non-technical users:
    - go to your `/etc/default` folder, then open the `grub` file with kate.
    - Edit the file and set `GRUB_TIMEOUT=0`, then save. It'll prompt for your password.
    - Afterwards, in terminal run `sudo grub-mkconfig`
  - for technical users:`sudo vim /etc/default/grub` and set `GRUB_TIMEOUT=0`. save changes, then run `sudo grub-mkconfig` to generate `/boot/grub/grub.cfg`

Tip: even if your boot menu is hidden, you can access it when your pc is starting:

If you have BIOS: press and hold SHIFT key right after you see you Motherboard/PC splash screen

If you have UEFI: start pressing ESC the moment you see your motherboard/pc splash screen.

### Updated Nested Desktop with Nobara 39 (thanks matt_schwartz for the update):

`sudo dnf install plasma-lookandfeel-nobara-steamdeck-additions`

includes:

- should support Legion Go at native resolution
  - It should work for both Steam Deck and ROG Ally.
  - Make sure to set the game entry to “Native” in the Steam game settings menu first.
- you’ll have to set scaling once in the KDE settings when the nested desktop session loads for the first time but it should save it for future nested desktop sessions
  or else the screen will be for ants at 1600p
- also adds back the right-click “add to steam” shortcut you get with the steamdeck-KDE-presets package (which conflicts with the new theming)

<!-- Here's a run down on how to get running
```git clone https://github.com/AdnanHodzic/auto-cpufreq.git```
```cd auto-cpufreq && sudo ./auto-cpufreq-installer```
Create this in /etc/auto-cpufreq.conf
```[charger]

governor = shedutil
energy_performance_preference = performance
scaling_min_freq = 1600000
scaling_max_freq = 5100000
turbo = auto

[battery]

governor = powersave
energy_performance_preference = performance
scaling_min_freq = 1600000
scaling_max_freq = 3300000
turbo = auto ```
then ```auto-cpufreq --install``` or if you want to see what's it doing ```auto-cpufreq --live``` -->

## Bazzite Deck Edition Guides

### Blank Screen on First Reboot

If you see a frozen or blank screen on first reboot after a fresh installation of Bazzite, you can permanently fix the issue via the following:

1. press `Ctrl + Alt + F2` to open a terminal
2. login via your username and password
3. once logged in, type `steamos-session-select plasma`

- if you are on deck-gnome, try swapping `plasma` with `gnome` if it doesn't work

4. the terminal command should switch you to desktop mode
5. from desktop mode, just press the `Return to Game Mode` shortcut on the Desktop

- for deck-gnome, the `return to game mode` shortcut will be in the menu that you see after you click the top-left corner of the screen

### Nested Desktop Screen is rotated incorrectly

Open terminal in Nested Desktop (NOT Desktop mode), and run the following:

```bash
kscreen-doctor output.1.rotation.normal
```

then restart nested desktop

### Change Nested Desktop Resolution

run the [bazzite-nested-desktop-resolution.sh](./bazzite-nested-desktop-resolution.sh) script.

You can edit the script with your preferred nested desktop resolution before running it.

After running the script, restart Game mode. Then change steam's resolution to match the resolution you set.

# Emulator Info

Emulator related documentation, including recommended settings, etc.

## HHD (Dualsense Controller emulation)

Game emulators sometimes don't recognize the emulated dualsense controller via HHD.

This is usually because the emulator may have temporarily latched onto the original xbox controller instead of the emulated dualsense

- you can usually resolve this by flipping the fps-mode switch on and off.
- if you still have a controller issue afterwards, reorder the controller from player 2 to player 1 in the QAM.
  - sometimes steam registers the emulated controller as player 2 even when no other controller is attached

## Emudeck

For to install, go to https://www.emudeck.com, and scroll down to the section that shows installer options.

If the `Linux` install option doesn't work for you, the `ChimeraOS` install instructions should also work fine on other Linux distros.

## Dolphin (Gamecube)

For to improve stability, make sure you disable V-sync in the Dolphin settings

- open Dolphin on desktop mode
- select `Graphics`
- disable V-sync, save changes

# TDP Control:

Note that the Legion Go (LGO) has an issue in STT mode (vs STAMP mode in the bios), where custom TDP values will eventually get changed by the bios while in STT mode. STAMP mode fixes this, but there are users reporting crashing while in STAMP mode. STT does not have this stability issue.

If you use the SimpleDeckyTDP plugin with the [LGO custom TDP method](https://github.com/aarron-lee/SimpleDeckyTDP/blob/main/py_modules/devices/README.md#experimental-custom-tdp-method-for-the-legion-go), fixes stability issues on STAMP. Note that this requires bios v28 or newer

There's a few options for TDP Control on the Legion Go.

### `Legion_L + Y` combo

source: https://linuxgamingcentral.com/posts/chimeraos-on-legion-go/

> You can switch colors (of the power LED) by holding Legion L + Y. Each time you press this combination, you change the performance mode:

- quiet: blue LED; uses about 8 W
- balanced: white LED; uses about 15 W
- performance: red LED; uses about 20 W
- custom: purple LED; uses anywhere from 5-30 W; although at default it seems to be around 20 W

For `custom` on the new bios (bios v28) Custom by default is 30W TDP with everything maxed out
And it resets every time you switch modes

### SimpleDeckyTDP

Decky Plugin that provides TDP and GPU controls. Also has an option to fix Steam's TDP + GPU Sliders. Note that there's a risk that Decky Plugins can stop working from any Steam updates from Valve

https://github.com/aarron-lee/SimpleDeckyTDP

### Steam Patch

Steam Patch enables Steam's TDP slider + GPU sliders to work. Note that this works by patching the Steam client, which means that any Steam updates from Valve can potentially break this fix.

https://github.com/corando98/steam-patch

### Simple Ryzen TDP

Basic Desktop app for TDP control, but can also be added to game mode as a backup option

https://github.com/aarron-lee/simple-ryzen-tdp/

# Controller support

### hhd

Link: https://github.com/antheas/hhd

PS5 Dualsense Edge controller emulator, currently supports all buttons on the LGO controller except the back scrollwheel (scrollwheel already worked previously). Has improvements vs rogue, such as more consistently working rumble, config file for configuring different options, RGB LED control via steam input, etc. It also supports managing the power button, so no extra program is necessary.

It is preinstalled on Bazzite for the Legion Go.

Install instructions are available on the github.

### HandyGCCS (aka handycon)

Default installed OOTB on ChimeraOS, Nobara Deck Edition. It supports all the standard Xbox controls, `Legion_L + X` for Steam/Home, `Legion_L + A` for QAM. Back buttons are not supported.

Note that you can get back buttons to work with the LegionGoRemapper plugin, but it has the same limitations as the LegionSpace app on Windows; you can only remap back buttons to other controller buttons, and they cannot be managed individually in Steam Input.

### rogue-enemy (deprecated/no longer maintained)

Link: https://github.com/corando98/ROGueENEMY

PS5 Dualsense Edge controller emulator, currently manages all hardware buttons except the back scrollwheel (scrollwheel already works). Back buttons are usable in Steam Input, same for the trackpad.

Note that rogue-enemy has conflicts with handygccs, so it must be disabled. Also, since handygccs handles for the power button, you'll need a separate solution for power button suspend. You can use this, which was extracted from handygccs: https://github.com/aarron-lee/steam-powerbuttond

# Quality Of Life Fixes

### LegionGoRemapper Decky Plugin - Fan Control + RGB control + backbutton remapping

Link: https://github.com/aarron-lee/LegionGoRemapper/

Allows for managing back button remaps, controller RGB lights, toggle touchpad on/off, etc

You can also enable custom fan curves, confirmed functional on bios v29

- note that this uses the exact same functionality as LegionSpace on Windows, so it has the same limitations
- back button remapping should not be used w/ PS5 controller emulation

### CSS Loader Plugin - Themes

- note, requires `CSS Loader` Decky Plugin
- manually install by downloading the theme + placing in `$HOME/homebrew/themes/` folder
- sometimes themes require a reboot before they start working.

Legion Go Theme - https://github.com/frazse/SBP-Legion-Go-Theme

PS5 to Xbox Controller Glyph Theme - https://github.com/frazse/PS5-to-Xbox-glyphs

- If you'd like to manually edit mappings, you can find glyphs at `$HOME/.local/share/Steam/controller_base/images/api/dark/`
  - manual mapping can be done by editing the css file with the svg/png paths you want

```
# quick install, CSS Loader Decky Plugin must already be installed and enabled

# Legion Go Theme Install
cd $HOME/homebrew/themes && git clone https://github.com/frazse/SBP-Legion-Go-Theme.git

# PS5 to Xbox Controller Glyph Theme
cd $HOME/homebrew/themes && git clone https://github.com/frazse/PS5-to-Xbox-glyphs
```

# Resolved or won't-fix bugs (changelog for documentation purposes)

- Dec 9th 2023 - Nobara desktop mode shortcut might break for users that update their Nobara installation. This should not apply to brand new, clean installations.
  - this issue has been fixed on NobaraOS 39
  - recent installations by users indicate that this bug has been resolved on Nobara 38
  - Manual fix at the bottom of the page [here](#nobara-desktop-mode-switch-temporary-fix)
- (won't fix) Bugs for Pipewire EQ sound improvements - Pipewire EQ sound improvements are an optional sound fix for the LGO, currently is buggy and not recommended
  - This is most likely due to a Steam Deck OLED related update.

# 3D prints

https://makerworld.com/en/models/88724#profileId-94984

https://www.thingiverse.com/thing:6364915/files

https://makerworld.com/en/models/57312#profileId-94578

https://www.thingiverse.com/thing:4675734

https://www.reddit.com/r/LegionGo/comments/17s8rv8/3d_printed_steamclip_kickstand_released/

https://www.thingiverse.com/thing:6311208/

# Misc

install AppImage manager

```
flatpak install flathub it.mijorus.gearlever -y --user
```

<!-- https://steamcommunity.com/groups/SteamClientBeta/discussions/3/3775742015037677494/ -->
