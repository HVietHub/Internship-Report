---
title: "Worklog Tuần 5"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---


### Mục tiêu tuần 5:

* Hiểu dịch vụ AWS cơ bản, cách dùng console & CLI.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2   | - Tối ưu hóa chi phí EC2 với Lambda <br> - **Thực hành:** <br>&emsp; + Tạo VPC <br>&emsp; + Tạo nhóm bảo mật <br>&emsp; + Tạo phiên bản EC2 <br>&emsp; + Web-hooks slack đang đến <br>&emsp; + Tạo thẻ cho phiên bản <br>&emsp; + Tạo vai trò cho hàm Lambda <br>&emsp; + Tạo hàm Lambda <br>&emsp; + Hàm dừng phiên bản <br>&emsp; + Hàm bắt đầu phiên bản <br>&emsp; + Kiểm tra kết quả <br>&emsp; + Dọn dẹp tài nguyên                                                                                             | 06/10/2025   | 06/10/2025      |<https://000022.awsstudygroup.com/>|
| 3   | - Quản lý tài nguyên bằng thẻ và nhóm tài nguyên <br> - **Thực hành:** <br>&emsp; + Tạo phiên bản EC2 bằng thẻ <br>&emsp; + Quản lý thẻ trong tài nguyên AWS <br>&emsp; + Lọc tài nguyên theo thẻ <br>&emsp; + Sử dụng thẻ với CLI <br>&emsp; + Tạo nhóm tài nguyên <br>&emsp; + Dọn dẹp tài nguyên                                            | 07/10/2025   | 07/10/2025      | <https://000027.awsstudygroup.com/> |
| 4   | - Quản lý quyền truy cập vào các dịch vụ EC2 bằng thẻ tài nguyên thông qua các dịch vụ IAM <br> - **Thực hành:** <br>&emsp; + Tạo người dùng IAM <br>&emsp; + Tạo Chính sách IAM <br>&emsp; + Tạo Vai trò IAM <br>&emsp; + Chuyển đổi Vai trò <br>&emsp; + Khởi tạo quyền truy cập vào bảng điều khiển EC2 trong Khu vực AWS - Tokyo <br>&emsp; + Khởi tạo quyền truy cập vào bảng điều khiển EC2 trong Khu vực AWS - Bắc Virginia <br>&emsp; + Chỉnh sửa Thẻ tài nguyên trên Phiên bản EC2 <br>&emsp; + Kiểm tra Chính sách <br>&emsp; + Dọn dẹp tài nguyên | 08/10/2025   | 08/10/2025      | <https://000028.awsstudygroup.com/> |
| 5   | - Giới hạn quyền của người dùng với ranh giới quyền IAM <br> - **Thực hành:** <br>&emsp; + Tạo Chính sách hạn chế <br>&emsp; + Tạo Người dùng bị giới hạn IAM <br>&emsp; + Kiểm tra Giới hạn người dùng IAM <br>&emsp; + Dọn dẹp tài nguyên                  | 09/10/2025   | 09/10/2025      | <https://000030.awsstudygroup.com/> |
| 6   | - Mã hóa khi lưu trữ với AWS KMS <br> - **Thực hành:** <br>&emsp; + Tạo Chính sách và Vai trò <br>&emsp; + Tạo Nhóm và Người dùng <br>&emsp; + Tạo Dịch vụ Quản lý Khóa <br>&emsp; + Tạo Thùng <br>&emsp; + Tải dữ liệu lên S3 <br>&emsp; + Tạo CloudTrail <br>&emsp; + Ghi nhật ký vào CloudTrail <br>&emsp; + Tạo Amazon Athena <br>&emsp; + Truy xuất dữ liệu bằng Athena <br>&emsp; + Kiểm tra và chia sẻ dữ liệu được mã hóa trên S3 <br>&emsp; + Dọn dẹp tài nguyên                                                                                         | 10/10/2025   | 10/10/2025      | <https://000033.awsstudygroup.com/> |


### Kết quả đạt được tuần 5:

- Tối ưu hóa chi phí EC2 tự động bằng các chức năng Lambda (tự động khởi động/dừng, chiến lược gắn thẻ, thông báo Slack).

- Cải thiện việc tổ chức tài nguyên bằng cách sử dụng thẻ hiệu quả và tạo Nhóm Tài nguyên để tăng khả năng hiển thị và quản lý.

- Nâng cao kỹ năng kiểm soát truy cập bằng cách áp dụng các chính sách IAM gắn liền với thẻ tài nguyên và xác thực quyền trên nhiều Vùng AWS.

- Củng cố quản trị IAM thông qua việc triển khai các ranh giới quyền để thực thi các giới hạn người dùng nghiêm ngặt.

- Có được kinh nghiệm thực tế vững chắc về mã hóa AWS KMS, lưu trữ an toàn S3, kiểm tra CloudTrail và phân tích nhật ký Athena.