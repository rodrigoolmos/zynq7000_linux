Clone buildroot repo:
```plaintext
git clone https://github.com/buildroot/buildroot.git
```
Set the Zynq 7000 defconfig:
```plaintext
make zynq_zc702_defconfig
```
To make general configurations use
```plaintext
make menuconfig
```
To configure de linux kernel use
```plaintext
make linux-menuconfig
```
To configure uboot configurations use:
```plaintext
make uboot-menuconfig
```
To compile the SO use:
```plaintext
 make -j $(nproc)
```
This will generate different files in output/images/. Use the ones that you need. For example, you could flash the SD card with the image sdcard.img and replace the files relative to your system:
 - BOOT.bin
 - devicetree.dtb
 - extlinux/extlinux.conf
