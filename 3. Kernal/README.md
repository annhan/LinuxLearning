Kernel là thành phần tiếp theo mà chúng ta sẽ làm việc. Để có thể build kernel, trước tiên chúng ta cần get resouce về máy:

$ cd

$ git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable.git

$ cd linux-stable

Xem danh sách version kernel:

$ git branch -a

Chọn version kernel để build:

$ git checkout linux-4.9.y

Bắt đàu quá trình build

$ armmake distclean

$ armmake multi_v7_defconfig

$ armmake

Thời gian build nhanh hay lâu tùy thuộc vào cấu hình máy. Sau khi build ta sẽ được file kết quả trong arch/arm/boot/zImage và arch/arm/boot/dts/am335x-boneblack.dtb
Copy các file này vào phân vùng boot trên thẻ nhớ

$ cd

$ cd linux-statble

$ cp arch/arm/boot/zImage arch/arm/boot/am335x-boneblack.dtb /media/user/boot

Test kernel thồi:

Làm tương tự với u-boot, đến khi nhấn SPACE.

=> mmc rescan

=> fatload mmc 0:1 ${loadaddr} zImage

=> fatload mmc 0:1 ${fdtaddr} am335x-boneblack.dtb
=> setenv bootargs console=ttyO0

=> bootz ${loadaddr} – ${fdtaddr}

Nếu board có thể load được kernel thì chúng ta đã build kernel thành công. Sau đó, trên console sẽ báo kernel panic. Lỗi này do chúng ta chưa có root filesystem.