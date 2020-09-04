Cross: Toochain này chạy trên các hệ thống khác loại, cho phép việc phát triển nhanh hơn trên desktop và load xuống các thiết bị để test.

# Build toolchain crosstool-NG

Cài đặt công cụ

```
sudo apt-get install gcc gperf bison flex texinfo help2man make libncurses5-dev \
python-dev
```

Clone toolchain về máy và tiến hành cài đặt:
```


$ git clone https://github.com/crosstool-ng/crosstool-ng
$ cd crosstool-NG
$ ./bootstrap
$ ./configure –enable-local
$ make
$ make install
```

Sau khi việc cài đặt hoàn thành, chúng ta tiến hành select toolchain. Để xem danh sách hỗ trợ của toolchain, chúng ta sử dụng lệnh:

`$ ./ct-ng list-samples`

Toolchain sẽ show một loạt danh sách mà crosstool-NG hỗ trợ. Để chọn toolchain cho BeagleBone Black, ta gõ lệnh:

`$ ./ct-ng arm-cortex_a8-linux-gnueabi`

Để xây dựng toolchain cho các kiến trúc khác, ta cũng có thể gõ lệnh:

`$ ./ct-ng selection_toolchain`

Cấu hình cho toolchain. Để xem chi tiết việc cấu hình, chúng ta có thể vào trang chủ của crosstool-NG, dưới dây sẽ hướng dẫn cấu hình cho BBB với lệnh:

`$ ./ct-ng menuconfig`

Trong Paths and mics, disable Render the toolchain read-only
Trong Targer options | Floating point, select hardware (FPU)
Sau khi cấu hình, tất cả sẽ được lưu trong file .config. Chúng ta tiến hành build toolchain:

`$ ./ct-ng build`

Việc build có thể mất đến 1 giờ hoặc hơn tùy vào cấu hình máy tính.

Sau khi build xong, kết quả được tạo ra trong thư mục ~/x-tools/arm-cortex_a8-linux-gnueabihf/bin. Chúng ta có thể add pathname này vào $PATH để sử dụng bash_completion thuận tiện cho các hoạt động sau.

Như vậy là chúng ta đã build xong toolchain và có thể khám phá các hoạt động của toolchain này.

Link tham khảo: https://anhgiau.wordpress.com/2018/02/25/toolchain-linux/


Để cài đặt toolchain:

$ sudo apt install gcc-arm-linux-gnueabihf

Câu lệnh trên sẽ cài đặt toolchain để chúng ta có thể build các thành phần khác. Các lệnh của toolchain này nằm trong thư mục /usr/bin (điều này sẽ cần thiết cho việc build root filesystem).

Tạo alias thuận tiện cho việc build. Chúng ta sẽ thường lặp lại việc gõ lệnh này, nên việc tạo alias là một cách để giúp chúng ta tối ưu hóa công việc hơn.

$ alias armmake='make -j4 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- '

-j4: chỉ ra số lõi sử dụng cho việc build.
ARCH=arm: chỉ định kiến trúc build là arm.
CROSS_COMPILE=arm-linux-gnueabihf-: prefix toolchain
Kết thúc phần cài đặt toolchain.