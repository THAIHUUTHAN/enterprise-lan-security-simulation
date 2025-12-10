# enterprise-lan-security-simulation
Thiết kế mạng cho công ty vừa và nhỏ với mô hình lớp lõi thu gọn - Collapsed Core
<img width="586" height="438" alt="image" src="https://github.com/user-attachments/assets/f88c8f7c-d3d5-4afb-a4ab-01508211f425" />

Sơ đồ Logic của mô hình: 
<img width="671" height="349" alt="image" src="https://github.com/user-attachments/assets/ee7a48a1-e7a2-45c6-8963-ff670d17a870" />

Phân vùng (Zoning):
  INSIDE (Security 100): Mạng nội bộ tin cậy.
  OUTSIDE (Security 0): Mạng Internet không tin cậy.
  DMZ (Security 50): Vùng đệm bán tin cậy.

Công ty hoạt động với khoảng 90 nhân sự gồm có 5 phòng ban. Mô hình mạng được thiết kế theo kiến trúc Cisco Collapsed Core (Lớp lõi thu gọn), tập trung khả năng định tuyến tại lớp Core và khả năng chuyển mạch/truy nhập tại lớp Access:
Phòng Kỹ thuật (20 nhân sự): Quản trị hệ thống, chứa các tài nguyên quan trọng (Server nội bộ, Máy Admin).
Phòng Marketing (20 nhân sự): Nhu cầu truy cập Internet cao, Wifi diện rộng.
Phòng Kế toán (20 nhân sự): Yêu cầu bảo mật dữ liệu tài chính khắt khe, mạng riêng biệt.
Phòng Kinh doanh (20 nhân sự): Số lượng nhân sự lớn nhất, nhu cầu truy cập dữ liệu liên tục.
Ban Giám đốc (3 người): Yêu cầu độ ưu tiên dịch vụ (QoS) và bảo mật cao.


Sử dụng các cơ chế bảo mật như:

1.Bảo mật Lớp 2 (Layer 2 Security)
 Đây là lớp dễ bị tấn công nhất vì các thiết bị Switch mặc định thường có   cấu hình "tin tưởng tất cả".
 
1.1 Port Security:
Kiểm soát thiết bị nào được phép kết nối vào cổng switch dựa trên địa chỉ MAC.
Cấu hình giới hạn số lượng MAC (ví dụ: tối đa 2) ngăn chặn việc người dùng tự ý cắm Hub/Switch cá nhân để mở rộng mạng trái phép.
1.2 DHCP Snooping:
Ngăn chặn Rogue DHCP Server. Switch sẽ xây dựng một cơ sở dữ liệu (Binding Database) ánh xạ giữa: [MAC Address - IP Address - VLAN - Port].
Chỉ các cổng được cấu hình là "Trusted" (cổng nối lên Core Switch) mới được phép gửi các gói tin cấp phát IP (DHCP Offer/Ack).
1.3 Dynamic ARP Inspection (DAI):
Sử dụng cơ sở dữ liệu của DHCP Snooping để xác thực các gói tin ARP trong mạng.
Ngăn chặn kẻ tấn công gửi thông báo ARP giả mạo để thực hiện tấn công Man-in-the-Middle.

2. Tường lửa và Phân vùng (Firewall Zoning)
Cisco ASA sử dụng công nghệ Stateful Inspection (Kiểm tra trạng thái), ưu việt hơn Router ACL thông thường (Stateless).
Zone-Based Policy: Hệ thống được chia thành 3 vùng với mức độ tin cậy (Security Level) khác nhau:
INSIDE (Level 100): Vùng tin cậy cao nhất (Mạng LAN).
OUTSIDE (Level 0): Vùng không tin cậy (Internet).
DMZ (Level 50): Vùng đệm chứa máy chủ công khai.
Nguyên tắc dòng chảy: Theo mặc định, lưu lượng được phép đi từ vùng có mức bảo mật cao sang thấp (Inside ra Outside), nhưng bị cấm theo chiều ngược lại. Điều này ngăn chặn hacker từ Internet chủ động kết nối vào mạng nội bộ.
