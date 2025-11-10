---
title: "Bản đề xuất"
date: 2025-11-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# Security Scan Pipeline on AWS Cloud 
## Giải pháp tự động hoá phân tích bảo mật mã nguồn trong DevSecOps Pipeline  

### 1. Tóm tắt điều hành  
Security Scan & Deployment Pipeline là dự án hướng tới việc tự động hóa toàn bộ quy trình build – scan – deploy của ứng dụng trên nền tảng AWS Cloud, tích hợp trực tiếp với GitHub để triển khai mô hình DevSecOps thực hành.

Mục tiêu của hệ thống là:

- Phát hiện sớm lỗi bảo mật và code smell trong mã nguồn bằng Amazon CodeGuru Reviewer.
- Tự động build và tạo artifact khi có commit mới.
- Lưu trữ artifact an toàn trên Amazon S3.
- Triển khai tự động ứng dụng lên Amazon EC2 qua AWS CodeDeploy.

Pipeline này giúp rút ngắn thời gian phát hành, đảm bảo tính bảo mật, đồng thời duy trì tính nhất quán trong toàn bộ quy trình DevSecOps của nhóm phát triển.

### 2. Tuyên bố vấn đề  
*Vấn đề hiện tại*  
+ Quá trình build và kiểm tra mã nguồn thủ công dễ bỏ sót lỗi bảo mật.
+ Việc triển khai ứng dụng lên EC2 không có cơ chế tự động, tốn thời gian và dễ sai sót.
+ Không có hệ thống tập trung để theo dõi, lưu trữ artifact hoặc báo cáo kết quả scan bảo mật.

*Giải pháp đề xuất*  
Xây dựng AWS CodePipeline kết nối với GitHub bằng webhook để tự động kích hoạt quy trình mỗi khi có commit mới.
Quy trình gồm:

+ 1. CodePipeline nhận tín hiệu commit từ GitHub.
+ 2. CodeBuild build mã nguồn và quét bảo mật bằng Amazon CodeGuru Reviewer.
+ 3. Kết quả và artifact build được tải lên Amazon S3.
+ 4. CodeDeploy lấy artifact từ S3 để tự động triển khai lên EC2.
+ 5. Quản lý toàn bộ thông tin quy trình thông qua AWS Console hoặc CloudWatch Logs.

*Lợi ích và hoàn vốn đầu tư (ROI)*  
Giảm 60–70% thời gian triển khai thủ công.

Tăng tính bảo mật với CodeGuru tự động phát hiện lỗi code.

Đảm bảo quy trình CI/CD khép kín, giảm rủi ro cấu hình sai.  

Chi phí tiết kiệm.

### 3. Kiến trúc giải pháp  
Pipeline được thiết kế theo mô hình DevSecOps Serverless Hybrid, sử dụng kết hợp các dịch vụ AWS để đảm bảo tự động hóa từ đầu đến cuối.

**Sơ đồ kiến trúc tổng quan**:

![DevOps / Pipeline Engineer](/images/2-Proposal/diagram.png)


*Dịch vụ AWS sử dụng*  
- *AWS CodePipeline*: Dàn điều phối pipeline tự động.
- *AWS CodeBuild*: Build + quét bảo mật với CodeGuru. 
- *Amazon CodeGuru Reviewer*: Phân tích và báo cáo vấn đề trong code. 
- *Amazon S3*: Lưu trữ artifact và kết quả quét.  
- *AWS CodeDeploy*: Triển khai artifact lên EC2.
- *Amazon EC2*: Môi trường chạy ứng dụng.
- *Amazon SNS*: Gửi thông báo build/deploy.  
- *CloudWatch, CloudTrail, GuardDuty, Detective, Security Hub*: Giám sát, điều tra và tổng hợp bảo mật.


### 4. Triển khai kỹ thuật  
*Các giai đoạn triển khai*  
1. *Kết nối GitHub với CodePipeline*: Tạo webhook và cấp quyền truy cập qua OIDC hoặc PAT và Cấu hình pipeline tự động chạy khi có commit. 
2. *Tạo CodeBuild Project*: Môi trường: Ubuntu + Node.js/Python. Viết file buildspec.yml để build và quét CodeGuru.
3. *Cấu hình CodeDeploy*: Tạo Deployment Group liên kết EC2 instance. Viết file appspec.yml:  
4. *Giám sát & bảo mật*: 
    + CloudWatch ghi log pipeline & ứng dụng.
    + AWS CloudTrail Ghi lịch sử API và hành động người dùng.
    + GuardDuty phát hiện hành vi bất thường.
    + Detective phân tích nguyên nhân sự cố.
    + Security Hub tổng hợp báo cáo cho toàn hệ thống.
    + SNS gửi thông báo trạng thái pipeline cho Developer.

### 5. Lộ trình & Mốc triển khai  
- *Trước thực tập (Tháng 9)*: 1 tháng lên kế hoạch. Nghiên cứu, tạo IAM Role, bucket S3, EC2, kết nối GitHub.  
- *Thực tập (Tháng 10-11)*:  
    - Tháng 10: Cấu hình CodePipeline + webhook GitHub.  
    - Tháng 11: Viết file buildspec.yml, appspec.yml, tích hợp CodeGuru. Thêm các dịch vụ giám sát và bảo mật (SNS, GuardDuty, Detective, Security Hub). 
- *Sau triển khai*: Nghiên cứu thêm.

### 6. Ước tính ngân sách  
Có thể xem chi phí trên [AWS Pricing Calculator](https://calculator.aws/#/estimate?id=621f38b12a1ef026842ba2ddfe46ff936ed4ab01)  
Hoặc tải [tệp ước tính ngân sách](../attachments/budget_estimation.pdf).  

*Chi phí hạ tầng*  
- AWS CodePipeline: 0.40 USD/tháng (2 pipeline chạy mỗi ngày).  
- AWS CodeBuild: 0.35 USD/tháng (30 build, mỗi build 5 phút).  
- Amazon S3: 0.10 USD/tháng (Artifact & log).  
- AWS CodeDeploy: 0.20 USD/tháng (2 lần deploy/tháng).  
- Amazon EC2 (t2.micro): 0.10 USD/tháng (Instance chạy ứng dụng).  
- CloudWatch + SNS: 0.05 USD/tháng (Giám sát & thông báo).  

*Tổng*: ≈ 1.2 USD/tháng, 14.4 USD/12 tháng ( AWS Free Tier)

### 7. Đánh giá rủi ro  
*Ma trận rủi ro*  
- Lỗi webhook GitHub – CodePipeline không kích hoạt tự động
- Timeout khi quét CodeGuru Reviewer hoặc CodeBuild build quá lâu
- Artifact lỗi hoặc thiếu khi deploy lên EC2
- Pipeline dừng giữa chừng do lỗi quyền IAM  
- Chi phí vượt giới hạn AWS Free Tier

*Chiến lược giảm thiểu*  
- Kiểm tra lại cấu hình IAM Role, OAuth Token, và GitHub Webhook Trigger.
- Giới hạn phạm vi repo, tối ưu buildspec.yml, chỉ quét phần code thay đổi.
- Thực hiện unit test trước build, dùng checksum để kiểm tra artifact integrity.
- Kiểm tra và xác nhận IAM Policy của CodeBuild, CodeDeploy, S3 và SNS.
- Thiết lập AWS Budgets và Billing Alerts gửi qua SNS Email.

*Kế hoạch dự phòng*  
- Nếu pipeline không tự chạy, có thể thủ công kích hoạt pipeline trong AWS Console hoặc dùng CLI 
- Nếu timeout xảy ra, chia nhỏ pipeline (ví dụ tách riêng stage build và scan) hoặc tạo job dự phòng chạy vào khung giờ thấp tải.
- Nếu deploy lỗi, CodeDeploy sẽ rollback tự động về phiên bản artifact trước đó trên S3.
- Tạo IAM Backup Role với quyền tương tự, có thể kích hoạt tạm thời khi policy chính bị lỗi.  
- Khi vượt giới hạn, tạm ngưng pipeline không cần thiết, dọn dẹp S3 bucket và tắt EC2 tạm thời.

### 8. Kết quả kỳ vọng  
*Kỹ thuật đạt được*: 
+ Tự động hóa quy trình CI/CD và Security Scan.
+ Phân tích code và bảo mật liên tục bằng CodeGuru.
+ Triển khai liền mạch qua CodeDeploy – EC2.
+ Giám sát toàn diện qua CloudWatch, Detective, Security Hub.
+ Cảnh báo email tức thời qua SNS.

*Giá trị dài hạn*: 
+ Mô hình DevSecOps mẫu dùng cho workshop, học tập, và triển khai thực tế.

Có thể mở rộng thêm
+ Kiểm thử tự động
+ Triển khai trên ECS/EKS hoặc AWS Lambda
+ Tích hợp thêm Trivy, SonarQube, Checkov để đa dạng hóa công cụ quét bảo mật.
