# Building a Minimal Debian Rootfs with debootstrap

## Introduction

**debootstrap** is a Linux tool used to create a minimal and functional Debian filesystem (or one of its derivatives, such as Ubuntu). It’s especially useful for building customized systems, lightweight environments, or base configurations that you can use as the root filesystem (rootfs) on embedded devices, such as the Xilinx Zynq-7000.

Install the required tools:
```plaintext
sudo apt install debootstrap qemu-user-static
```
Create a directory to hold the root filesystem::
```plaintext
mkdir -p ~/Documents/debootstrap/
```
Use debootstrap to install Debian into the directory (rootfs) you just created:
```plaintext
sudo debootstrap --arch=armhf --foreign <distro> ~/Documents/debootstrap/ http://deb.debian.org/debian #debian
```
```plaintext
sudo debootstrap --arch=armhf --foreign jammy ~/Documents/debootstrap/ http://ports.ubuntu.com/ubuntu-ports/ #ubuntu
```
Copy qemu-arm-static into the rootfs for ARM emulation:
```plaintext
sudo cp /usr/bin/qemu-arm-static ~/Documents/debootstrap/usr/bin/
```
Enter the chroot environment to finish the debootstrap process:
```plaintext
sudo chroot ~/Documents/debootstrap /debootstrap/debootstrap --second-stage
```
Set hostname:
```plaintext
echo "zynq" | sudo tee ~/Documents/debootstrap/etc/hostname
```
Access the chroot environment

From here you can configure diferent stuff of the rootfs apt network etc

```plaintext
sudo chroot ~/Documents/debootstrap/
```
Set the root password:
```plaintext
passwd root
```
Example debian network "/etc/network/interfaces"
```plaintext
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
        address 192.168.11.100
        netmask 255.255.255.0
        gateway 192.168.11.1
        dns-nameservers 8.8.8.8 8.8.4.4
```
To exit chroot:     
```plaintext
exit
```
Coppy the rootfs to your SD:
```plaintext
sudo rsync -aHAX ~/Documents/debootstrap /path_to_SD/rootfs/
```
```plaintext
sync
```

unmount de SD and extract it.

For more info:

https://wiki.debian.org/Debootstrap

