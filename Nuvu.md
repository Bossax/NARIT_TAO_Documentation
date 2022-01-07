# Nuvu Camera
This page is to provide simple guide to install and work with HNu camera specific to NARIT conditions. For detialed explanation, refer to **HNu camera user manual** and **Nuvu Cameras installation guide**.

## Installtion on a Linux Mint machine
HNu128 is connected to a computer via a PCI card which talks to the frame grabber. The installation steps on Linux are explained in the **Installation Guide**, though there are some modifications to be done to make it work on Linux Mint

1. After unpack the .tgz file, we need to edit lines in the file *.osVersionbash*

* uncomment the DISTRO linux mint line
```shell
elif cat /etc/*-release | grep DISTRIB_ID | grep -i linuxmint  1>  /dev/null # TODO: ?
then # LinuxMint
 	DISTRO=ubuntu # TODO: Ubuntu-based but different release labelling, so we need to distinguish them below ... and beyond, for Pleora does not support Linux explicitly
```

* check compatible versions of Ubuntu and Linux Mint pair;  eg. Linux Mint 20.2
is based on Ubuntu Focal (Version 20.04.3). edit the version number
```shell
elif 	cat /etc/*-release | grep DISTRIB_RELEASE | grep -e "20.[[:digit:]]" 1>  /dev/null
then # release 20.something found
  OS_VERSION=$UBT_20
  ```


2. Then, run **installNuvuCameras.sh** script
```shell
 sudo ./installNuvuCameras.sh
```
3. When finished, force install edt plug in via NuvuInstaller script
```shell
sudo ./nc-install-edt.bash FORCE
```
4. Frame grabber driver needs installing. The frame grabber is of EDT, Inc.
The version that works is 5.6.0 which can be downloaded from https://edt.com/file-category/pdv/
* change the permission to run the file before running it
```shell $
chmod 0755 EDTpdv_lnx_5.6.0.0.run
```
* After installtion finished , change directory to `NuvuSW_4.2.1/NuvuInstaller/Dependencies/Third-party/EDTpdv` and copy  aconfiguration file `.cfg` to `/opt/EDTpdv/camera_config/`

5. Test if the frame grabber is working
* initialize the camera
```shell
$ sudo /opt/EDTpdv/initcam -f /opt/EDTpdv/camera_config/nc_16cl.cfg
```
* take a picture
```shell
/opt/EDTpdv/take -b /tmp/mypicture.bmp
```
6. Build the EDTpdv dynamic ibrary:
```shell
sudo make -B -C /opt/EDTpdv/CFLAGS=-fPIC libpdv.so
```

7. Remove virtual camera plug in. As of August 2021 , the virtual camera plug in is not working and has to be excluded
>The virtual plugin  has an unfortunate bug in version 4.2.1 causing the automatic detection of cameras to loop forever.
You should remove the symbolic link :
	`/opt/NuvuCameras/Plugins/nc_grab_virtual`from the `/opt/NuvuCameras/Plugins directory`.
> Alternatively, the call to `ncCamOpen()` in the examples could be modified to use
  `unit = EDT_CL`
to restrict the SDK to interrogating just the EDT framegrabber


  **note**
  *NuPixel* software has not been successfully installed on Linux. There are issues
with QT libraries.


---
## Nuvu SDK
