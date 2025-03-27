# Jetson-development-board-setting
# Setting-up and flashing the Jetson Xavier NX development board JNX30D for the very first time.

# Contents
* [Introduction](#Introduction)
* [Part 1: To set a host pc for Jetson Xavier NX](#part1)
* [Part 2: Installation from Auvidea web-page and flashing](#part2)
* [Part 3: Booting from emmc to SSD](#part3)
* [Part 4: Install remaining SDK libraries on Jetson Ubuntu OS](#part4)

<a name="Introduction"></a>
# Introduction
This is a step-by-step guide to configure the Jetson Xavier NX (JNX30D) development board for the very first time. The two key tasks are flashing the board and switching the boot from system's internal emmc to SSD. The four steps listed below can be used to complete these tasks.

<a name="part1"></a>
## Part 1: To set a host pc for Jetson Xavier NX
1. Make sure to have a PC with Ubuntu 18.04/20.04.22.10. In my case, it was 18.04. 
2. Make sure th Python is installed as it is required for NVIDIA SDK Manager. In my case, Python 3.9 was installed. 
3. Make sure to have a standard USB Type A to micro-USB cable of good quality. 
4. Host PC should have an Internet connection. 
5. Download the `NVIDIA sdk manager` from the link: [nvidia-sdk-manager](https://developer.nvidia.com/nvidia-sdk-manager)
6. Open the terminal, and change the folder where the file is downloaded:
```$ cd ../downloaded_folder```
7. Install it by typing: 
```$ sudo apt install ../sdkmanager_1.9.1-10844_amd64.deb```

**Note:** Freeing up space on the system hard drive is strongly advised over using an external device. Up to 42GB of the memory was free in my case.

8. Launch `NVIDIA sdk manager` from terminal window by typing `sdkmanager` and hit enter. A GUI will launch, requiring authentication. Input credentials, then go to the further steps.
9. Install `Jetpack` for the Jetson Xavier NX development kit by completig the following sub-steps from GUI screen:
    
 - 9.1. select `Jetson` from the `product category` menu.
   
 - 9.2. select `Target Hardware (Jetson Xavier NX development kit)` from `Hardware configuration` menu.  
 - 9.3. select `Linux and Jetpack` from the `Target operating system` menu.
 - 9.4. select `Deepstream` from the `Additional SDKs` menu. Then continue to the next steps. 

10. After installation is complete, skip the flashing process.

<a name="part2"></a>
## Part 2: Installation from Auvidea web-page and flashing
11. Go to [Auvidea web-page](https://auvidea.eu/firmware/) to download the latest firmware for the Jetson Xavier NX board, as shown in Figure 3.
12. Open the terminal, and change the folder where the file is downloaded:
```$ cd ../downloaded_folder```
13. Extract the downloaded file.
 ``` $ tar xvf Jetpack_4_6_TX2NX_JNX30.tar.bz2```
14. Change the directory to the extracted files and then extract the `kernel_out.tar.bz2` file.
    
 ``` $ cd Jetpack_4_6_TX2NX_JNX30```
 
 ``` $ tar xvf kernel_out.tar.bz2```
 
15. Copy the extracted `kernel_out` folder into Jetpack Linux for Tegra folder and the path of the folder would be: ``. 
``` ```
16. Connect the Jetson Xavier NX board to the host PC using a USB cable and power it on.
17. Change the current directory to Linux for Tegra folder.
 ``` $ cd .../JetPack_5.1_Linux_JETSON_XAVIER_NX_TARGETS/Linux_for_Tegra```
18. Type `lsusb` in the terminal. It shoud display the Xavier NX board. It is in auto-recovery mode when it is connected for the very first time.
  

19. Connect the Jetson Xavier NX board to the host PC using USB cable, and power it on.
20. Connect a monitor using an HDMI cable, a keyboard, and a mouse to the development board.
21. To flash the board, execute:
    
 ```$ sudo ../nvsdkmanager_flash.sh jetson-xavier-nx-devkit-nx30d nvme0n1p1```

If the installation is successful, and the Ubuntu 20.04 will appear on the connected monitor. 

22. Follow the instructions that are mentioned on the Ubuntu development board for stuff like language, location, login details, etc.

*The flashing and booting were successful. Now it is good to go! Wait Wait... You would see the installation is on the internal emmc, not the SSD drive. Thus, it's time to change the booting from the emmc to the SSD. For this, see Part 3.*
 

<a name="part3"></a>
## Part 3: Booting from emmc to SSD
Now, its time to play on the terminal window of Ubuntu OS on the Jetson Xavier NX board to boot it from SSD. You can disconnect the Jetson board from the host PC.

23. Open the `disc manager` in Ubuntu 20.04 and verify the name of the storages. In my case, the SSD storage has the name `/dev/nvme0n1`.
    
24. Set up RootFS on the SSD. To do this, launch Jetson Xavier NX's terminal, and to format the storage device, execute:      
```$ sudo parted YOUR_STORAGE_DEVICE_NAME mklabel gpt```

I set the command as: 

```$ sudo parted /dev/nvme0n1 mklabel gpt```

25. Create the RootFS partition by typing: 
```$ sudo parted <YOUR_STORAGE_DEVICE> mkpart APP 0GB <YOUR_ROOTFS_SIZE>```

I set 123GB in my case. You can set as per your storage device.

```$ sudo parted /dev/nvme0n1 mkpart APP 0GB 123GB```  

26. Create filesystem by typing: 
```$ sudo mkfs.ext4 <YOUR_STORAGE_DEVICE>1```

I set the command as: 
```$ sudo mkfs.ext4 nvme0n1p1```

28. Copy the existing RootFS to the storage device by typing:
```$ sudo mount <YOUR_STORAGE_DEVICE> /mnt``` 

I set the command as: 
```$ sudo mount nvme0n1p1 /mnt```

```$sudo rsync -axHAWX --numeric-ids --info=progress2 --exclude={"/dev/","/proc/","/sys/","/tmp/","/run/","/mnt/","/media/*","/lost+found"} / /mnt/``` 

I set the command as: 
 ```$ sudo rsync -axHAWX --numeric-ids --info=progress2 --exclude={"/dev/","/proc/","/sys/","/tmp/","/run/","/mnt/","/media/*","/lost+found"} / /mnt/```

29. The SSD drive must be set as the root target in `extlinux.conf` file. This is required in order for the operating system to locate the system files. To do this, open the extlinux.conf file by typing: 
```$ sudo nano /boot/extlinux/extlinux.conf```
For this, install the nano text editor first by typing `sudo apt install nano`
30. Now change the root path only in the `extlinux.conf` file by changing `root=<YOUR_STORAGE_DEVICE>1` as `root=/dev/nvme0n1p1`. Left the rest of the things same in the file. 
31. Reboot the Jetson Xavier NX development board. After re-booting, validate the SSD boot by typing `df /` on the terminal window, and it must display the SSD status. Also, confirm it by opening the file manager. 
 
<a name="part4"></a>
## Part 4: Install remaining SDK libraries on Jetson Ubuntu OS
Connect the system to the Internet, and run:




![Jetson Xavier NX Software Setup Guide]:("https://auvidea.eu/product/70879/")
