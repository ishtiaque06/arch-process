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
  i. 
