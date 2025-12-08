---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# Apex Legends Di chuyển sang Máy chủ Amazon GameLift chỉ trong 10 ngày

Bởi Michael Jackson, David Holladay, và Juho Jantunen vào ngày 12/05/2025 trong Amazon GameLift, Phát triển game, Game, Ngành công nghiệp

Respawn Entertainment gần đây đã di chuyển Apex Legends sang máy chủ Amazon GameLift, hoàn tất quá trình cắt chuyển lưu lượng chỉ trong 10 ngày mà không có thời gian chết (downtime) nào đối với người chơi.

Một ưu tiên cốt lõi của Respawn là thực hiện việc tích hợp và xác thực kỹ thuật mà không gây gián đoạn cho các nhóm phát triển hoặc người chơi Apex. AWS đã đáp ứng được nhu cầu đó bằng cách hợp tác với AWS Partner Code Wizards Group và tận dụng Chương trình Hỗ trợ Di chuyển Amazon GameLift để đưa ra một kế hoạch di chuyển có giai đoạn, rủi ro thấp và toàn diện — kết quả là một trong những cuộc chuyển đổi hạ tầng do AWS dẫn dắt lớn nhất từng được thực hiện cho một trò chơi live service.

![Apex Legends GameLift Migration](../../images/3-BlogsTranslated/blog1-image-1.png)

---

## Đặt ưu tiên

Là một trò chơi live service, Apex Legends liên tục phát triển — không chỉ về gameplay và nội dung, mà còn về chất lượng vận hành. Với hàng triệu người chơi trên toàn thế giới, Respawn cần nhiều khả năng quan sát, tính nhất quán và sự linh hoạt hơn trong hạ tầng. Khi bắt đầu tìm hiểu các tùy chọn hosting hiện đại với AWS và Amazon GameLift, họ đã đặt ra ba mục tiêu rõ ràng:

**Mở rộng Trải nghiệm Người chơi**  
Hạ tầng cần phải có khả năng mở rộng để đáp ứng nhu cầu cao điểm của người chơi trong các mùa giải mới và sự kiện.

**Hiện đại hóa Hosting**  
Việc chuyển từ hạ tầng lai với bare metal sang nền tảng cloud-based sẽ mang lại môi trường gameplay có thể mở rộng cho cộng đồng người chơi toàn cầu.

**Duy trì Tốc độ Phát triển**  
Giải pháp mới phải tích hợp trơn tru với quy trình phát triển, kiểm thử và triển khai hiện có của Respawn — mà không làm chậm tiến độ phát triển.

> "Giảm thiểu gián đoạn cho cộng đồng người chơi là yếu tố then chốt quyết định thành công của cuộc di chuyển. Sự phối hợp chặt chẽ giữa Respawn và đội Amazon GameLift Servers đã biến điều đó thành hiện thực."  
> — Robert LaCruise, Giám đốc Kỹ thuật Chính, Dịch vụ Trò chơi Trực tuyến, Apex Legends

---

## Lập kế hoạch di chuyển

Để đảm bảo Amazon GameLift Servers có thể đáp ứng các yêu cầu này, Respawn đã hợp tác với AWS và Code Wizards Group — đối tác lâu năm chuyên về di chuyển trong ngành game — để tiến hành đánh giá chi tiết.

Amazon GameLift mang lại kinh nghiệm từ việc hỗ trợ nhiều game nổi tiếng như Dead by Daylight, Mortal Kombat 1, và Marvel SNAP, cùng khả năng mở rộng lên đến 100 triệu người chơi đồng thời.

Các nhóm đã xác định 3 bằng chứng kỹ thuật chính (proof-of-concepts):

**Main Flow**: Matchmaking và phân bổ máy chủ mượt mà cho tất cả người chơi.

**Zero Downtime Updates**: Khả năng triển khai cập nhật mà không gây gián đoạn.

**Metrics & Observability**: Dữ liệu thời gian thực để giám sát và cải thiện trải nghiệm người chơi.

> "Chúng tôi tin rằng dịch vụ của AWS mang lại hiệu suất tốt nhất cho người chơi so với các giải pháp khác. Hơn nữa, AWS đã trở thành chuẩn cho nhiều game FPS khác, và chúng tôi tin người chơi toàn cầu sẽ nhận thấy lợi ích từ việc di chuyển này."  
> — Robert LaCruise, Giám đốc Kỹ thuật Chính, Dịch vụ Trò chơi Trực tuyến, Apex Legends

---

## Thử nghiệm proof-of-concept

Để xác thực chiến lược di chuyển, các nhóm đã xây dựng và kiểm thử nhiều công nghệ mới.

### Matchmaking liền mạch

Bước đầu tiên là xây dựng một "player redirect layer" có thể chuyển hướng người chơi sang Amazon GameLift Servers mà không cần thay đổi logic backend của game, giúp tránh các thay đổi rủi ro và vòng QA không cần thiết.

Ngoài ra, một Game Server Wrapper cũng được tạo ra để tận dụng build máy chủ game hiện có mà không cần chỉnh sửa, đồng thời bổ sung khả năng quan sát và logging. Tất cả đều được phát triển bởi Code Wizards Group và là chìa khóa của cuộc di chuyển.

> "Chúng tôi hiểu trải nghiệm người chơi là điều cực kỳ quan trọng với Respawn. Chúng tôi đã tạo Wrapper và Portal để hiện thực hóa điều này. Kết quả là không có downtime trong trải nghiệm người chơi."  
> — Stuart Muckley, CEO & Founder, Code Wizards Group

### Zero Downtime Updates

Code Wizards đã phát triển hệ thống tự động di chuyển người chơi sang máy chủ nâng cấp sau khi trận đấu kết thúc — cho phép triển khai bản cập nhật mà không làm gián đoạn gameplay.

> "Các mục tiêu hướng đến người chơi đã được đưa vào thiết kế kiến trúc… đảm bảo rằng triển khai không downtime là sự thật từ đầu đến cuối."  
> — Jared Cugno, Trưởng bộ phận Kỹ thuật, Apex Legends

### Giám sát Thời gian Thực

Các dịch vụ monitoring và observability mang lại cho Respawn cái nhìn rõ ràng về tình trạng phiên chơi, độ trễ và hiệu suất máy chủ — cho phép phát hiện và xử lý kịp thời các sự cố.

> "Chúng tôi đang tận dụng hạ tầng toàn cầu của AWS để liên tục tối ưu hóa hiệu suất máy chủ và mạng. Giờ đây, chúng tôi có nhiều tùy chọn và sự linh hoạt hơn trên nền tảng hạ tầng mới."  
> — Robert LaCruise, Giám đốc Kỹ thuật Chính, Dịch vụ Trò chơi Trực tuyến, Apex Legends

### Lợi ích Backend

Sau quá trình thử nghiệm và tối ưu hóa, Respawn hiện đang hưởng lợi từ:

- Mạng lưới toàn cầu, độ trễ thấp của AWS
- Công cụ AWS hỗ trợ debug và tuning server
- Tối ưu chi phí với nhiều lựa chọn compute instance
- Giữ nguyên pipeline phát triển hiện có nhờ wrapper và redirect layer
- Kiểm soát nhiều hơn qua Amazon GameLift Servers API như TerminationGameSession và queue placement override

Điều này có nghĩa là Respawn không cần làm chậm phát triển hay thay đổi pipeline nhưng vẫn có được công cụ tốt hơn — mà không đánh đổi tốc độ.

![Portal Translation Layer](../../images/3-BlogsTranslated/blog1-image-2.png)

*Hình 1: Lớp dịch "Portal" chuyển hướng lưu lượng từ backend hiện có sang Amazon GameLift Servers.*

---

## Quá trình di chuyển 10 ngày

Sau giai đoạn đánh giá, Respawn đã tiến hành cắt chuyển lưu lượng theo từng khu vực, bắt đầu từ những khu vực nhỏ nhất để giảm thiểu tác động và tăng độ tin cậy dần dần.

AWS cung cấp:

- Hỗ trợ kỹ thuật trực tiếp
- Công cụ giám sát thời gian thực
- Hệ thống rollback đã được kiểm thử trước

Trước khi cắt chuyển, các nhóm đã:

- Xác định ngưỡng rollback
- Xác thực pipeline cảnh báo
- Chạy thử nghiệm di chuyển giả lập

Trong quá trình di chuyển, họ liên tục giám sát:

- Kết nối người chơi
- Độ trễ và jitter
- Thời gian chờ trong hàng (queue time)
- Tỷ lệ thành công khi phân bổ trận đấu

Nhờ hệ thống tự động và cảnh báo thời gian thực, đội ngũ luôn sẵn sàng rollback nếu cần. Nhưng thực tế, quá trình đã diễn ra suôn sẻ, không downtime trong suốt 10 ngày.

Respawn đã chia sẻ kết quả ban đầu trong bản cập nhật tháng trước với cộng đồng, đồng thời tiếp tục theo dõi và tối ưu trải nghiệm người chơi.

---

## Kết luận

Việc di chuyển Apex Legends sang Amazon GameLift Servers cho thấy điều gì có thể đạt được khi hiện đại hóa hạ tầng với người chơi làm trung tâm.

Được hậu thuẫn bởi AWS và Code Wizards Group, Respawn đã thực hiện một cuộc di chuyển quy mô lớn trong thời gian kỷ lục, mà không làm gián đoạn gameplay hay pipeline phát triển.

> "Đội ngũ di chuyển đã trải qua nhiều thử thách… Họ đã làm rất tốt, và tôi rất mong chờ tương lai."  
> — Jared Cugno, Trưởng bộ phận Kỹ thuật, Apex Legends

---

## Tác giả

![Michael Jackson](../../images/3-BlogsTranslated/blog1-author-1.png)

**Michael Jackson:**  
Michael Jackson là Giám đốc Phát triển Kinh doanh Toàn cầu của Amazon GameLift Services, tập trung vào các giải pháp máy chủ trò chơi và phát trực tuyến trò chơi tại Amazon. Ông có kinh nghiệm về lưu trữ được quản lý và công nghệ đám mây được thiết kế riêng cho ngành công nghiệp trò chơi. Michael đã giúp một số studio sáng tạo nhất thế giới hiện đại hóa cơ sở hạ tầng, giảm thiểu rủi ro vận hành và triển khai trên quy mô toàn cầu.

![David Holladay](../../images/3-BlogsTranslated/blog1-author-2.png)

**David Holladay:**  
David Holladay là người đứng đầu bộ phận Tiếp thị sản phẩm trò chơi của AWS.

![Juho Jantunen](../../images/3-BlogsTranslated/blog1-author-3.png)

**Juho Jantunen:**  
Juho Jantunen là Kiến trúc sư Giải pháp Chính Toàn cầu của nhóm AWS for Games, chuyên về các giải pháp lưu trữ máy chủ và backend cho game. Anh có kinh nghiệm trong ngành công nghiệp game và công nghệ đám mây, đồng thời đã xây dựng và vận hành backend game trên AWS cho nhiều tựa game với hàng triệu người chơi.
