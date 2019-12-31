# arch-process
This repo describes the processes I went through to install and configure arch into my laptop. This is more of a personal reference than anything else, but I'm willing to try to help out anyone who bumps into this readme and wants further guidance.

**Make sure you've read through this ReadMe in its entirety before carrying out the tasks.**

**It also helps to skim through the [Arch Linux Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide) before starting.**

## Laptop Specs:
- Intel core i5 7th Gen
- 16GB RAM
- NVIDIA GTX 1050 Mobile

## Steps
1. Download [Arch Linux ISO](https://www.archlinux.org/download/)
2. Burn it into your USB drive. You can use various software for this, like [UNetBootin](https://unetbootin.github.io/). I used the following command: `sudo dd if=<iso name> of=/dev/sdx`, where `/dev/sdx` is the name of your to-be-bootable partition. 
3. Reboot the laptop, select the USB device from the list of UEFI boot options available.
4. You should see a GRUB menu, with several options including "Boot Arch Linux (x86_64)". With this option highlighted, press `e` to modify the boot parameters. You should see a line (or multiple lines) of text with several options in it. Append `nomodeset` to this line to ensure your network adapters work properly (I'm not sure why this is necessary, but they don't get activated otherwise). After appending, press `Ctrl+X` to boot into the live media with the updated boot parameters. 
5. Once you have booted, you will see a shell with the `root` user logged in. From this point on, you're ready to start following the [Arch Linux Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide). Here are notes corresponding the steps that should be kept in mind:
  
    i. **Connect to the Internet**: In this step, if you're connecting to a wi-fi network, you can use `wpa_supplicant` to connect to it. Here is a configuration file that I used to save a Haverford College network's details (YMMV):
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
   
   ii. 
   
   
   
   
   
   
