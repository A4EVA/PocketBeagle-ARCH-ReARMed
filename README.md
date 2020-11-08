# PocketBeagle Arch Linux Installation
This repository is the result of the work done on the forum Archlinuxarm (link:https://archlinuxarm.org/forum/viewtopic.php?f=9&t=14823) and will help you to prepare a bootable arch linux sd card for the BeagleBone Black and Pocket Beagle the following instructions are different from the original one you'll find on: https://archlinuxarm.org/platforms/armv7/ti/beaglebone-black because in order to support the Pocket Beagle i have to compile myself u-boot in order to have it work properly with the Pocket Beagle SoM. The new compiled u-boot that you can find in this repository is bigger than the original one present into the arch tar file so the ext4 partition should start from 4096 and not 2048, this step is mandatory, because if the partition start at 2048 the following use od dd commands, needed to place the bootloader in the righ spot, will corrupt the partition and if you try to boot from the sd card u-boot will not find any ext4 filesystem. Following Instruction has been tested on Pocket Beagle and BeagleBone Black. A special Thank YOU to summers for the precius help provided to troubleshoot the problems we encountered on the way.

Credits:
- TheJoker187
- Summers

# SD Card Preparation

Replace sdX in the following instructions with the device name for the SD card as it appears on your computer.

  - Zero the beginning of the SD card
    ```sh
      dd if=/dev/zero of=/dev/sdX bs=1M count=8
  - Start fdisk to partition the SD card: 
    ```sh
      fdisk /dev/sdX
    ```
  - At the fdisk prompt, delete old partitions and create a new one: 
    - a. Type o This will clear out any partitions on the drive.
    - b. Type p to list partitions. There should be no partitions left. 
    - c. Now type n, then p for primary, 1 for the first partition on the drive, 4096 for the first sector, and then press ENTER to accept the default last sector. 
    - d. Write the partition table and exit by typing w.
 - Create the ext4 filesystem: 
    ```sh
      mkfs.ext4 /dev/sdX1
    ```
 - Mount the filesystem: 
   ```sh
      mkdir mnt
      mount /dev/sdX1 mnt
 - Download and extract the root filesystem:
    ```sh
     wget http://os.archlinuxarm.org/os/ArchLinuxARM-am33x-latest.tar.gz
     bsdtar -xpf ArchLinuxARM-am33x-latest.tar.gz -C mnt
     sync
 - Remove the original bootloader files from /mnt/boot :
   ```sh
     rm MLO
     rm u-boot.img
 - Copy the new uboot files into /mnt/boot :
    ```sh
     cp /"PATH TO THIS CLONED REPO"/u-boot/MLO /"PATH TO YOUR MNT"/mnt/boot
     cp /"PATH TO THIS CLONED REPO"/u-boot/u-boot.img /"PATH TO YOUR MNT"/mnt/boot
 - Install the U-Boot bootloader:
    ```sh
     dd if=mnt/boot/MLO of=/dev/sda count=1 seek=1 conv=notrunc bs=128k
     dd if=mnt/boot/u-boot.img of=/dev/sda count=2 seek=1 conv=notrunc bs=384k
     umount mnt
     sync
 - Insert the MicroSD into the PocketBeagle and apply power, keep in mind that you need UART to USB connected
    into the PocketBeagle to see the boot process and start the configuration of the board. At the moment there
    is no usb to ethernet functionality enabled on the board.

# New Features Cooming Soon !

  - USB Ethernet Gadget Functionality
  - USB Serial Gadget Functionality 
  - Script to automate all the passages above !
