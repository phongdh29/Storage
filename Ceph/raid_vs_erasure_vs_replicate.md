# 1. RAID
# 2. Erasure code
- Erasure Coding (EC) là một phương pháp bảo vệ dữ liệu mà trong đó dữ liệu được chia thành các đoạn, được mở rộng và được mã hóa với các phần dữ liệu dự phòng và lưu trữ

- Mục tiêu của việc Erasure Coding là để cho phép dữ liệu bị hỏng tại một số điểm trong quá trình lưu trữ đĩa được xây dựng lại bằng cách sử dụng thông tin về dữ liệu được lưu trữ ở nơi khác

- Ưu điểm: Sử dụng ít dung lượng ổ cứng hơn
- Nhược điểm: Sử dụng nhiều CPU để bảo vệ dữ liệu, rebuild lâu

# 3. Replicate
- Tạo ra các bản copy của dữ liệu vào các server khác nhau => Bảo vệ dữ liệu ở tầng server, DC

- Replicate 2: Tạo ra 2 bản dữ liệu trên 2 server. Nếu có 4 server thì chỉ được phép lỗi tối đa 1 server

- Replicate 3: Tạo ra 3 bản dữ liệu trên 3 server. Nếu có 4 server thì chỉ được phép lỗi tối đa 2 server.

=>> Tùy vào mức độ dữ liệu mà chọn Replicate 2 hoặc 3

- Ưu điểm: Dễ rebuild, sử dụng ít CPU

- Nhược điểm: Yêu cầu ít nhất 2 storage trở lên.


