# Network-simple-SOHO-lab

Enterprise Network Infrastructure

Mô hình mạng doanh nghiệp được thiết kế và triển khai trên Cisco Packet Tracer, tập trung vào VLAN segmentation, inter-VLAN routing, DHCP, OSPF, ACL, NAT/PAT và Dual-ISP failover.

Project Overview

Dự án mô phỏng hệ thống mạng cho một doanh nghiệp có nhiều phòng ban và nhiều khu vực làm việc.

Mục tiêu chính:

Phân tách mạng theo phòng ban bằng VLAN.
Định tuyến giữa các VLAN.
Cấp phát IP tập trung bằng DHCP.
Sử dụng OSPF cho định tuyến động.
Kiểm soát truy cập bằng ACL.
Cho phép mạng nội bộ truy cập Internet thông qua NAT/PAT.
Triển khai hai ISP để dự phòng kết nối Internet.
Sao lưu cấu hình thiết bị thông qua TFTP.
Áp dụng PortFast và BPDU Guard để tăng cường bảo mật Layer 2.
Business Requirements
Requirement	Description
Network Segmentation	Tách các phòng ban thành các VLAN riêng
Centralized DHCP	DHCP Server cấp IP cho các VLAN thông qua DHCP Relay
Inter-VLAN Routing	Các VLAN được định tuyến thông qua Router-on-a-Stick
Dynamic Routing	Sử dụng OSPF Area 0
Guest Isolation	Guest không được truy cập TFTP Server
Internet Access	Các mạng nội bộ có thể truy cập Internet
ISP Redundancy	ISP-2 hoạt động như đường dự phòng cho ISP-1
Configuration Backup	Sao lưu cấu hình bằng TFTP
Layer 2 Security	Sử dụng PortFast và BPDU Guard
Network Topology
<img width="1178" height="620" alt="image" src="https://github.com/user-attachments/assets/95a6bfb8-3158-436e-9480-314631a603c0" />

VLAN & IP Addressing
VLAN	Department	Network	Gateway
10	ADMIN	192.168.0.0/28	192.168.0.1
20	FINANCE	192.168.0.16/28	192.168.0.17
30	EMPLOYEES	192.168.2.0/25	192.168.2.1
40	GUESTS	192.168.3.0/24	192.168.3.1
Infrastructure Networks
Link	Network
R1 ↔ DHCP Server	192.168.1.0/30
R1 ↔ TFTP Server	192.168.1.4/30
R1 ↔ ISP-1	203.10.133.0/30
R1 ↔ ISP-2	204.120.0.0/30
Design Decisions
VLAN Segmentation

Mỗi phòng ban được đặt trong một VLAN riêng nhằm:

Giảm broadcast domain.
Quản lý IP dễ dàng hơn.
Tạo ranh giới để áp dụng security policy.
Dễ dàng mở rộng hệ thống.
VLSM

Các VLAN sử dụng subnet có kích thước khác nhau dựa trên nhu cầu thiết bị.

ADMIN       /28
FINANCE     /28
EMPLOYEES   /25
GUESTS      /24

Các mạng point-to-point sử dụng /30 vì chỉ cần hai địa chỉ IP usable.

Router-on-a-Stick

R1 sử dụng một trunk 802.1Q để phục vụ nhiều VLAN:

G0/0/1.10 → VLAN 10
G0/0/1.20 → VLAN 20
G0/0/1.30 → VLAN 30
G0/0/1.40 → VLAN 40

Điều này cho phép R1 thực hiện inter-VLAN routing mà không cần một physical interface riêng cho từng VLAN.

Centralized DHCP

DHCP Server được đặt ở một mạng riêng.

Các VLAN sử dụng:

ip helper-address 192.168.1.2

để chuyển DHCP request tới DHCP Server.

OSPF

OSPF Area 0 được sử dụng để trao đổi routing information giữa các thiết bị Layer 3.

Lựa chọn này giúp giảm số lượng static route cần cấu hình thủ công khi topology phát triển.

Guest Security

Guest VLAN được xem là mạng không tin cậy.

Policy chính:

GUEST ──X──> TFTP Server
GUEST ─────> Internet

ACL được sử dụng để thực thi policy này.

Dual-ISP

Hai ISP được triển khai theo mô hình:

ISP-1 → Primary
ISP-2 → Backup

ISP-2 sử dụng floating static default route với Administrative Distance cao hơn.

NAT/PAT

Các địa chỉ IP nội bộ là private IP nên NAT/PAT được sử dụng để cho phép nhiều client nội bộ truy cập mạng bên ngoài thông qua địa chỉ WAN.

Technologies Used
Cisco Packet Tracer
Cisco IOS
VLAN
802.1Q Trunking
Router-on-a-Stick
VLSM
DHCP / DHCP Relay
OSPF
ACL
NAT / PAT
Dual-ISP
TFTP
PortFast
BPDU Guard
Testing

Các chức năng được kiểm tra theo từng requirement:

Test	Expected Result
VLAN assignment	Client nằm đúng VLAN
Trunk	Các VLAN cần thiết được truyền qua trunk
DHCP	Client nhận đúng subnet và gateway
Inter-VLAN Routing	Các VLAN được routing đúng policy
OSPF	Routing information được học qua OSPF
Guest → TFTP	Traffic bị chặn
NAT/PAT	Client nội bộ truy cập được mạng ngoài
ISP-1 failure	Traffic chuyển sang ISP-2
ISP-1 recovery	ISP-1 trở lại đường ưu tiên
TFTP Backup	Configuration được sao lưu
Result

Dự án đã xây dựng một mô hình mạng doanh nghiệp bao gồm:

VLAN Segmentation
        ↓
Inter-VLAN Routing
        ↓
DHCP / DHCP Relay
        ↓
OSPF
        ↓
ACL
        ↓
NAT/PAT
        ↓
Dual-ISP
        ↓
Failover

Project cũng bao gồm các kịch bản troubleshooting như:

VLAN bị cấu hình sai.
VLAN bị thiếu trên trunk.
DHCP Relay sai.
OSPF adjacency lỗi.
ACL chặn nhầm traffic.
ISP chính bị mất kết nối.
NAT cấu hình sai.

Qua đó project thể hiện không chỉ khả năng cấu hình Cisco IOS mà còn khả năng thiết kế IP addressing, lựa chọn kiến trúc mạng, triển khai, kiểm thử và troubleshooting.
