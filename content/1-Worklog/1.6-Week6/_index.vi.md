---
title: "Worklog Tuần 6"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---


### Mục tiêu tuần 6:

* Hiểu dịch vụ AWS cơ bản, cách dùng console & CLI.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                            |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ | --------------- | ----------------------------------------- |
| 2 | - Vai trò & Điều kiện IAM <br> - **Thực hành:** <br>&emsp; + Tạo Nhóm IAM <br>&emsp; + Tạo Người dùng IAM <br>&emsp; + Kiểm tra Quyền của Người dùng <br>&emsp; + Tạo Vai trò IAM Quản trị <br>&emsp; + Cấu hình Chuyển đổi vai trò <br>&emsp; + Hạn chế quyền truy cập vai trò <br>&emsp; + Chuyển đổi giới hạn vai trò theo IP <br>&emsp; + Chuyển đổi giới hạn vai trò theo Thời gian <br>&emsp; + Dọn dẹp tài nguyên | 13/10/2025 | 13/10/2025 | <https://000044.awsstudygroup.com/>|
| 3 | - Cấp quyền cho ứng dụng truy cập các dịch vụ AWS với vai trò IAM. <br> - **Thực hành:** <br>&emsp; + Tạo Phiên bản EC2 <br>&emsp; + Tạo thùng S3 <br>&emsp; + Tạo người dùng và khóa truy cập IAM <br>&emsp; + Sử dụng khóa truy cập <br>&emsp; + Tạo người dùng và khóa truy cập IAM <br>&emsp; + Sử dụng vai trò IAM <br>&emsp; + Dọn dẹp tài nguyên | 14/10/2025 | 14/10/2025 | <https://000048.awsstudygroup.com/> |
| 4 | - Ôn tập các khái niệm về cơ sở dữ liệu <br> - Amazon RDS & Amazon Aurora <br> - Redshift - Elasticache | 15/10/2025 | 15/10/2025 | <https://cloudjourney.awsstudygroup.com/> |
| 5 | - Amazon Relational Database Service (Amazon RDS) <br> - **Thực hành:** <br>&emsp; + Tạo VPC <br>&emsp; + Tạo Nhóm bảo mật EC2 <br>&emsp; + Tạo Nhóm Bảo mật RDS <br>&emsp; + Tạo Nhóm Mạng Con DB <br>&emsp; + Tạo phiên bản EC2 <br>&emsp; + Tạo phiên bản cơ sở dữ liệu RDS <br>&emsp; + Triển khai Ứng dụng <br>&emsp; + Sao lưu và Khôi phục <br>&emsp; + Dọn dẹp tài nguyên | 16/10/2025 | 16/10/2025 | <https://000005.awsstudygroup.com/> |
| 6 |- Chuyển đổi & Di chuyển Sơ đồ Cơ sở dữ liệu <br> - **Thực hành:** <br>&emsp; + Máy khách RDP EC2 Connect <br>&emsp; + Trình quản lý Hạm đội EC2 Connect <br>&emsp; + Cấu hình Nguồn SQLSrv <br>&emsp; + Oracle connect SrcDB <br>&emsp; + Cấu hình Nguồn Oracle <br>&emsp; + Xóa Ràng buộc <br>&emsp; + MSSQL để cấu hình mục tiêu MySQL <br>&emsp; + MSSQL để tạo dự án MySQL <br>&emsp; + MSSQL để tạo SchemConv MySQL <br>&emsp; + Chuyển đổi Oracle2 MySQL Schema 1 <br>&emsp; + Tạo tác vụ và điểm cuối Mig <br>&emsp; + Kiểm tra S3 <br>&emsp; + Tạo di chuyển không có máy chủ <br>&emsp; + Tạo sự kiện không <br>&emsp; + Nhật ký <br>&emsp; + Khắc phục sự cố áp suất bộ nhớ trong kịch bản thử nghiệm <br>&emsp; + Khắc phục sự cố lỗi bảng trong kịch bản thử nghiệm <br>&emsp; + Dọn dẹp tài nguyên | 17/10/2025 | 17/10/2025 | <https://www.youtube.com/watch?v=cxwAOP1379s&list=PLahN4TLWtox2a3vElknwzU_urND8hLn1i&index=229> |


### Kết quả đạt được tuần 6:

- Nâng cao kỹ năng về IAM bằng cách triển khai quyền truy cập vai trò có điều kiện (IP, thời gian), tạo người dùng, nhóm và vai trò quản trị viên, và xác thực chuyển đổi vai trò an toàn.

- Học cách cấp quyền truy cập ứng dụng an toàn vào các dịch vụ AWS bằng cách thay thế khóa truy cập bằng vai trò IAM trên EC2.

- Nâng cao hiểu biết về các dịch vụ cơ sở dữ liệu AWS bao gồm RDS, Aurora, Redshift và ElastiCache.

- Triển khai thành công môi trường RDS đầy đủ (VPC, nhóm bảo mật, EC2, phiên bản DB) và thực hiện triển khai ứng dụng cùng với các hoạt động sao lưu/khôi phục.

- Hoàn thành quy trình chuyển đổi và di chuyển lược đồ cơ sở dữ liệu đầu cuối cho MSSQL→Aurora MySQL và Oracle→MySQL bằng SCT và DMS, bao gồm khắc phục sự cố và giám sát sự kiện.