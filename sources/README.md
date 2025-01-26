Clone Linux kernel
```plaintext
git clone https://github.com/xilinx/linux-xlnx.git
```
Checkout fixed version (Linux kernel)
```plaintext
cd linux-xlnx && git checkout xilinx-v<version(2022.2 2023.2 ...)> && cd ..
```
Prepare the environment (**if you open a new console you will have to export the environment again**)
```plaintext
export CROSS_COMPILE=arm-linux-gnueabihf-
source /tools/Xilinx/Vivado/<version>/settings64.sh
export ARCH=arm
export PATH="/path_to_u-boot/u-boot-xlnx/tools":$PATH
```
Go to Linux kernel folder:

Set the Zynq 7000 defconfig:
```plaintext
make xilinx_zynq_defconfig
```
If you want to modifie something:
```plaintext
make menuconfig
```
Tell to build a uImage, the load address and build it:
```plaintext
make UIMAGE_LOADADDR=0x8000 uImage -j $(nproc)
```