# Archbook
A complete guide on how to make your chromebook into a lightweight Arch Linux machine with the i3 window manager.
Special thanks to MrChromebox for providing the custom UEFI firmware and script.

## Screenshots
<img src="screenshots/Pictures/screenshot-1.png" alt="Screenshot" width="500">
<img src="screenshots/Pictures/screenshot-2.png" alt="Screenshot" width="500">
<img src="screenshots/Pictures/screenshot-3.png" alt="Screenshot" width="500">
<img src="screenshots/Pictures/screenshot-4.png" alt="Screenshot" width="500">

## Requirements
- A chromebook
- A screwdriver
- A USB drive with the latest Arch Linux ISO (other distros can also be installed, but we will use arch for this build)

## Let's get started

First, check your chromebook model and your motherboard model, you can check your model at the bottom stickers.
I had the Acer R752T, so it had the Bobba360 motherboard, you can check your motherboard on > https://docs.mrchromebox.tech/docs/supported-devices.html#device-listing

### Developer mode
When you have your chromebook model, you have to turn on the Developer Mode. This might be different for other chromebooks model, search your developer mode keys. 
Be careful—this will erase all local data on your Chromebook. Turn on developer mode by:
- Power off
- Esc + Refresh + Power
- When you see a recuperation panel, press Ctrl + D
- Then set the OS verification off
- When the OS verification is off press Ctrl + D again
- Now, everytime you power on the chromebook, you will find the OS verification is OFF window, press Ctrl + D to boot normally into the dev mode.

Great! Now, set up your account again. Do not set a password for the developer account, as this might cause issues preventing you from using sudo commands.
Once you boot in, you have to run the Firmware Utility Script, to see if you have fw write protection.

## Firmware Utility Script

Run the script from the VT2 terminal. While on the desktop, press Ctrl + Alt + F2 (or the forward arrow key).
Then, login with the user chronos, you should be able to boot in without a password.
Paste the following command in the terminal 
```bash
cd; curl -LO mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
```

You should see your chromebook info and your Firmware Protection status, if it's not enabled, skip the next step.
## Disabling Firmware Protection

The method to disable firmware protection depends on your Chromebook model. For example, I had a Bobba360, so I used the battery removal method. Check out the motherboard page to see what method you should use > https://docs.mrchromebox.tech/docs/supported-devices.html#device-listing

### Method 1 : Removing the Write protection screw
- Power off the device
- Unscrew the back and open it
- Remove the WP screw, you can see it at > https://docs.mrchromebox.tech/docs/supported-devices.html#device-listing
- Reassemble the device
- Skip to the next step

### Method 2 : Disconnecting the battery
- Power off the device
- Unscrew the back and open it
- Remove the battery
- Plug in the charger to your usb-c port
- Reassemble the device
- Skip to the next step
- Once you finished the tutorial, you can put back in the battery

### Method 3 : Bridging a jumper
- Power off the device and unplug the charger
- Unscrew the back and open it
- Disconnect the battery
- Locate the WP jumper, you can see it at > https://docs.mrchromebox.tech/docs/supported-devices.html#device-listing
- If the jumper holes are opened, you can make a bridge with a paperclip, else, you might have to make a solder bridge
- Reconnect the battery
- Reassemble the device
- Skip to the next step

If your model requires a SuzyQable, refer to this guide for instructions: https://docs.mrchromebox.tech/docs/firmware/wp/disabling.html#using-closed-case-debugging-ccd-using-a-suzyqable

## Installing the custom UEFI
Run the Firmware Utility Script at the vt2 terminal with
```bash
cd; curl -LO mrchromebox.tech/firmware-util.sh && sudo bash firmware-util.sh
```
Choose the second option (CAUTION: THIS MIGHT BRICK YOUR DEVICE, DO AT YOUR OWN RISK)
If its not available, sorry, you cannot install Arch Linux as your chromebook isnt compatible with custom UEFI.
Follow the instructions of the script.

## Booting into the custom UEFI
Once it's done, reboot your device to boot into the custom UEFI. When you see the UEFI logo, press esc, and plug in your installation USB. Go to boot options and boot with your USB, this should boot into the Arch installer.

## Installing arch
In this step we'll install arch linux.

Connect to wifi using `iwctl` utility : 

```bash
iwctl
device list
station name scan #replace name with your device name ex: wlan0
station name get-networks
station name connect SSID
```
Ethernet should just work right away.
Then write archinstall command, and choose your hour, partitions, hostname, username, password etc.
It's important to select the desktop profile, i3WM, and PipeWire for audio. I also recommend using NetworkManager for Wi-Fi management.

Press done and reboot your computer (IMPORTANT : Remove the instalation media.)

## Ricing and configuring arch
Ricing’ refers to customizing your system's appearance. We'll achieve this using my i3WM dotfiles.
Install the following packages with:

```bash
pacman -S cairo libxcb python2 neofetch nerd-fonts font-awesome xcb-proto xcb-util-image xcb-util-wm xcb-util-xrm jsoncpp i3-gaps dmenu ranger vim rxvt-unicode feh imagemagick python-pip python-pywal w3m git
```
Then, we should install `yay` to add some more packages.
```bash
cd ~
mkdir -p /tmp/yay_install
cd /tmp/yay_install

sudo pacman -S base-devel

sudo pacman -S expac yajl git

git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si

cd ~
rm -rf /tmp/yay_install
```

Then add the following packages with yay
```bash
yay -S polybar-git picom otf-overpass
```
Then, download my dotfiles, 

```bash
cd ~
git clone https://github.com/VendavalSC/archbook
cp -r ~/archbook/neofetch/ ~/.config/
cp -r ~/archbook/i3/ ~/.config/
cp -r ~/archbook/picom/ ~/.config/
cp -r ~/archbook/polybar/ ~/.config/

cp -r ~/archbook/.Xresources ~/
xrdb .Xresources
```

Finally, you have to install your wallpaper, and store it in `/Pictures/wal` as `bg1.jpg`
If you installed my dotfiles, you have my wallpaper, so, you can get it from there.
```bash
mv ~/archbook/bg1.jpg ~/Pictures/wal/bg1.jpg
wal -i ~/Pictures/wal/bg1.jpg
```

Then, for the last step, you just put
```bash
polybar bar
```

Reboot your laptop by using
```bash
reboot now
```

### Login Manager (Optional)
We'll install lightdm-mini-greeter as our login manager. Let's begin. *This only works with one user.

First, well install the lightdm-mini-greeter.

```bash
yay -S lightdm-mini-greeter
```
Great, then, copy my lightdm folder into yours with:
```bash
cp -r ~/archbook/lightdm /etc/
```
Then, you'll have to modify the lightdm-mini-greeter file, so you log-in into the right user:
```bash
nano /etc/lightdm/lightdm-mini-greeter.conf
```
Replace v3nd1 with your username in the 7th line.

## Done
You're done! Enjoy!



