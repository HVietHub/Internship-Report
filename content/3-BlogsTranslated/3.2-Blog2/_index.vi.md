---
title: "Blog 2"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

# 6 bài học các nhà phát triển game có thể rút ra từ chiến lược quản trị đám mây của Epic Games

Bởi Gena Gizzi và Ryan Rumble, ngày 12/03/2025 trong Amazon EC2, Amazon EBS, Amazon ElastiCache, Amazon EventBridge, Amazon RDS, Application Integration, Application Services, AWS Lambda, AWS Step Functions, Compute, Customer Solutions, Database, Game Development, Games, Graviton, Industries, Storage

*Bài viết này được đồng tác giả bởi Reza Nikoopour, Kỹ sư chính, Quản trị Đám mây tại Epic Games.*

---

Đối với các studio game lớn nhỏ, một chiến lược quản trị đám mây thông minh có thể đóng vai trò quan trọng trong việc giảm chi phí và tăng cường bảo mật cho một trò chơi live service. Dưới đây là một số bài học quan trọng từ trải nghiệm của Epic Games khi làm việc với Amazon Web Services (AWS) mà các nhà phát triển game nên cân nhắc khi xây dựng chiến lược quản trị đám mây.

Kể từ khi Fortnite ra mắt năm 2017, lượng người chơi của Epic đã đạt tới 500 triệu tài khoản đăng ký. Fortnite được xây dựng trên AWS, sử dụng các dịch vụ như Amazon EC2 cho hạ tầng backend. Đến nay, Epic vẫn tiếp tục dựa vào công nghệ AWS để vận hành đội ngũ máy chủ toàn cầu.

Với AWS, Epic có thể tự động mở rộng dung lượng tính toán dựa trên biến động nhu cầu người chơi khi trò chơi ngày càng phát triển.

> "AWS đã đóng vai trò quan trọng trong việc định hướng khi chúng tôi thành lập nhóm quản trị đám mây tại Epic. Nhờ vậy, chúng tôi đã giảm được khoảng 20% chi phí đám mây, đồng thời cải thiện khả năng quan sát tài nguyên trên toàn hệ thống."  
> — Shane Smith, Phó Chủ tịch Dịch vụ Công nghệ tại Epic Games

Điều này có được một phần nhờ sáng kiến được triển khai vào năm 2022 nhằm đạt được hiệu quả trên đám mây, không chỉ dừng ở mức thành thạo, đồng thời chuyển đổi số backend của Epic để hỗ trợ sự phát triển tiếp theo của Fortnite.

---

## 1. Thiết lập rào chắn (guardrails)

Các nhà phát triển game thường mơ ước phát hành một siêu phẩm, nhưng việc đoán trước mức độ phổ biến là rất khó. Các chiến lược như gắn thẻ (tagging), quản lý quyền truy cập và quản trị tài nguyên đám mây thường không được ưu tiên trong giai đoạn tăng trưởng nhanh. Khi trò chơi bất ngờ trở thành hiện tượng, trọng tâm thường chuyển hoàn toàn sang duy trì trải nghiệm người chơi.

Tuy nhiên, quản trị đám mây là yếu tố sống còn, vì nó cung cấp bộ quy tắc, quy trình và báo cáo giúp định hướng thực hành tốt nhất. Việc đảm bảo tầm nhìn tổng thể về tài nguyên và hiểu rõ các đường dẫn quan trọng của game sẽ giúp các nhà phát triển có vị thế vững chắc hơn trong dài hạn.

---

## 2. Khai thác chuyên môn từ AWS

Để cải thiện quản trị đám mây, Epic đã thành lập một ban chỉ đạo gồm các lãnh đạo kỹ thuật nội bộ và đại diện từ nhóm tài khoản AWS. Các cuộc thảo luận ban đầu tập trung vào mức độ trưởng thành trên đám mây, thực hành tốt nhất, và cách AWS có thể giúp Epic vận hành hiệu quả hơn.

Từ đó, Epic làm việc chặt chẽ với AWS Professional Services và AWS Customer Solutions Manager. Họ tập hợp một nhóm đa dạng các kỹ sư đam mê đám mây từ nhiều bộ phận khác nhau để thành lập Cloud Governance team, còn được gọi là cloud center of excellence (CCoE).

---

## 3. Hiểu rõ quyền sở hữu

Để thực hiện thay đổi có ý nghĩa, Epic cần một cách tiếp cận tiêu chuẩn để xác định quyền sở hữu tài khoản AWS. Nhóm Quản trị Đám mây đã xác định người chịu trách nhiệm cho từng tài khoản và tạo các kênh liên lạc thời gian thực để trao đổi thông tin nhanh chóng.

Nền tảng này cho phép Epic xây dựng và thực thi nhiều biện pháp kiểm soát bổ sung trên toàn bộ môi trường đám mây.

---

## 4. Cấp quyền truy cập

Nhóm Quản trị Đám mây tập trung vào việc cung cấp truy cập tự phục vụ đến AWS, sử dụng kết hợp giữa GitHub, Okta, SailPoint và AWS IAM Identity Center.

- **GitHub**: lưu trữ hạ tầng dưới dạng mã (IaC) định nghĩa vai trò IAM và nơi triển khai.
- **AWS IAM Identity Center**: phân phối các vai trò IAM chuẩn trên toàn bộ tài khoản AWS của Epic.
- **Okta**: cung cấp danh tính và nhóm quyền để kiểm soát truy cập.
- **SailPoint**: cho phép quản lý tài khoản phê duyệt hoặc từ chối yêu cầu truy cập.

---

## 5. Tự động hóa cấu hình

Sau khi triển khai quyền truy cập tự phục vụ, Epic sử dụng AWS Step Functions để triển khai cấu hình cơ bản cho mỗi tài khoản.

Điều này bao gồm:

- Các thành phần mặc định như vai trò IAM
- Biện pháp tiết kiệm chi phí, ví dụ hết hạn log cho AWS Lambda
- Các biện pháp bảo mật, như bật mã hóa Amazon EBS theo mặc định ở tất cả các khu vực

Với AWS Step Functions và AWS Lambda, Epic có thể tập trung vào cấu hình cốt lõi mà không lo ngại về điều phối workflow, đồng thời đảm bảo mọi tài khoản đều được thiết lập đúng chuẩn.

---

## 6. Thực thi tiêu chuẩn

Trước khi triển khai chính sách, Epic cần cơ chế phù hợp, vì vậy đã hợp tác với nhà cung cấp giải pháp quản trị.

Epic chuyển tiếp các sự kiện từ tất cả tài khoản vào một điểm duy nhất thông qua Amazon EventBridge để phân tích và xử lý. Nhờ đó, nhóm Quản trị Đám mây có thể buộc sử dụng bộ tag tối thiểu nhằm cung cấp thông tin về quyền sở hữu và chi phí.

Các cơ chế tự động loại bỏ ngay lập tức mọi tài nguyên không có tag bắt buộc. Tương tự, Epic cũng áp dụng cơ chế buộc Amazon RDS và Amazon ElastiCache chạy trên AWS Graviton để tối ưu chi phí, nhưng cho phép ngoại lệ khi có yêu cầu.

Ngoài ra, Epic đưa vào quy trình bắt buộc quét thay đổi hạ tầng dưới dạng mã (IaC) khi có pull request trong GitHub. Nếu phát hiện vấn đề, hệ thống sẽ tự động bình luận vào pull request, giúp developer sửa lỗi trước khi triển khai.

---

## Tổng kết

Bằng việc xem xét lại con người, quy trình và công nghệ, Epic đã đạt được nhiều tiến bộ trong hành trình quản trị đám mây và gặt hái kết quả rõ rệt.

Ngoài tiết kiệm chi phí, Epic còn có khả năng quan sát tài nguyên tốt hơn, với gần như 100% tài nguyên AWS được gắn tag trong cả môi trường production và development. Việc kiểm toán liên tục quyền truy cập và theo dõi tập trung cũng đã tăng cường bảo mật, đồng thời thúc đẩy văn hóa tích cực trong toàn công ty.

Đối với các nhà phát triển game muốn tìm hiểu thêm về chiến lược quản trị đám mây thông minh, hãy tham khảo cách các công ty khác triển khai best practices và liên hệ với AWS Representative để được hỗ trợ.

---

## Tác giả
![Juho Jantunen](/images/3-BlogsTranslated/blog2-author-1.png)
**Gena Gizzi:**  
Gena Gizzi là Kiến trúc sư Giải pháp Game Cấp cao của AWS. Cô hỗ trợ khách hàng phát triển, ra mắt và mở rộng quy mô trò chơi cũng như doanh nghiệp của họ trên AWS.
![Juho Jantunen](/images/3-BlogsTranslated/blog2-author-2.png)
**Ryan Rumble:**  
Ryan Rumble là Trưởng phòng Giải pháp Khách hàng của AWS. Bên cạnh việc thúc đẩy Quản lý Chương trình cho đội ngũ và khách hàng, Ryan còn chuyên hỗ trợ các công ty định hướng chuyển đổi và trưởng thành trên hành trình đám mây.

