# arch-process
This repo describes the processes I went through to install and configure arch into my laptop. This is more of a personal reference than anything else, but I'm willing to try to help out anyone who bumps into this readme and wants further guidance.

**Make sure you've read through this ReadMe in its entirety before carrying out the tasks.**

**It also helps to skim through the [Arch Linux Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide) before starting.**

## Laptop Specs:
- Intel core i5 7th Gen
- 16GB RAM
- NVIDIA GTX 1050 Mobile

## Steps

### Installation
1. Download [Arch Linux ISO](https://www.archlinux.org/download/)
2. Burn it into your USB drive. You can use various software for this, like [UNetBootin](https://unetbootin.github.io/). I used the following command: `sudo dd if=<iso name> of=/dev/sdx`, where `/dev/sdx` is the name of your to-be-bootable partition. 
3. Reboot the laptop, select the USB device from the list of UEFI boot options available.
4. You should see a GRUB menu, with several options including "Boot Arch Linux (x86_64)". With this option highlighted, press `e` to modify the boot parameters. You should see a line (or multiple lines) of text with several options in it. Append `nomodeset` to this line to ensure your network adapters work properly (I'm not sure why this is necessary, but they don't get activated otherwise). After appending, press `Ctrl+X` to boot into the live media with the updated boot parameters. 
5. Once you have booted, you will see a shell with the `root` user logged in. From this point on, you're ready to start following the [Arch Linux Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide). Here are notes corresponding the steps that should be kept in mind:
    1. **Connect to the Internet**: In this step, if you're connecting to a wi-fi network, you can use `wpa_supplicant` to connect to it. Here is a configuration file that I used to save a Haverford College network's details (YMMV):
        ```
            wpa.conf
            --------
            network={
                ssid="eduroam"
                scan_ssid=1
                key_mgmt=WPA-EAP
                eap=PEAP
                phase2="auth=MSCHAPV2"
                identity="<your eduroam username>"
                password="<your eduroam password>"
            }
        ```
        *Hint: you can use `[vim](https://vim-adventures.com/)` to edit the file, since it's available in the live media.*
        
        Once you've created this config file, assuming you know the name of your wireless interface (run `ip link`  to find out), run this command: `wpa_supplicant -B -i *interface* -c wpa.conf`
        
        If this command is successful, you will need to get an IP address: `dhcpcd *interface*`. Then you'll be connected! 
        
        To verify, you can run `ping google.com` to see if you get any packets in return. 
        
    2. **Install essential packages**: the guide suggests running `pacstrap /mnt base linux linux-firmware` to get a minimal working installation. However, I've discovered that the latest linux kernel (5.4.6 on arch) doesn't work with the touchpad out of the box. So, I opted for an LTS release of the kernel, which is currently 4.19. I also added a couple other packages to get my minimum installation. These are the following:
        1. Core:
            1. `base`
            2. `linux-lts`
            3. `linux-firmware`
        2. Text editor:
            1. `vim`
        3. For bootloading:
            1. `grub`
            2. `efibootmgr` (for UEFI)
            3. `os-prober` (for multi-booting; since I have Windows on my machine)
            4. `intel-ucode` (microcode for intel processor bug-fixes)
        4. For a GUI:
            1. `xorg-server`
            2. `lightdm`
            3. `lightdm-gtk-greeter` (login screen)
            4. `i3-gaps`
            5. `nvidia`
        5. Networking on the GUI:
            1. `networkmanager`
            2. `network-manager-applet`
        6. Other:
            1. `sudo` (permissions)
            2. `firefox`
            3. `light` (to control brightness)
            4. `rxvt-unicode` (Terminal emulator)
    3. **Add a non-root user**: By default, Arch comes with a root user with all permissions. To make the system more secure, create another user you'll regularly use following [this](https://wiki.archlinux.org/index.php/Users_and_groups#Example_adding_a_user) doc:
        1. `useradd -m username`
        2. `passwd username`
        3. Add the users to `sudo-ers` (read [this](https://wiki.archlinux.org/index.php/Sudo#Configuration) first):
            1. `EDITOR=vim visudo`
            2. Find the line that says `root ALL=(ALL) ALL` and add `username ALL=(ALL) ALL` below that line, and save.
    4. Add bootloader ([docs](https://wiki.archlinux.org/index.php/GRUB#UEFI_systems)):
        1. Mount your EFI partition (let's say in /efi)
        2. grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
        3. Write main configuration: `grub-mkconfig -o /boot/grub/grub.cfg`
5. Done! Reboot.
        
### Post-installation
After the reboot, you should see a login shell, which indicates a working system. To verify that the GUI works, log in as `root` and run: `systemctl start lightdm`. This should show the LightDM greeter with the non-root user selected. Login with that user. You should see anunconfigured i3 desktop. At this point, the GUI is working. 

From this point on, do a couple of things:
1. Connect to internet:
    1. Run `sudo systemctl enable NetworkManager`. This will ensure NetworkManager starts at boot.
    2. Run `sudo systemctl start NetworkManager` to start it. 
    3. To get access to the network connection editor GUI, run `nm-applet`. This should add an icon to the i3 taskbar. Once you click on this icon, you will see a list of networks to connect to. Connect to internet from there (security reading on passwords [here](https://wiki.archlinux.org/index.php/NetworkManager#Encrypted_Wi-Fi_passwords)). 
2. Start LightDM on boot: 
    1. Similar to NetworkManager, run `sudo systemctl enable lightdm`.
3. Enable [tap-to-click in your X11 Window Manager](https://cravencode.com/post/essentials/enable-tap-to-click-in-i3wm/).
4. Enable brightness control:
    1. `light`, one of the packages installed before, is a command-line utility to adjust brightness. To increase brightness by 5%, run `light -A 5`. To decrease by 5%, run `light -U 5`.
    2. These two commands can be binded with the brightness control buttons when using i3. Open `~/.config/i3/config` in your text editor, and add the following lines there:
        ```
        ~/.config/i3/config
        -------------------
        bindsym XF86MonBrightnessUp exec light -A 5 # increase screen brightness
        bindsym XF86MonBrightnessDown exec light -U 5 # decrease screen brightness
        ```
    3. Then, add your non-root user to the `video` group to ensure you can actually adjust brightness: `sudo usermod -aG video username`.
    4. Reboot and enjoy :)


