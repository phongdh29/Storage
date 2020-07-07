# 1.File system
- File system được dùng để quản lý cách dữ liệu được đọc và lưu trên thiết bị.
- File system cho phép người dùng truy cập nhanh chóng và an toàn khi cần thiết.
- Journaling: được thiết kế để ngăn ngừa mất dữ liệu từ sự cố hoặc mất điện đột ngột. Nếu không có journaling, máy tính không biết liệu tệp đã được ghi vào ổ đĩa hay chưa => Tệp tin vẫn còn trên disk, nhưng bị lỗi. Nếu có journaling, máy tính sẽ kiểm tra nhật ký hệ thống tệp khi nó khởi động và tiếp tục công việc => ngăn ngừa mất dữ liệu.


# 2.Các loại file system

- Ext: Ext là phiên bản đầu tiên của hệ thống tệp ext, được phát giới thiệu vào năm 1992. Đây là bản nâng cấp từ hệ thống tệp Minix, nhưng thiếu các tính năng quan trọng.
- Ext2: Không phải là journaling file system. 
- Ext3: giống như ext 2 nhưng có thêm journaling. Cho phép các phana vùng chuyển đổi giữa ext2 và ext3. 
- Ext4: Bao gồm các tính năng mới hơn và giúp giảm phân mảnh tệp. Đây là phiên bản được sử dụng trên hầu hết các hệ thống Linux hiện nay
