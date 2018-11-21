# Building for Supported Renesas Boards

AGL supports building for several automotive
[Renesas](https://www.renesas.com/us/en/solutions/automotive.html) board kits.
Renesas is the number one supplier of vehicle control microcontrollers and
System on a Chip (SoC) products for the automotive industry.

This section provides the build and deploy steps you need to create an
image for the following Renesas platforms:

* [Renesas R-Car Starter Kit Pro Board](https://www.elinux.org/R-Car/Boards/M3SK)
* [Renesas R-Car Starter Kit Premier Board](https://www.elinux.org/R-Car/Boards/H3SK)
* [Renesas Salvator-X Board](https://www.elinux.org/R-Car/Boards/Salvator-X)
* [Renesas Kingfisher Infotainment Board](https://elinux.org/R-Car/Boards/Kingfisher)

**NOTE:** You can find similar information for the Pro and Premier board kits on the
[R-Car/Boards/Yocto-Gen3](https://elinux.org/R-Car/Boards/Yocto-Gen3) page.
The information on this page describes setup and build procedures for both these
Renesas development kits.


## 1. Downloading Prioprietory Drivers

Before setting up the build environment, you need to download proprietary drivers from the
[R-Car H3/M3 Software library and Technical document](https://www.renesas.com/us/en/solutions/automotive/rcar-download/rcar-demoboard-2.html)
site.
This download site supports the Pro and Premier board starter kits.

**NOTE:** Not sure what you do if you are using the Salvator-X or Kingfisher Infotainment boards.

Follow these steps to download the drivers you need:

1. **Determine the Files You Need:**

     Run the ``setup_mm_packages.sh`` script as follows to
     display the list of ZIP files containing the drivers you need.
     Following is an example:

     ```bash
     grep -rn ZIP_.= $AGL_TOP/meta-agl/meta-agl-bsp/meta-rcar-gen3/scripts/setup_mm_packages.sh
     3:ZIP_1="R-Car_Gen3_Series_Evaluation_Software_Package_for_Linux-weston2-20170904.zip"
     4:ZIP_2="R-Car_Gen3_Series_Evaluation_Software_Package_of_Linux_Drivers-weston2-20170904.zip"
     ```

     The script's output identifies the files you need to download from the page.

2. **Find the Download Links:**

   Find the appropriate download links on the
   [R-Car H3/M3 Software library and Technical document](https://www.renesas.com/us/en/solutions/automotive/rcar-download/rcar-demoboard-2.html)
   site.
   The file pairs are grouped according to the Yocto Project version you are
   using with the AGL software.
   The Flounder release of AGL uses the 2.4 version of the Yocto Project (i.e. "Rocko").

3. **Download the Files:**

   Start the download process by clicking the download link.
   If you do not have an account with Renesas, you will be asked to register a free account.
   You must register and follow the "Click Through" licensing process
   in order to download these proprietary files.

   If needed, follow the instructions to create the free account by providing the required
   account information.
   Once the account is registered and you are logged in, you can download the files.

   **NOTE:**
   You might have to re-access the
   [original page](https://www.renesas.com/us/en/solutions/automotive/rcar-download/rcar-demoboard-2.html)
   that contains the download links you need after creating the account and logging in.

4. **Create an Environment Variable to Point to Your Download Area:**

   Create and export an environment variable named `XDG_DOWNLOAD_DIR` that points to
   your download directory.
   Here is an example:

   ```bash
   $ export XDG_DOWNLOAD_DIR=$HOME/Downloads
   ```

5. **Be Sure the Files Have Rights:**

   Be sure you have the necessary rights for the files you downloaded.
   You can use the following command:

   ```bash
   chmod a+4 $XDG_DOWNLOAD_DIR/*.zip
   ```

6. **Check to be Sure the Files are Downloaded and Have the Correct Rights:**

   Do a quick listing of the files to ensure they are in the download directory and
   they have the correct access rights.
   Here is an example:

   ```bash
   $ ls -l $XDG_DOWNLOAD_DIR/*.zip
   -rw-rw-r-- 1 scottrif scottrif 4662080 Nov 19 14:48 /home/scottrif/Downloads/R-Car_Gen3_Series_Evaluation_Software_Package_for_Linux-weston2-20170904.zip
   -rw-rw-r-- 1 scottrif scottrif 3137626 Nov 19 14:49 /home/scottrif/Downloads/R-Car_Gen3_Series_Evaluation_Software_Package_of_Linux_Drivers-weston2-20170904.zip
   ```

## 2. Getting More Software

1. **Get the `bmaptool`:**

   Download this tool from the
   [bmap-tools](https://build.opensuse.org/package/show/isv:LinuxAutomotive:AGL_Master/bmap-tools)
   repository.
   The site has pre-built packages (DEB or RPM) for the supported host
   operating systems.

2. **Get Your Board Support Package (BSP) Version:**

   Be sure to have the correct BSP version of the R-Car Starter Kit
   based on the version of the AGL software you are using.
   Use the following table to map the Renesas version to your AGL software:

     | AGL Version| Renesas version |
     |:-:|:-:|
     | AGL master  | 3.9.0 |
     | AGL 6.0.0 | 3.7.0 |
     | AGL 5.0.x, 5.1.0| 2.23.1 |
     | AGL 4.0.x |2.19.0 |

   **NOTE:**
   I don't know how the user uses this information.
   I need more information.

## 3. Getting Your Hardware Together

   Gather together this list of hardware items, which is not exhaustive.
   Having these items ahead of time saves you from having to try and
   collect hardware during development:

   * Supported Starter Kit Gen3 board with its 5V power supply.
   * Micro USB-A cable for serial console.
     This cable is optional if you are using ethernet and an SSH connection.
   * USB 2.0 Hub.  The hub is optional but makes it easy to connect multiple USB devices.
   * Ethernet cable.  The cable is optional if you are using a serial console.
   * HDMI type D (Micro connector) cable and an associated display.
   * 4Gbyte minimum micro-SD Card.  It is recommended that you use a class 10 type.
   * USB touch screen device such as the GeChic 1502i/1503i.  A touch screen device is optional.

   **NOTE:** The Salvator-X Board has NDA restrictions.
   Consequently, less documentation is available for this board both here and across the
   Internet.


## 4. Making Sure Your Build Environment is Correct

   The
   "[Initializing Your Build Environment](../image-workflow-initialize-build-environment.html/Initializing-your-build-environment)"
   section presented generic information for setting up your build environment
   using the `aglsetup.sh` script.
   If you are building an image for a supported Renesas board,
   you need to take steps to make sure your build host is set up correctly.

1. **Define Your Board:**

   Depending on your Renesas board, define and export a `MACHINE` variable as follows:

   | Board| `MACHINE` Setting |
   |:-:|:-:|
   | Starter Kit Pro/M3  | `MACHINE`=m3ulcb |
   | Starter Kit Premier/H3  | `MACHINE`=h3ulcb |
   | Salvator-X  | `MACHINE`=h3-salvator-x |

   For example, the following command defines and exports the `MACHINE` variable
   for the Starter Kit Pro/M3 Board:

   ```bash
   $ export MACHINE=m3ulcb
   ```

2. **Run the `aglsetup.sh` Script:**

   Use the following commands to run the AGL Setup script:

   ```bash
   $ cd $AGL_TOP
   $ source meta-agl/scripts/aglsetup.sh -m $MACHINE -b build agl-devel agl-demo agl-netboot agl-appfw-smack agl-localdev
   ```

   **NOTE:**
   Running the `aglsetup.sh` script automatically places you in the
   working directory (i.e. `$AGL_TOP/build`).
   You can change this default behavior by adding the "-f" option to the
   script's command line.

   In the previous command, the "-m" option sets your machine to the previously
   defined `MACHINE` variable.
   The "-b" option defines your Build Directory, which is the
   default `$AGL_TOP/build`.
   Finally, the AGL features are provided to support building the AGL Demo image
   for the Renesas board.

   You can learn more about the AGL Features in the
   "[Initializing Your Build Environment](../image-workflow-initialize-build-environment.html)"
   section.

3. **Examine the Script's Log:**

   Read the script's log to be sure no errors occurred during your setup.
   For example, suppose the graphics drivers were missing or could not be extracted.
   In case of missing graphics drivers, you could notice an error message
   similar to the following:

   ```bash
   [snip]
   --- fragment /home/working/workspace_agl_master/meta-agl/templates/machine/h3ulcb/50_setup.sh
   /home/working/workspace_agl_master /home/working/workspace_agl_master/build_gen3
   The graphics and multimedia acceleration packages for
   the R-Car Gen3 board can be downloaded from:
    https://www.renesas.com/en-us/solutions/automotive/rcar-demoboard-2.html

   These 2 files from there should be store in your'/home/devel/Downloads' directory.
     R-Car_Gen3_Series_Evaluation_Software_Package_for_Linux-weston2-20170904.zip
     R-Car_Gen3_Series_Evaluation_Software_Package_of_Linux_Drivers-weston2-20170904.zip
   /home/working/workspace_agl_master/build_gen3
   --- fragment /home/working/workspace_agl_master/meta-agl/templates/base/99_setup_EULAconf.sh
   --- end of setup script
   OK
   Generating setup file: /home/working/workspace_agl_master/build_gen3/agl-init-build-env ... OK
   ------------ aglsetup.sh: Done
   [snip]
   ```

   If you encounter this issue, or any other unwanted behavior, you can fix the error
   mentioned, remove the `$AGL_TOP/build` directory, and then re-launch the
   `aglsetup.sh` again.

   You can find out more about any error by examining the `setup.log` file, which is in
   the `build/conf` folder.
   Here is an example that indicates the driver files could not be extracted from
   the downloads directory:

   ```bash
   [snip]

   ~/workspace_agl/build/conf $ cat setup.log
   --- beginning of setup script
   --- fragment /home/thierry/workspace_agl/meta-agl/templates/base/01_setup_EULAfunc.sh
   --- fragment /home/thierry/workspace_agl/meta-agl/templates/machine/m3ulcb/50_setup.sh
   ~/workspace_agl ~/workspace_agl/build
   ERROR: FILES "+/home/thierry/Downloads/R-Car_Gen3_Series_Evaluation_Software_Package_for_Linux-20180423.zip+" NOT EXTRACTING CORRECTLY
   ERROR: FILES "+/home/thierry/Downloads/R-Car_Gen3_Series_Evaluation_Software_Package_of_Linux_Drivers-20180423.zip+" NOT EXTRACTING CORRECTLY
   The graphics and multimedia acceleration packages for
   the R-Car Gen3 board BSP can be downloaded from:
   <https://www.renesas.com/us/en/solutions/automotive/rcar-download/rcar-demoboard-2.html>

   These 2 files from there should be stored in your
   '/home/thierry/Downloads' directory.
     R-Car_Gen3_Series_Evaluation_Software_Package_for_Linux-20180423.zip
     R-Car_Gen3_Series_Evaluation_Software_Package_of_Linux_Drivers-20180423.zip
   ERROR: Script /home/thierry/workspace_agl/build/conf/setup.sh failed
   [snip]
   ```

## 5. Checking Your Configuration

Users may want to check that the board is correctly selected in the environment:

```bash
grep -w -e "^MACHINE =" $AGL_TOP/build/conf/local.conf
  MACHINE = "h3ulcb"
or
  MACHINE = "m3ulcb"
or
  MACHINE = "h3-salvator-x"
```

Configure for Release or Development:

* development images contain extra tools for developer convenience, in particular:
  * a debugger (gdb)
  * some tweaks, including a disabled root password
  * a SFTP server
  * the TCF Agent for easier application deployment and remote debugging
  * some extra system tools (usb, bluetooth ...)
  * ...

We explicitely activate these debug facilities by specifying the “agl-devel agl-netboot” feature.

## Building the AGL Demo Platform for R-Car Starter Kit Gen3

### Build your image

The process to build an image is simple:

```bash
bitbake agl-demo-platform
```

You may need to install rpcgen to run this command.

When finished (it may take few hours), you should get the final result:

```bash
ls -l $AGL_TOP/build/tmp/deploy/images/$MACHINE
```

**Note**:

In case of failure of the build it is safe to first check that the Linux distribution chosen for your host has been validated for the current version of Yocto used by AGL.

## Booting AGL Image on R-Car Starter Kit Gen3 boards using a microSD card

To boot the board using a micro-SD card, there are two operations that must be done prior to first initial boot:

* Update all firmware on the device.
* Set up the board to boot on the SD-card.

For each subsequent build you only need to rewrite the SD-card with the new image.

### Firmware Update

This proceedure is done in two steps. The 'Sample Loader and MiniMonitor update' step only needs to be done once per device. The 'Firmware stack update' step is mandatory only if you use AGl Eel (version 5.0) or later.

#### Sample Loader and MiniMonitor update

Follow the documentation on the [eLinux.org wiki][R-car loader update] to update to at least version 3.02. This is mandatory to run AGL.

#### Firmware stack update

As an AArch64 platform, both **h3ulcb** and **m3ulcb** have a firmware stack divided in : **ARM Trusted Firmware**, **OP-Tee** and **U-Boot**.

If you use AGl Eel (version 5.0) or later, you must update the firmware using the following links to eLinux.org wiki: **[h3ulcb][R-car h3ulcb firmware update]** or **[m3ulcb][R-car m3ulcb firmware update]**.

The files listed in the eLinux.org wiki table will be found in the directory:

```bash
*\$AGL_TOP/build/tmp/deploy/images/$MACHINE*
```

The Salvator-X firmware update process is not documented on eLinux.

### Prepare the SD-card on the host

Plug the microSD card and get its associated device by either running *`dmesg | tail -15`* or *`lsblk`*, for example:

```bash
dmesg | tail -15

  [ 1971.462160] sd 6:0:0:0: [sdc] Mode Sense: 03 00 00 00
  [ 1971.462277] sd 6:0:0:0: [sdc] No Caching mode page found
  [ 1971.462278] sd 6:0:0:0: [sdc] Assuming drive cache: write through
  [ 1971.463870]  sdc: sdc1 sdc2
```

Here, the SD-card is attached to the device /dev/sdc.

```bash
lsblk

  NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
  sda      8:0    0 167,7G  0 disk
  ├─sda1   8:1    0   512M  0 part /boot/efi
  ├─sda2   8:2    0 159,3G  0 part /
  └─sda3   8:3    0   7,9G  0 part [SWAP]
  sdb      8:16   0 931,5G  0 disk
  └─sdb1   8:17   0 931,5G  0 part /media/storage
  sdc      8:32   1  14,9G  0 disk
  ├─sdc1   8:33   1    40M  0 part
  └─sdc2   8:34   1   788M  0 part
```

**IMPORTANT NOTE**: This is a critical operation, each computer is different and removable devices can change from time to time:
so you should repeat this operation each time you insert the microSD card to confirm the device name.

In the example above, we see:

* the first SATA drive as 'sda'.
* 'sdc' corresponds to the microSD card, and is also marked as removable device by *lsblk* which is a good confirmation.
* Your desktop system probably offers a choice to mount the SD-card automatically in some directory.
* In the next sample code, we'll suppose that the SD-card mount directory is stored in the variable $SDCARD.
* For example, if the microSD card is associated with device *sdc*:

Go to your build directory:

```bash
cd $AGL_TOP/build/tmp/deploy/images/$MACHINE
```

You can use bmaptool to copy the **.wic.xz** file to the storage device, discovered in the previous step:

```bash
bmaptool copy ./agl-demo-platform-$MACHINE.wic.xz $SDCARD
```

Or you can be uncompressed and written to the device:

```bash
  sudo umount /dev/sdc
  xzcat ./agl-demo-platform-$MACHINE.wic.xz | sudo dd of=$SDCARD bs=4M
  sync
```

### Booting the board

* Turn the board off using the power switch.
* Insert the microSD-card.
* Verify that you have plugged in, at least :
  * An external monitor on HDMI port
  * An input device (keyboard, mouse, touchscreen...) on USB port.

* Turn the board on using the power switch.
 After a few seconds, you'll see the AGL splash screen on the display and you'll be able to log in on the console terminal or in the graphic screen.

## Serial Console Setup

### Install a serial client on your computer

This can be “screen”, “picocom”, “minicom”.
The lighter of the 3 is “picocom” (it has less dependencies).

### Plug a USB cable from your computer to the serial CP2102 USB port (micro USB-A)

With “dmesg” you can check the device created for the serial link.
Usually, it's /dev/ttyUSB0 but the number may vary depending on other USB serial ports connected to the host.
To get it, you must switch the board on.
For example:

```bash
dmesg | tail
[2097783.287091] usb 2-1.5.3: new full-speed USB device number 24 using ehci-pci
[2097783.385857] usb 2-1.5.3: New USB device found, idVendor=0403, idProduct=6001
[2097783.385862] usb 2-1.5.3: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[2097783.385864] usb 2-1.5.3: Product: FT232R USB UART
[2097783.385866] usb 2-1.5.3: Manufacturer: FTDI
[2097783.385867] usb 2-1.5.3: SerialNumber: AK04WWCE
[2097783.388288] ftdi_sio 2-1.5.3:1.0: FTDI USB Serial Device converter detected
[2097783.388330] usb 2-1.5.3: Detected FT232RL
[2097783.388658] usb 2-1.5.3: FTDI USB Serial Device converter now attached to ttyUSB0
```

The link is attached to the device /dev/ttyUSB0.
It is time to launch your serial client.
Example:

```bash
picocom -b 115200 /dev/ttyUSB0
```

or

```bash
minicom -b 115200 -D /dev/ttyUSB0
```

or

```bash
screen /dev/ttyUSB0 115200
```

### Power on the board to see a shell on the console

* For machine h3ulcb:

```bash
NOTICE:  BL2: R-Car Gen3 Initial Program Loader(CA57) Rev.1.0.7
NOTICE:  BL2: PRR is R-Car H3 ES1.1
NOTICE:  BL2: LCM state is CM
NOTICE:  BL2: DDR1600(rev.0.15)
NOTICE:  BL2: DRAM Split is 4ch
NOTICE:  BL2: QoS is Gfx Oriented(rev.0.30)
NOTICE:  BL2: AVS setting succeeded. DVFS_SetVID=0x52
NOTICE:  BL2: Lossy Decomp areas
NOTICE:       Entry 0: DCMPAREACRAx:0x80000540 DCMPAREACRBx:0x570
NOTICE:       Entry 1: DCMPAREACRAx:0x40000000 DCMPAREACRBx:0x0
NOTICE:       Entry 2: DCMPAREACRAx:0x20000000 DCMPAREACRBx:0x0
NOTICE:  BL2: v1.1(release):41099f4
NOTICE:  BL2: Built : 19:20:52, Jun  9 2016
NOTICE:  BL2: Normal boot
NOTICE:  BL2: dst=0xe63150c8 src=0x8180000 len=36(0x24)
NOTICE:  BL2: dst=0x43f00000 src=0x8180400 len=3072(0xc00)
NOTICE:  BL2: dst=0x44000000 src=0x81c0000 len=65536(0x10000)
NOTICE:  BL2: dst=0x44100000 src=0x8200000 len=524288(0x80000)
NOTICE:  BL2: dst=0x49000000 src=0x8640000 len=1048576(0x100000)


U-Boot 2015.04 (Jun 09 2016 - 19:21:52)

CPU: Renesas Electronics R8A7795 rev 1.1
Board: H3ULCB
I2C:   ready
DRAM:  3.9 GiB
MMC:   sh-sdhi: 0, sh-sdhi: 1
In:    serial
Out:   serial
Err:   serial
Net:   Board Net Initialization Failed
No ethernet found.
Hit any key to stop autoboot:  0
=>
```

* For machine m3ulcb:

```
NOTICE:  BL2: R-Car Gen3 Initial Program Loader(CA57) Rev.1.0.14
NOTICE:  BL2: PRR is R-Car M3 Ver1.0
NOTICE:  BL2: Board is Starter Kit Rev1.0
NOTICE:  BL2: Boot device is HyperFlash(80MHz)
NOTICE:  BL2: LCM state is CM
NOTICE:  BL2: AVS setting succeeded. DVFS_SetVID=0x52
NOTICE:  BL2: DDR1600(rev.0.22)NOTICE:  [COLD_BOOT]NOTICE:  ..0
NOTICE:  BL2: DRAM Split is 2ch
NOTICE:  BL2: QoS is default setting(rev.0.17)
NOTICE:  BL2: Lossy Decomp areas
NOTICE:       Entry 0: DCMPAREACRAx:0x80000540 DCMPAREACRBx:0x570
NOTICE:       Entry 1: DCMPAREACRAx:0x40000000 DCMPAREACRBx:0x0
NOTICE:       Entry 2: DCMPAREACRAx:0x20000000 DCMPAREACRBx:0x0
NOTICE:  BL2: v1.3(release):4eef9a2
NOTICE:  BL2: Built : 00:25:19, Aug 25 2017
NOTICE:  BL2: Normal boot
NOTICE:  BL2: dst=0xe631e188 src=0x8180000 len=512(0x200)
NOTICE:  BL2: dst=0x43f00000 src=0x8180400 len=6144(0x1800)
NOTICE:  BL2: dst=0x44000000 src=0x81c0000 len=65536(0x10000)
NOTICE:  BL2: dst=0x44100000 src=0x8200000 len=524288(0x80000)
NOTICE:  BL2: dst=0x50000000 src=0x8640000 len=1048576(0x100000)


U-Boot 2015.04-dirty (Aug 25 2017 - 10:55:49)

CPU: Renesas Electronics R8A7796 rev 1.0
Board: M3ULCB
I2C:   ready
DRAM:  1.9 GiB
MMC:   sh-sdhi: 0, sh-sdhi: 1
In:    serial
Out:   serial
Err:   serial
Net:   ravb
Hit any key to stop autoboot:  0
=>
```

### Configure U-boot parameters

Follow the steps below to configure the boot from microSD card and to set screen resolution:

* Turn the board on using the power switch.
* Hit any key to stop autoboot (warning you have only few seconds).
* Type **printenv** to check if you have correct parameters for booting your board:
  * Example for a h3ulcb:

    ```
    => printenv
    baudrate=115200
    bootargs=console=ttySC0,115200 root=/dev/mmcblk1p1 rootwait ro rootfstype=ext4
    bootcmd=run load_ker; run load_dtb; booti 0x48080000 - 0x48000000
    bootdelay=3
    fdt_high=0xffffffffffffffff
    initrd_high=0xffffffffffffffff
    load_dtb=ext4load mmc 0:1 0x48000000 /boot/Image-r8a7795-h3ulcb.dtb
    load_ker=ext4load mmc 0:1 0x48080000 /boot/Image
    stderr=serial
    stdin=serial
    stdout=serial
    ver=U-Boot 2015.04 (Jun 09 2016 - 19:21:52)

    Environment size: 648/131068 bytes
    ```

  * Example for a m3ulcb:

    ```
    => printenv
    baudrate=115200
    bootargs=console=ttySC0,115200 root=/dev/mmcblk1p1 rootwait ro rootfstype=ext4
    bootcmd=run load_ker; run load_dtb; booti 0x48080000 - 0x48000000
    bootdelay=3
    fdt_high=0xffffffffffffffff
    filesize=cdeb
    initrd_high=0xffffffffffffffff
    load_dtb=ext4load mmc 0:1 0x48000000 /boot/Image-r8a7796-m3ulcb.dtb
    load_ker=ext4load mmc 0:1 0x48080000 /boot/Image
    stderr=serial
    stdin=serial
    stdout=serial
    ver=U-Boot 2015.04 (Nov 30 2016 - 18:25:18)

    Environment size: 557/131068 bytes
    ```

    * To boot on a sd card, it is recommended to set your environment using these commands :

    ```
    setenv bootargs console=ttySC0,115200 ignore_loglevel vmalloc=384M video=HDMI-A-1:1920x1080-32@60 root=/dev/mmcblk1p1 rw rootfstype=ext4 rootwait rootdelay=2
    setenv bootcmd run load_ker\; run load_dtb\; booti 0x48080000 - 0x48000000
    setenv load_ker ext4load mmc 0:1 0x48080000 /boot/Image
    ```

    * For machine h3ulcb (BSP >= 2.19):

    ```
    setenv load_dtb ext4load mmc 0:1 0x48000000 /boot/Image-r8a7795-es1-h3ulcb.dtb
    ```

    * For machine h3ulcb (BSP < 2.19):

    ```
    setenv load_dtb ext4load mmc 0:1 0x48000000 /boot/Image-r8a7795-h3ulcb.dtb
    ```

    * For machine m3ulcb:

    ```bash
    setenv load_dtb ext4load mmc 0:1 0x48000000 /boot/Image-r8a7796-m3ulcb.dtb
    ```

    * For machine m3ulcb with a kingfisher board:

    ```bash
    setenv load_dtb ext4load mmc 0:1 0x48000000 /boot/Image-r8a7796-m3ulcb-kf.dtb
    ```

    * For machine h3ulcb with a kingfisher board:

    ```bash
    setenv load_dtb ext4load mmc 0:1 0x48000000 /boot/Image-r8a7795-es1-h3ulcb-kf.dtb
    ```

    * Finally save boot environment:

    ```bash
    saveenv
    ```

* Now you can boot:

```
run bootcmd
```

### Console boot

After booting, you should see the wayland display on the external monitor and a login prompt on the console, such as:

* For machine h3ulcb:

```bash
Automotive Grade Linux ${AGL_VERSION} h3ulcb ttySC0

h3ulcb login: root
```

* For machine m3ulcb:

```bash
Automotive Grade Linux ${AGL_VERSION} m3ulcb ttySC0

m3ulcb login: root
```

Logging in on the console is easy:

* login is 'root'
* password is empty (not asked)

### Network access

If the board is connected to a local network using ethernet and if a DHCP server is able to distribute IP addresses,
you can then determine the Gen3 board IP address and log in using ssh:

```bash
m3ulcb login: root
Last login: Tue Dec  6 09:55:15 UTC 2016 on tty2
root@m3ulcb:~# ip -4 a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
3: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    inet 10.0.0.27/24 brd 10.0.0.255 scope global eth0
       valid_lft forever preferred_lft forever
root@m3ulcb:~#
```

Here, IP address is 10.0.0.27. Logging in using SSH is easy:

```bash
$ ssh root@10.0.0.27
Last login: Tue Dec  6 10:01:11 2016 from 10.0.0.13
root@m3ulcb:~# cat /etc/os-release
ID="poky-agl"
NAME="Automotive Grade Linux"
VERSION="3.0.0+snapshot-20161202 (chinook)"
VERSION_ID="3.0.0-snapshot-20161202"
PRETTY_NAME="Automotive Grade Linux 3.0.0+snapshot-20161202 (chinook)"
```

## More Documentation

Detailed guides on how to build AGL for Renesas boards and using AGL SDK inside a ready-to-use Docker container:

* [AGL-Devkit-Build-your-1st-AGL-Application.pdf][Iot.bzh AGL-Devkit-Build-your-1st-AGL-Application]
 Generic guide on how to build various application types (HTML5, native, Qt, QML, …) for AGL.
* [AGL-Devkit-HowTo_bake_a_service.pdf][Iot.bzh AGL_Phase2-Devkit-HowTo_bake_a_service]
 Generic guide on how to add a new service in the BSP.
* [AGL-Kickstart-on-Renesas-Porter-Board.pdf][Iot.bzh AGL-Kickstart-on-Renesas-Porter-Board]
* [AGL-Devkit-Image-and-SDK-for-Porter.pdf][Iot.bzh AGL-Devkit-Image-and-SDK-for-Porter]
* [AGL Developer Website](http://docs.automotivelinux.org)

[R-car m3ulcb]: http://elinux.org/R-Car/Boards/M3SK
[R-car m3ulcb firmware update]: https://elinux.org/R-Car/Boards/M3SK#Flashing_firmware
[R-car h3ulcb]: http://elinux.org/R-Car/Boards/H3SK
[R-car h3ulcb firmware update]: https://elinux.org/R-Car/Boards/H3SK#Flashing_firmware
[R-car salvator-x]: https://elinux.org/R-Car/Boards/Salvator-X
[R-car loader update]: http://elinux.org/R-Car/Boards/Kingfisher#How_to_update_of_Sample_Loader_and_MiniMonitor
[R-car Kingfisher]: https://elinux.org/R-Car/Boards/Kingfisher
[R-car yocto]: http://elinux.org/R-Car/Boards/Yocto-Gen3
[rcar Linux Drivers]: https://www.renesas.com/solutions/automotive/rcar-download/rcar-demoboard.html
[rcar Linux Drivers 2]: https://www.renesas.com/en-us/solutions/automotive/rcar-download/rcar-demoboard-2.html
[Iot.bzh AGL-Kickstart-on-Renesas-Porter-Board]: http://docs.automotivelinux.org/docs/devguides/en/dev/reference/iotbzh2016/sdk/AGL-Kickstart-on-Renesas-Porter-board.pdf
[Iot.bzh AGL-Devkit-Image-and-SDK-for-Porter]: http://docs.automotivelinux.org/docs/devguides/en/dev/reference/iotbzh2016/sdk/AGL-Devkit-Image-and-SDK-for-porter.pdf
[Iot.bzh AGL-Devkit-Build-your-1st-AGL-Application]: http://docs.automotivelinux.org/docs/devguides/en/dev/reference/iotbzh2016/sdk/AGL-Devkit-Build-your-1st-AGL-Application.pdf
[Iot.bzh AGL_Phase2-Devkit-HowTo_bake_a_service]: http://docs.automotivelinux.org/docs/devguides/en/dev/reference/iotbzh2016/bsp/AGL_Phase2-Devkit-HowTo_bake_a_service.pdf
