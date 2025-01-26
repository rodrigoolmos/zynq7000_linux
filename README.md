# zynq7000_linux
This repository contains how to boot linux on a zynq

Clone U-Boot Second Stage BootLoader (SSBL)
```plaintext
git clone https://github.com/xilinx/u-boot-xlnx.git
```
Checkout fixed version (Vivado version)
```plaintext
cd u-boot-xlnx && git checkout xilinx-v<version(2022.2 2023.2 ...)> && cd ..
```
Clone Device tree utilities
```plaintext
git clone https://github.com/xilinx/device-tree-xlnx.git
```
Checkout fixed version (Vivado version)
```plaintext
cd device-tree-xlnx && git checkout xilinx_v<version(2022.2 2023.2 ...)> && cd ..
```
Install the device tree compiler
```plaintext
sudo -H apt-get install device-tree-compiler
```
Prepare the environment
```plaintext
export CROSS_COMPILE=arm-linux-gnueabihf-
source /tools/Xilinx/Vivado/2022.2/settings64.sh
```
