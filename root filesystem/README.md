Bước gần như cuối cùng để trong việc build. Chúng ta tiến hành get root filesystem về máy.

$ cd

$ git clone git://git.buildroot.net/buildroot

$ cd buildroot

Cấu hình buildroot để build root filesystem cho BBB:

$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- distclean

$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- beaglebone_defconfig

$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig

Sau câu lệnh trên, một cửa sổ cấu hình sẽ hiện ra. Lần lượt, chúng ta cấu hình như sau (nhấn SPACE bỏ chọn, nhấn y để chọn):

Uncheck kernel
Uncheck bootloader
Target option:
Floating point strategy (NEON)
ARM instruction set (Thumb2)
Target packages:
Networking application:
[*] dhcpcd
[*] openssh
Exit và save cấu hình lại.

Tiến hành build với lệnh:

`$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-`

Việc build sẽ khá mất thời gian. Sau khi build xong, chúng ta sẽ có được file rootfs.ext4
Tiến hành write nó xuống thẻ nhớ:

`$ sudo dd if=output/images/rootfs.ext4 of=/dev/mmcblk0p2`

Bây giờ, chúng ta sẽ cùng test thành quả.

Chúng ta cũng tiến hành boot như với u-boot đến đoạn nhấn SPACE.

Việc boot này theo các thủ công, sau khi đã chắc chắn chúng ta sẽ tạo một file để board tự boot.

=> mmc rescan

=> fatload mmc 0:1 ${loadaddr} zImage

=> fatload mmc 0:1 ${fdtaddr} am335x-boneblack.dtb
=> setenv bootargs console=${console} {optargs} root=/dev/mmcblk0p2 rootfstype=ext4

=> bootz ${loadaddr} – ${fdtaddr}

Nếu một việc suông sẽ, console sẽ xuất hiện thông báo login. Chúng ta sẽ login vào với id là “root” mà không cần mật khẩu, chúng ta có thể đổi mật khẩu sau.

Nếu muốn board tự boot, chúng ta sẽ tạo ra ra một file có tên là uEnv.txt chứa nội dung của các bước boot thủ công, nội dung file như sau:
```
bootdir=                                                         
bootfile=zImage 
fdtfile=am335x-boneblack.dtb
loadfdt=fatload mmc 0:1 ${fdtaddr} ${fdtfile}
loaduimage=fatload mmc 0:1 ${loadaddr} ${bootfile}     
uenvcmd=mmc rescan; run loaduimage; run loadfdt; run fdtboot
fdtboot=run mmc_args; bootz ${loadaddr} - ${fdtaddr}
mmc_args=setenv bootargs console=${console} ${optargs} root=/dev/mmcblk0p2 rootfstype=ext4
```
Như vậy là chúng ta đã xây dựng thành công hệ thống embedded Linux cơ bản trên board BBB.