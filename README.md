# kernel_programming

## Summary of Setup and Execution Steps

### Overview

This guide walks through setting up a virtualized environment using QEMU to run a Linux kernel on a RISC-V architecture, leveraging U-Boot as the bootloader and Buildroot for toolchain generation. The tutorial covers installation of prerequisites, compilation, and execution steps for each component.

---

## Page 1: Introduction to Components

### Key Components
- **QEMU:** Emulator for creating virtual RISC-V hardware environments.
- **U-Boot:** Bootloader that initializes the virtual hardware and loads the Linux kernel.
- **RISC-V:** Target architecture for QEMU and the software stack.
- **Linux Kernel:** OS that runs on the virtualized RISC-V hardware.
- **Buildroot:** Tool for building cross-compilation toolchains and Linux system images.
- **OpenSBI:** Provides an interface between the Linux OS and RISC-V hardware for low-level tasks.

### Flow of Execution
1. **QEMU** emulates the RISC-V hardware environment.
2. **U-Boot** initializes the hardware and loads the Linux kernel.
3. **Buildroot** creates the toolchain and system image.
4. **OpenSBI** interfaces with RISC-V hardware, managing low-level operations.

---

## Page 2: Setup on Ubuntu 20.04

### Install Prerequisites

```bash
sudo apt update && sudo apt install build-essential libncurses-dev rsync git ninja-build libglib2.0-dev libpixman-1-dev bison flex libssl-dev wget unzip bc file
```

### Directory Preparation

```bash
mkdir tech.io && cd tech.io
```

### Install Toolchain with Buildroot

```bash
wget https://buildroot.org/downloads/buildroot-2023.08.tar.gz
tar -xf buildroot-2023.08.tar.gz
cd buildroot-2023.08
make menuconfig
```

#### Configuration in Menuconfig:
**Select the following options:**
- **Target options**:
  - Target Architecture > **RISCV**
  - Target Architecture Size > **64-bit**
- **Toolchain**:
  - C library > **musl**
  - Kernel Headers > **Linux 6.4.x kernel headers**
  - Binutils Version > **binutils 2.41**
  - GCC compiler Version > **gcc 13.x**
Exit and save the configuration. Build the toolchain:

```bash
make sdk -j$(nproc)
cd ..
mkdir toolchain && cd toolchain
tar -xf ../buildroot-2023.08/output/images/riscv64-buildroot-linux-musl_sdk-buildroot.tar.gz
riscv64-buildroot-linux-musl_sdk-buildroot/relocate-sdk.sh
cd ..
printf "export PATH=~/tech.io/toolchain/riscv64-buildroot-linux-musl_sdk-buildroot/bin:\$PATH\n" > tech.io-env.sh
source tech.io-env.sh
```

### Verify Installation

```bash
riscv64-linux-gcc --version
```

---

## Page 3: Install QEMU

### Install via Package Manager (Recommended)

```bash
sudo apt update
sudo apt install qemu-user qemu-system
qemu-system-riscv64 --version
```

### Verify Supported Machines

```bash
qemu-system-riscv64 -M ?
```

---

# Example Day 1: Build Hello World by using
```bash
sudo apt install vim
cd /home/htritai/tech.io
vim hello.c
```
```c
// hello.c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

```bash
## Build hello.c
riscv64-linux-gcc -o hello hello.c -static
## Run hello
qemu-riscv64 hello
```
## Page 4: Install U-Boot

### Set Environment Variable

```bash
echo "export CROSS_COMPILE=riscv64-linux-" >> tech.io-env.sh
source tech.io-env.sh
```

### Build U-Boot

```bash
git clone https://source.denx.de/u-boot/u-boot.git
cd u-boot
git checkout v2023.07.02
make qemu-riscv64_smode_defconfig
make -j$(nproc)
```

### Output
- **Binary Location:** `./u-boot.bin`

---

## Page 5: Install OpenSBI

### Build OpenSBI

```bash
git clone https://github.com/riscv-software-src/opensbi.git
cd opensbi
git checkout v1.2
make PLATFORM=generic FW_PAYLOAD_PATH=../u-boot/u-boot.bin -j$(nproc)
```

### Output
- **Firmware Location:** `build/platform/generic/firmware/fw_payload.elf`

---

## Page 6: Run U-Boot in QEMU

### Create QEMU Run Script

```bash
cd ~/tech.io
echo '#!/usr/bin/bash' > run-u-boot.sh
echo 'qemu-system-riscv64 -smp 2 \' >> run-u-boot.sh
echo '-m 1G \' >> run-u-boot.sh
echo '-nographic \' >> run-u-boot.sh
echo '-machine virt \' >> run-u-boot.sh
echo '-bios ./opensbi/build/platform/generic/firmware/fw_payload.elf' >> run-u-boot.sh
chmod +x run-u-boot.sh
```

### Run QEMU Script

```bash
./run-u-boot.sh
```

### Expected Output

```
U-Boot 2023.07.02 ...
...
Hit any key to stop autoboot:  0
=>
```

---

## Page 7: Install Linux Kernel

### Environment Setup

```bash
echo "export ARCH=riscv" >> tech.io-env.sh
source tech.io-env.sh
```

### Build Linux Kernel

```bash
git clone https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git
cd linux
git checkout v6.2
make defconfig
make -j$(nproc)
```

### Verify Build

```bash
grep --color=always -ni 'riscv' .config
ls arch/riscv/boot -lSh
```
If the configuration option `CONFIG_RISCV=y` is present, it indicates that the kernel was configured and built for RISC-V. This happens because the `ARCH` environment variable was set to `riscv` when the `.config` file was generated.
### Output Files

- **Image:** `arch/riscv/boot/Image`
- **Image.gz:** `arch/riscv/boot/Image.gz`

---

## Page 8: Create Disk Image

### Install `parted`

```bash
sudo apt-get update
sudo apt-get install -y parted
```

### Create Disk Image

```bash
dd if=/dev/zero of=disk.img bs=1M count=128
```

### Create Partitions

```bash
sudo parted disk.img mklabel gpt
sudo losetup --find --show --partscan disk.img # Check output: example /dev/loop10
sudo parted --align minimal /dev/loop10 mkpart primary ext4 0 50%
sudo parted --align minimal /dev/loop10 mkpart primary ext4 50% 100%
sudo mkfs.ext4 /dev/loop10p1
sudo mkfs.ext4 /dev/loop10p2
sudo parted /dev/loop10 set 1 boot on
```

### Copy Kernel to Disk

```bash
sudo mkdir /mnt/uboot
sudo mount /dev/loop10p1 /mnt/uboot
sudo cp linux/arch/riscv/boot/Image /mnt/uboot
sudo umount /mnt/uboot
# sudo losetup -d /dev/loop10 # maybe not use it
```

---

## Page 9: Modify QEMU Script

### Update `run-u-boot.sh`

```bash
#!/usr/bin/bash

qemu-system-riscv64 -smp 2 \
-m 1G \
-nographic \
-machine virt \
-bios ./opensbi/build/platform/generic/firmware/fw_payload.elf \
-blockdev driver=file,filename=./disk.img,node-name=disk \
-device virtio-blk-device,drive=disk
```

### Run QEMU Script

```bash
./run-u-boot.sh
```

### Verify Disk Detection

```
Device 0: QEMU VirtIO Block Device
            Type: Hard Disk
...
```

---

## Page 10: Configure U-Boot for Kernel Boot

### Set U-Boot Boot Command

```bash
cd u-boot
make menuconfig
```

- **Boot Command:** `ext4load virtio 0:1 84000000 Image; booti 0x84000000 - ${fdtcontroladdr}`
- **Boot Arguments:** `root=/dev/vda2 rootwait console=ttyS0 earlycon=sbi`

### Rebuild U-Boot and OpenSBI

```bash
make -j$(nproc)
cd ../opensbi/
make PLATFORM=generic FW_PAYLOAD_PATH=../u-boot/u-boot.bin -j$(nproc)
cd ..
./run-u-boot.sh
```

### Outcome

- **U-Boot** loads and attempts to boot the Linux kernel from the `disk.img`.

---

## Page 11: Build Root Filesystem with BusyBox

### Install BusyBox

```bash
wget https://busybox.net/downloads/busybox-1.33.0.tar.bz2
tar xf busybox-1.33.0.tar.bz2
cd busybox-1.33.0
make allnoconfig
make menuconfig
```

### BusyBox Configuration

  - **Enable Static Binary:**
    - Navigate to `Settings` → `Build Options`.
    - Select `Build static binary (no shared libs)`.


  - **Set Cross Compiler Prefix:**
    - Navigate to `Settings` → `Build Options`.
    - Set `Cross compiler prefix` to `riscv64-linux-`.
   - **Enter Shell -> Set ash:**
   - **Enter Init Utilities -> Set Init**
   - **Enter Init Utilities -> Set halt**

   - **Enter Linux System Utilities -> Set mount, remove Support -f (fake mount) and Support -v (verbose)**
   - **Enter CoreUtils -> Set cat, echo, mkdir, ls**
   - **Enter Process Utilities -> Set uptime**
   - **Enter Editor -> Set vi**
   - **Enter Networking -> Set httpd (32 kb), remove Enaable -u <user> option, Enable HTTP authentication, Support reverse proxy, Support GZIP content encoding, Support caching via ETag header.**
   - **Enter Networking -> Set ifconfig**

### Build and Install BusyBox

```bash


make -j$(nproc)
make install
```

### Copy BusyBox to Root Filesystem

```bash
sudo mkdir /mnt/rootfs
sudo mount /dev/loop10p2 /mnt/rootfs
sudo mkdir /mnt/rootfs/dev /mnt/rootfs/proc /mnt/rootfs/sys
sudo rsync -aH _install/ /mnt/rootfs/
sudo umount /mnt/rootfs
```

### Run U-Boot and Access BusyBox

```bash
./run-u-boot.sh
```

- Inside BusyBox, mount proc:

```bash
mount -t proc none /proc
```

---

This guide provides a comprehensive setup to emulate a RISC-V environment with QEMU, load U-Boot, and boot a Linux kernel, including a simple root filesystem with BusyBox. Each step is tailored to ensure correct installation and execution within the virtual environment.
