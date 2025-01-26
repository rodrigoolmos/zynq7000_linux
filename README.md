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
Prepare the environment (**if you open a new console you will have to export the environment again**)
```plaintext
export CROSS_COMPILE=arm-linux-gnueabihf-
source /tools/Xilinx/Vivado/2022.2/settings64.sh
```
Generate your Vivado Zynq project:
  - Generate the bitstream.
  - Export hardware (.xsa file).

Generate your FSBL with VITIS IDE (fsbl.elf)
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


Generate device tree blob with Vitis (devicetree.dtb)
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

Build the U-Boot: (u-boot.elf)
  - Place the DTS file in the path: u-boot-xlnx/arch/arm/dts/$(board-name).dts
  - Add the DTB path name to the Makefile: u-boot-xlnx/arch/arm/dts/Makefile under dtb-$(CONFIG_ARCH_ZYNQ) group += \ -> $(board-name).dtb
  - Add to u-boot-xlnx/configs/xilinx_zynq_virt_defconfig: under CONFIG_OF_LIST label -> CONFIG_OF_LIST="..... $(board-name)"
  - export DEVICE_TREE=$(board-name)
  - make xilinx_zynq_virt_defconfig
  - make -j $(nproc)

Build Linux kernel (uImage)
  - [Build linux kernel from sources](sources/README.md)
  - [Build linux kernel buildroot](buildroot/README.md)

Create the boot image (BOOT.bin)
To create a boot image, you can use the Create Boot Image wizard in the Vitis IDE or the Bootgen
command line tool (the Create Boot Image Wizard also calls the Bootgen tool). The principle function
of the Create Boot Image wizard or Bootgen is to integrate the partitions (hardware-bitstream and
software) in the proper format. It allows you to specify security options and also to create cryptographic
keys.
Functionally, Bootgen uses a BIF (Bootgen image format) file as an input and generates a single
file image in binary BIN or MCS format. It can program non-volatile memories such as QSPI and SD
cards. The Bootgen GUI facilitates the creation of a BIF input file.
In this case, we will use the Vitis IDE again to create the boot image. We must follow this
procedure:
1. Go to Xilinx, Create boot image, Zynq and Zynq Ultrascale.
2. Select architecture ”Zynq”, make sure that ’Create new BIF file’ is checked, and set ”/path_to/lab5.bif” as the ’Output BIF file path’.
3. In the boot image partitions:
  1. Add FSBL executable (”/path_to/fsbl.elf”) as bootloader.
  2. Add PL bitstream (”/path_to/system wrapper.bit”) as datafile.
  3. Add U-Boot executable (”/path_to/u-boot.elf”) as datafile. All those
4. Click on ’Create Image’