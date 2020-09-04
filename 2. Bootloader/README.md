Bootloader là thành phần thứ hai của embedded Linux. Nó có nhiệm vụ khởi tạo hệ thống và loader kernel vào bộ nhớ. Cũng như toolchain, bootloader có rất nhiều loại phù hợp cho từng loại kiến trúc khác nhau. Trong phần hướng dẫn này sẽ sử dụng U-Boot cho kit BeagleBone Black.

Trở về thư mục home của user:

$ cd

Tiến hành get resource u-boot về:

$ git clone git://git.denx.de/u-boot.git

Việc get này nhanh hay chậm tùy vào tốc độ đường truyền internet.

Sau khi đã get xong, home directory sẽ có một thư mục mang tên u-boot.

$ cd u-boot

$ armmake distclean

Câu lệnh trên để clean các kết quả và cấu hình của lần build trước. Chúng ta tiến hành cấu hình u-boot cho boar:

$ armmake am335x_evm_defconfig

Tiến hành build bootloader:

$ armmake

Sau khi build xong chúng ta sẽ được file MLO và u-boot.img.

Test u-boot trên board BBB.

Trước tiên chúng ta cần write u-boot xuống SD card. Chúng ta cần định dạng SD card để thực hiện việc test.

Trong bài hướng dẫn này sử dụng Adapter microSD (loại khay nhỏ cắm trực tiếp vào máy tính). Kiểm tra thẻ nhớ đã được mount hay chưa?

$ lsblk

Nếu kết quả có chứa mmcblk0 thì thẻ nhớ đã được mount thành công. Tiến hành phân vùng thẻ nhớ.

Chúng ta sẽ sử dụng một script trong sách để định dạng thẻ nhớ.

Get script này về máy:

$ git clone https://github.com/PacktPublishing/Mastering-Embedded-Linux-Programming-Second-Edition.git

Chúng ta sẽ được file có tên “Mastering-Embedded-Linux-Programming-Second-Edition”.

Thay đổi file mode cho script:

$ sudo chmod 755 ./Mastering-Embedded-Linux-Programming-Second-Edition/format-sdcard.sh

Tiến hành format với lệnh:

$ ./Mastering-Embedded-Linux-Programming-Second-Edition/format-sdcard.sh mmcblk0

Rút thẻ nhớ và cắm vào lại để sử dụng. Tiếp theo là copy các file vào phân vùng boot.

$ cp MLO u-boot.img /media/user_name/boot

Chuẩn bị boot:

Board BBB chưa cấp điện, lắp thẻ nhớ vào và kết nối cổng terminal trên BBB vào thiết bị UART-to-COM được cắm vào máy tính. Sử dụng terminal console để tương tác, ở đây sử dụng picocom.

$ sudo picocom -b 115200 /dev/ttyUSB0

Nhấn giữ nút boot và cấp nguồn cho board, lúc này trên console sẽ hiện các message, khi có thông báo nhấn SPACE để hủy việc boot tự động ta nhấn SPACE. Lúc này chúng ta có thể tương tác với u-boot. Như vậy là u-boot đã được build thành công.