
Phân loại Device Driver:
Character: Là driver không có buffer I/O, chiếm số đông trong những driver.
Block: Nó giao tiếp với block I/O và thường sử dụng cho các thiết bị lưu trữ, được sử dụng để đọc và ghi ổ đĩa càng nhanh càng tốt.
Network: Tương tự như block nhưng nó được sử dụng để truyền và nhận thông qua network thay vì ổ đĩa.
Tại sao cần phải dev Device Driver?
Vì bạn có những module độc quyền, bạn không muốn cấp phép cho mọi người sử dụng.
Để giảm thời gian boot bằng cách trì hoãn việc load các module không cần thiết.
Khi một số driver được load và nó tốn quá nhiều bộ nhớ để biên dịch khi chúng ở dạng static, chẳng hạn như USB driver.
Các kỹ năng bắt buộc:
Chúng ta cần có kỹ năng lập trình C, ít nhất thì cũng cần thân thiện với pointer và một số kỹ năng về hardware.

Environment Setup
Chúng ta có 2 cách để develop driver, sử dụng kernel source code và develop driver alone.

Theo cách đầu tiên, chúng ta cần download hoặc clone source code của kernel về máy để dev, install toolchain để build cho platform mà driver sẽ chạy trên đó, sau đó chúng ta sẽ dev driver dựa trên source code này.

Cách thứ hai, chúng ta sẽ tạo một thư mục, trong thư mục đó chúng ta tạo source code cho driver và Makefile cho nó. Trước khi build cho cách này, chúng ta cần cài đặt linux-header cho máy để build.

Tóm lại, cả hai cách đều có ưu và nhược điểm riêng, tùy vào yêu cầu mà ta lựa chọn cách nào phù hợp hơn. Cách đầu tiên cho ta tạo ra các module trên nhiều platform (cross-compile) nhưng lại khá phức tạp (chỉnh sửa các Kconfig, Kbuild,.. để có thể sử dụng). Cách thứ hai thì đơn giản hơn nhiều nhưng bù lại ta chỉ có thể build module trên platform đang chạy mà thôi (native-compile). Ngoài ra, cách thứ nhất có thể giúp ta đóng góp driver này cho cộng đồng Linux, mọi người có thể xem xét và chỉnh sửa driver này, cách thứ hai thì lại ích kỷ hơn một chút.

Trong phần hướng dẫn này, tôi sẽ thực hiện theo cách thứ 2 và tôi sẽ dev trên platform là Raspberry Pi.

Cài đặt các package bắt buộc;

$ sudo apt install gawk wget git diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm ncurses-dev lzop

Cài đặt kernel-header:

$ sudo apt install linux-headers-$(uname -r)

Khung Driver
Chúng ta cùng xem xét khung sườn của một driver cơ bản sau:

#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>
static int __init helloworld_init(void) {
pr_info("Hello world!\n");
return 0;
}
static void __exit helloworld_exit(void) {
pr_info("End of the world\n");
}
module_init(helloworld_init);
module_exit(helloworld_exit);
MODULE_AUTHOR("anhgiau <nguyenanhgiau1008@gmail.com>");
MODULE_LICENSE("GPL");

Phần code này được lưu trong file có tên là hello.c. Chúng ta nên tạo một thư mục và đặt source code của chúng ta vào trong đó để dễ quản lý.

$ mkdir hello

$ mv hello.c hello/

Bây giờ, chúng ta sẽ cùng tìm hiểu một chút về driver này.

#include <linux/init.h>
#include <linux/module.h>
#include <linux/kernel.h>

Cũng giống như lập trình application với C, kernel module cũng sử dụng cú pháp #include <header.h> để sử dụng các function, variable và data type của header file. Chúng ta cần <linux/init.h> để sử dụng function khởi tạo và cleanup driver, tương tự <linux/module.h> cho phép chuyển các tham số đến module vào lúc load.

Chúng ta sẽ tìm hiểu phần thân source code sau.

MODULE_AUTHOR("anhgiau <nguyenanhgiau1008@gmail.com>");
MODULE_LICENSE("GPL");

Phần này sẽ cung cấp các thông tin về module, chẳng hạn như: MODULE_AUTHOR(“tên tác giả”), MODULE_DESCRIPTION(“mô tả về mục đích của driver”), MODULE_LICENSE(“giấy phép”), và còn nhiều mô tả khác có trong <linux/module.h>

Tất cả các kernel driver đều có entry và exit point. Entry point tương ứng với function được gọi khi module được load (insmod, modprobe) và exit point tương ứng với function được gọi khi unload module (rmmod, modprobe -r).

Ngôn ngữ C có function main(), là entry point của mỗi chương trình của user được viết bằng C/C++ và exit khi function này return. Với kernel module, mọi thứ lại khác. Entry point có thể là bất kỳ tên nào mà bạn muốn và exit point cũng được định nghĩa bởi một function khác. Những gì mà chúng ta cần là thông báo cho kernel biết function nào sẽ được thực thi khi entry và exit. Function helloworld_init và helloworld_exit có thể là mang bất kỳ cái tên nào mà bạn muốn. Chỉ có một điều bắt buộc là bạn cần xác định function nào tương ứng với lúc load và remove.

module_init() được sử dụng để khai báo function mà sẽ được thực hiện khi module được load (insmod, modprobe). Function được khai báo có nhiệm vụ khởi tạo và định nghĩa các hoạt động của module. module_exit() sẽ khai báo function được thực hiện khi module unload (rmmod, modprobe -r).

__init và __exit là kernel marco được định nghĩa trong <linux/init.h>, chúng ta sẽ không đi sâu vào vấn đề này.

Tạo Makefile:

Chúng ta sẽ tạo một file có tên là Makefile, được đặt trong thư mục hello/ với nội dung sau:

obj-m := hello.o
all:
<tab>make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules
clean:
<tab>make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean

Lưu ý, tab ở trước make.

Compile và Testing
Compile first driver:

Để thực hiện việc build, chúng ta chỉ cần run lệnh make:

$ make

Sau khi build thành công, trong thư mục của chúng ta sẽ có thêm nhiều file.

Test driver:

Trước tiên, chúng ta sẽ load module bằng lệnh:

$ sudo insmod hello.ko
Để xem kết quả, chúng ta cần xem kernel log

$ dmesg

Tiếp theo, chúng ta sẽ unload module:

$ sudo rmmod hello

Xem kết quả của việc unload:

$ dmesg

Summary
Trong phần này, chúng ta đã tìm hiểu cơ bản về device driver, cách thức build một module đơn giản. Các load và unload module.

 
