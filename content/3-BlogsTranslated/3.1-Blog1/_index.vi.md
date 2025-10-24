---
title: "Blog 1"
date: 2025-09-09
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Apex Legends Di chuyển sang Amazon GameLift trong chỉ 10 ngày

Respawn Entertainment gần đây đã di chuyển **Apex Legends** sang máy chủ **Amazon GameLift**, hoàn tất quá trình chuyển lưu lượng trong chỉ **10 ngày** mà không có thời gian chết mà người chơi nhìn thấy.  
Một ưu tiên then chốt của Respawn là thực hiện việc tích hợp và xác thực kỹ thuật mà không làm gián đoạn đội ngũ phát triển hoặc trải nghiệm người chơi. AWS đã đáp ứng yêu cầu đó bằng cách hợp tác với đối tác **Code Wizards Group** và sử dụng Chương trình Hỗ trợ Di chuyển Amazon GameLift để xây dựng kế hoạch di chuyển theo từng giai đoạn, ít rủi ro — kết quả là một trong những chuyển đổi hạ tầng do AWS dẫn đầu quy mô lớn nhất được thực hiện cho trò chơi dịch vụ trực tuyến.

---

## Xác định các ưu tiên

Là một trò chơi dịch vụ trực tuyến, Apex Legends không ngừng phát triển — không chỉ về gameplay và nội dung mà còn về chất lượng vận hành. Với hàng triệu người chơi trên toàn cầu, Respawn cần hạ tầng có tính **nhìn thấy**, **nhất quán** và **linh hoạt**. Khi bắt đầu nghiên cứu các lựa chọn hosting hiện đại với AWS và Amazon GameLift, họ đã xác định ba mục tiêu rõ ràng:

1. **Mở rộng trải nghiệm người chơi**  
   Hạ tầng cần mở rộng theo nhu cầu cao điểm của người chơi trong các mùa mới hoặc sự kiện.

2. **Hiện đại hóa hosting**  
   Di chuyển từ hạ tầng lai (hybrid) sử dụng máy chủ vật lý (bare metal) sang nền tảng đám mây để cung cấp môi trường chơi có khả năng mở rộng cho cộng đồng người chơi toàn cầu.

3. **Duy trì tốc độ phát triển (Dev Velocity)**  
   Giải pháp mới phải tích hợp mượt với quy trình hiện có của Respawn: xây dựng, kiểm thử và triển khai — mà không làm chậm nhịp phát triển trò chơi.

> “Minimizing the disruption for our player population was a critical success factor for the migration. The coordinated effort between Respawn and the Amazon GameLift Servers team made this a reality.”  
> — Robert LaCruise, Giám đốc Kỹ thuật Chính, Dịch vụ Game Trực tuyến, Apex Legends  

---

## Lập kế hoạch di chuyển

Để đảm bảo Amazon GameLift đáp ứng những yêu cầu trên, Respawn đã hợp tác với AWS và Code Wizards Group — đối tác lâu năm trong ngành game — tiến hành đánh giá chi tiết. Amazon GameLift đã hỗ trợ các trò chơi nổi bật như *Dead by Daylight*, *Mortal Kombat 1*, *Marvel SNAP*, và có khả năng mở quy mô tới **100 triệu người chơi đồng thời**.  

Cùng nhau, các nhóm xác định ba proof-of-concept kỹ thuật chính:

1. **Main Flow**: Đảm bảo quá trình ghép trận (matchmaking) và việc đặt máy chủ (server placement) diễn ra mượt mà cho tất cả người chơi  
2. **Zero Downtime Updates**: Có khả năng đẩy cập nhật mà không làm gián đoạn trải nghiệm người chơi  
3. **Metrics & Observability**: Thu thập dữ liệu thời gian thực để giám sát và cải thiện trải nghiệm người chơi  

> “We believe their (AWS) services offer the best overall player facing performance when compared to other alternatives. Additionally, AWS servers are standard for many other FPS titles and we believe our players will see the benefits of this migration globally.”  
> — Robert LaCruise, Giám đốc Kỹ thuật Chính, Dịch vụ Game Trực tuyến, Apex Legends  

---

## Triển khai Proof-of-Concept

Để xác minh chiến lược di chuyển, các nhóm đã xây dựng và thử nghiệm nhiều thành phần mới.

### Ghép trận mượt mà

Bước đầu tiên là phát triển **player redirect layer** để chuyển hướng người chơi sang máy chủ Amazon GameLift mà không thay đổi logic backend trò chơi, tránh các thay đổi mã rủi ro và chu kỳ QA lớn. Đồng thời, **Game Server Wrapper** được tạo để sử dụng bản build máy chủ trò chơi hiện tại mà không thay đổi, đồng thời bổ sung logging và khả năng quan sát (observability). Cả hai thành phần này do Code Wizards Group phát triển và là chìa khóa cho việc di chuyển thành công:

> “We knew that player experience was super important to Respawn and their players. We created the Wrapper and Portal to enable this. The end result was no downtime to the player experience.”  
> — Stuart Muckley, CEO & Founder, Code Wizards Group  

### Cập nhật không gián đoạn

Code Wizards phát triển hệ thống tự động di chuyển người chơi sang các máy chủ đã được cập nhật sau khi trận đấu kết thúc — tức là đẩy bản cập nhật mà không làm gián đoạn gameplay:

> “Player-facing goals were formalized into our architecture designs… ensuring that zero downtime deployments were a reality from start to finish.”  
> — Jared Cugno, Trưởng bộ phận Kỹ thuật, Apex Legends  

### Giám sát thời gian thực

Các dịch vụ giám sát và observability cung cấp cho Respawn cái nhìn rõ về sức khỏe phiên chơi, độ trễ, và hiệu năng máy chủ — từ đó phát hiện và xử lý vấn đề kịp thời:

> “We are leveraging the global infrastructure of AWS to continuously optimize server and network performance. We have more options and flexibility on the new infrastructure platform.”  
> — Robert LaCruise, Giám đốc Kỹ thuật Chính, Dịch vụ Game Trực tuyến, Apex Legends  

Sau khi xác minh ban đầu, các hệ thống này được đưa qua thử nghiệm cấp sản xuất, benchmark instance, và tuning hiệu năng.

Ở tầng backend, Respawn giờ tận hưởng các lợi ích:

- Mạng lưới toàn cầu của AWS với độ trễ thấp  
- Công cụ debug và tuning máy chủ của AWS  
- Hiệu quả chi phí được cải thiện nhờ lựa chọn instance phù hợp  
- Duy trì pipeline phát triển hiện có nhờ wrapper và redirect layer  
- Kiểm soát hosting tốt hơn thông qua các API Amazon GameLift như **TerminationGameSession** và **queue placement override**, hỗ trợ chức năng wrapper & redirect layer  

Respawn không phải làm chậm phát triển hoặc thay đổi pipeline — nhưng lại có những công cụ tốt hơn — mà không đánh đổi tốc độ.

---

## Quá trình di chuyển trong 10 ngày

Sau giai đoạn đánh giá, Respawn triển khai chuyển lưu lượng theo từng vùng (region-by-region) sang Amazon GameLift Servers — bắt đầu từ các khu vực nhỏ để giảm ảnh hưởng đến người chơi và xây dựng độ tin cậy từng bước. AWS hỗ trợ kỹ thuật trực tiếp, cung cấp công cụ observability thời gian thực, và hệ thống rollback đã được thử nghiệm trước khi ra mắt để đảm bảo ổn định suốt quá trình.  

Trước khi thực hiện cutover, các nhóm định nghĩa ngưỡng rollback, xác thực pipeline cảnh báo, và chạy các mô phỏng di chuyển để đảm bảo tính ổn định. Trong quá trình di chuyển thực tế, các chỉ số được giám sát liên tục:

- Kết nối người chơi  
- Độ trễ và dao động (latency & jitter)  
- Thời gian chờ vào hàng đợi  
- Tỷ lệ đặt trận thành công  

Nhờ hệ thống tự động và cảnh báo thời gian thực, các nhóm luôn sẵn sàng rollback hoặc can thiệp ngay nếu cần. Nhưng quá trình di chuyển diễn ra suôn sẻ — **không có thời gian chết mà người chơi nhận thấy** trong suốt 10 ngày.  

Respawn đã chia sẻ kết quả ban đầu tới cộng đồng người chơi và tiếp tục theo dõi hiệu năng và tối ưu trải nghiệm.

---

## Kết luận

Việc di chuyển **Apex Legends** sang Amazon GameLift cho thấy điều gì có thể đạt được khi hiện đại hóa hạ tầng được thực hiện với người chơi là trọng tâm.  
Được hỗ trợ bởi AWS và Code Wizards Group, Respawn đã triển khai một migration rủi ro cao trong thời gian kỷ lục — mà không làm gián đoạn gameplay hay pipeline phát triển.

> “The migration team were put through their paces several times… They did it well, and I’m looking forward to seeing what the future holds.”  
> — Jared Cugno, Trưởng bộ phận Kỹ thuật, Apex Legends  

Nếu bạn muốn, tôi có thể gửi bản dịch tiếng Việt đã format sẵn (file markdown) để bạn copy vào blog của bạn?  


