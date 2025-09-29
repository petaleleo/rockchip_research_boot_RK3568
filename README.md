# rockchip_research_boot_RK3568
Research booting Radxa Rock 3A (RK3568)

![alt text](https://github.com/petaleleo/rockchip_research_boot_RK3568/blob/main/radxa3a_2.webp?raw=true)


links:

1. https://docs.radxa.com/en/rock3/rock3a
2. https://wiki.radxa.com/Rock3/3a
3. https://docs.u-boot.org/en/latest/board/rockchip/rockchip.html#rockchip-boards
4. https://electronix.ru/forum/topic/181090-zapusk-chipov-rockchip-bez-proprietarnyh-blobov/
5. https://opensource.rock-chips.com/wiki_Boot_option

# Boot flow 
from here: https://opensource.rock-chips.com/wiki_Boot_option
This chapter introduce the generic boot flow for Rockchip Application Processors, including the detail about what image we may use in Rockchip platform for kind of boot path:

- use U-Boot TPL/SPL from upsream or rockchip U-Boot, fully source code;
- use Rockchp idbLoader which is combinded by Rockchip ddr init bin and miniloader bin from Rockchip rkbin project;

```
+--------+----------------+----------+-------------+---------+
| Boot   | Terminology #1 | Actual   | Rockchip    | Image   |
| stage  |                | program  |  Image      | Location|
| number |                | name     |   Name      | (sector)|
+--------+----------------+----------+-------------+---------+
| 1      |  Primary       | ROM code | BootRom     |         |
|        |  Program       |          |             |         |
|        |  Loader        |          |             |         |
|        |                |          |             |         |
| 2      |  Secondary     | U-Boot   |idbloader.img| 0x40    | pre-loader
|        |  Program       | TPL/SPL  |             |         |
|        |  Loader (SPL)  |          |             |         |
|        |                |          |             |         |
| 3      |  -             | U-Boot   | u-boot.itb  | 0x4000  | including u-boot and atf
|        |                |          | uboot.img   |         | only used with miniloader
|        |                |          |             |         |
|        |                | ATF/TEE  | trust.img   | 0x6000  | only used with miniloader
|        |                |          |             |         |
| 4      |  -             | kernel   | boot.img    | 0x8000  |
|        |                |          |             |         |
| 5      |  -             | rootfs   | rootfs.img  | 0x40000 |
+--------+----------------+----------+-------------+---------+
```

Then when we talking about boot from eMMC/SD/U-Disk/net, they are in different concept:

Stage 1 is always in boot rom, it loads stage 2 and may load stage 3(when SPL_BACK_TO_BROM option enabled).
- Boot from SPI flash means firmware for stage 2 and 3(SPL and U-Boot only) in SPI flash and stage 4/5 in other place;
- Boot from eMMC means all the firmware(including stage 2, 3, 4, 5) in eMMC;
- Boot from SD card means all the firmware(including stage 2, 3, 4, 5) in SD card;
- Boot from U-Disk means firmware for stage 4 and 5(not including SPL and U-Boot) in Disk, optionally only including stage 5;
- Boot from net/tftp means firmeware for stage 4 and 5(not including SPL and U-Boot) on the network;

![alt text](https://github.com/petaleleo/rockchip_research_boot_RK3568/blob/main/894px-Rockchip_bootflow20181122.jpg?raw=true)

# Others

```BASH
!/bin/sh

./tools/mkimage -n rk3308 -T rksd -d bin/rk33/rk3308_ddr_589MHz_uart4_m0_v2.07.bin idbloader.img # Блоб
cat Firefly_Linux_SDK/rk3308_linux_release_v1.5.0a_20221212/u-boot/spl/u-boot-spl.bin >> idbloader.img
cp Firefly_Linux_SDK/rk3308_linux_release_v1.5.0a_20221212/u-boot/u-boot.img .
rkdeveloptool db Firefly_Linux_SDK/rk3308_linux_release_v1.5.0a_20221212/rkbin/rk3308_loader_uart4_v2.07.139.bin
sleep 1
rkdeveloptool wl 0x40 idbloader.img
sleep 1
rkdeveloptool wl 0x4000 u-boot.img
sleep 1
rkdeveloptool rd


./tools/mkimage -n rk3308 -T rksd -d Firefly_Linux_SDK/rk3308_linux_release_v1.5.0a_20221212/u-boot/tpl/u-boot-tpl-dtb.bin idbloader.img
cat Firefly_Linux_SDK/rk3308_linux_release_v1.5.0a_20221212/u-boot/spl/u-boot-spl-dtb.bin >> idbloader.img
```
