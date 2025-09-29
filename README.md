# rockchip_research_boot_RK3568
Research booting Radxa Rock 3A (RK3568)

![alt text](https://github.com/petaleleo/rockchip_research_boot_RK3568/blob/main/radxa3a_2.webp?raw=true)


links:

1. https://docs.radxa.com/en/rock3/rock3a
2. https://wiki.radxa.com/Rock3/3a
3. https://docs.u-boot.org/en/latest/board/rockchip/rockchip.html#rockchip-boards
4. 

#Others

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
