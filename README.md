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
Prepare the environment <span style="color:red">(if you open a new console you will have to export the environment again)</span>
```plaintext
export CROSS_COMPILE=arm-linux-gnueabihf-
source /tools/Xilinx/Vivado/2022.2/settings64.sh
```
Generate your Vivado Zynq project:
  - Generate the bitstream.
  - Export hardware (.xsa file).

Generate your FSBL with VITIS IDE
1. Create a new platform project using the exported hardware XSA from Vivado.
    - Create a new platform  
    ![Create a new platform](create_a_new_platform.png)
2. Modify the BSP settings for the standalone OS to include the xilffs library.
    - Add xilffs library  
    ![Add xilffs library 1](xilffs_library1.png)  
    ![Add xilffs library 2](xilffs_library2.png)
3. Build BSP sources.
4. Create a new application project (name it fsbl) on top of the platform that we have just created,
    selecting Zynq FSBL as the application template.
5. Change the C/C++ Build Settings for the FSBL application (See Figure 7) to include FSBL_DEBUG_INFO.
    This allows the FSBL to print debug messages during the boot process.
    - Add xilffs library  
    ![Add xilffs library 1](xilffs_library1.png)  
    ![Add xilffs library 2](xilffs_library2.png)
6. Build the FSBL application.


Generate device tree blob with Vitis
1. Add device tree utils repo: In Xilinx tab, select Software Repositories and add it.
    Add device tree utils repo.
    ![Add device tree utils repo](Add_device_tree.png)
2. Refresh repositories: In Xilinx, Scan Repositories.
3. Generate device tree: In Xilinx, Generate Device Tree.
4. Apply now the C/C++ preprocessor to handle includes:
```plaintext
"$CROSS_COMPILE"gcc -E -nostdinc -x assembler-with-cpp -o "path/out.dts" "path/in.dts"
```
    5. Compile now the device tree:
```plaintext
dtc -I dts -O dtb -o "path/out.dtb" "path/in.dts"
```

Build the U-Boot:
  - Place the DTS file in the path: u-boot-xlnx/arch/arm/dts/$(board-name).dts
  - Add the DTB path name to the Makefile: u-boot-xlnx/arch/arm/dts/Makefile under dtb-$(CONFIG_ARCH_ZYNQ) group += \ -> $(board-name).dtb
  - Add to u-boot-xlnx/configs/xilinx_zynq_virt_defconfig: under CONFIG_OF_LIST label -> CONFIG_OF_LIST="..... $(board-name)"
  - export DEVICE_TREE=$(board-name)
  - make xilinx_zynq_virt_defconfig
  - make -j $(nproc)