# AXI DMA with Petalinux 

## Introduction
This repo contains the setup for implementing AXI DMA in Xilinx/AMD devices, for performing Memory Mapo MM2S and S2MM operations, using a User space mappable DMA Buffer ```u-dma-buf``` from [https://github.com/ikwzm/udmabuf](https://github.com/ikwzm/udmabuf). An example design is also provided with a simple loopback setup.

## Vivado Project

## Petalinux Setup
Add the generated ```.xsa``` file generated from the Vivado project:
```bash
petalinux-config --get-hw-description=<xsa_file_path>
```
Build the Petalinux project
```bash
petalinux-build
```

Package the Petalinux project
```bash
petalinux-package --boot --fsbl images/linux/zynqmp_fsbl.elf --u-boot --force --fpga <bitstream_path> --output images/linux/BOOT.BIN
```



## Loopback Example
