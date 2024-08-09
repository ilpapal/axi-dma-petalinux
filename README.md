# AXI DMA with Petalinux 

## Introduction
This repo contains the setup for implementing AXI DMA in Xilinx/AMD devices, for performing Memory Mapo MM2S and S2MM operations, using a User space mappable DMA Buffer ```u-dma-buf``` from [https://github.com/ikwzm/udmabuf](https://github.com/ikwzm/udmabuf). An example design is also provided with a simple loopback setup.

## Vivado Project

## Petalinux Setup

### Device Tree
The device tree described by the file ```<petalinux_project>/project-spec/meta-user/recipes-bsp/device-tree/files/system-user.dtsi``` must contain the following, for specifying the AXI DMA Buffers and DMA Controllers, settings for having a persistent filesystem in the SD card in an ext4 format, as well as some settings for disabling write protection of the SD (see note).
```
/include/ "system-conf.dtsi"
/ {
    chosen {
        bootargs = " earlycon console=ttyPS0,115200 clk_ignore_unused uio_pdrv_genirq.of_id=generic-uio root=/dev/mmcblk0p2 rootfstype=ext4 rw rootwait";
    };

    udmabuf@0x00 {
        compatible = "ikwzm,u-dma-buf";
        device-name = "udmabuf0";
        minor-number = <0>;
        size = <0x4000000>;
        sync-mode = <1>;
        sync-always;
    };

    udmabuf@0x01 {
        compatible = "ikwzm,u-dma-buf";
        device-name = "udmabuf1";
        minor-number = <1>;
        size = <0x4000000>;
        sync-mode = <1>;
        sync-always;
    };
};

&axi_dma_0{
    compatible = "generic-uio";
};

&axi_dma_1{
    compatible = "generic-uio";
};

&sdhci1 {
	no-1-8-v;
	disable-wp;
};
```
_Note: The last node for the SD card was added for the RFSoC ZCU111 board, to avoid kernel panic at boot. Source: [AMD/Xilinx Forum](https://support.xilinx.com/s/question/0D54U000084fxFeSAI/error-card-did-not-respond-to-voltage-select-while-booting-through-emmc-zynq-ultrascale?language=zh_CN)_ 

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
