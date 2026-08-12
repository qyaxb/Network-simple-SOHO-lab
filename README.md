# Network-simple-SOHO-lab

1. Mục tiêu dự án

Xây dựng và triển khai mô hình mạng doanh nghiệp nhỏ trên Cisco Packet Tracer, tập trung vào khả năng phân tách mạng, định tuyến, quản lý địa chỉ IP, bảo mật và dự phòng kết nối Internet.

Dự án nhằm áp dụng kiến thức Cisco/CCNA vào một mô hình gần với môi trường doanh nghiệp thực tế, thay vì chỉ thực hiện các bài lab cấu hình riêng lẻ.

Các chức năng chính:

Phân chia mạng thành nhiều VLAN theo phòng ban.
Định tuyến giữa các VLAN bằng Router-on-a-Stick.
Sử dụng DHCP Server tập trung và DHCP Relay.
Sử dụng OSPF để định tuyến động.
Kiểm soát truy cập bằng ACL.
Triển khai NAT/PAT để các mạng nội bộ truy cập Internet.
Sử dụng hai ISP để tăng khả năng dự phòng.
Sử dụng TFTP để sao lưu cấu hình thiết bị.
Áp dụng một số cơ chế bảo vệ Layer 2 như PortFast và BPDU Guard.
2. Yêu cầu nghiệp vụ

Doanh nghiệp được giả định có bốn nhóm người dùng chính:

VLAN	Phòng ban	Yêu cầu
VLAN 10	ADMIN	Mạng dành cho bộ phận quản trị
VLAN 20	FINANCE	Mạng dành cho bộ phận tài chính
VLAN 30	EMPLOYEES	Mạng dành cho nhân viên
VLAN 40	GUESTS	Mạng dành cho khách

Các yêu cầu đặt ra:

Các phòng ban phải được tách biệt bằng VLAN.
Các VLAN phải có thể giao tiếp với nhau thông qua thiết bị Layer 3 khi chính sách cho phép.
Client phải có khả năng nhận địa chỉ IP tự động từ DHCP Server.
DHCP Server được đặt tập trung trong một subnet riêng.
Mạng Guest không được phép truy cập TFTP Server.
Các mạng nội bộ phải có khả năng truy cập Internet.
Hệ thống phải có hai đường kết nối ISP, trong đó một đường hoạt động chính và một đường dự phòng.
Khi đường ISP chính gặp sự cố, traffic phải có khả năng chuyển sang ISP dự phòng.
Hệ thống phải sử dụng OSPF để trao đổi thông tin định tuyến.
Cấu hình thiết bị cần có khả năng được sao lưu thông qua TFTP.
Các access port cần được bảo vệ khỏi một số sự cố Layer 2 cơ bản.
3. Topology

Topology gồm một router trung tâm, ba switch đại diện cho các tầng/khu vực trong doanh nghiệp, DHCP Server, TFTP Server và hai đường kết nối ISP.

                       INTERNET
                      /        \
                   ISP-1      ISP-2
                     |          |
                     +---- R1 --+
                          |
              +-----------+-----------+
              |           |           |
           DHCP Server  TFTP Server   LAN
                                      |
                                  Trunk 802.1Q
                                      |
                                  +---+---+
                                  |       |
                               FLOOR 1  FLOOR 2
                                  |       |
                                  +--- FLOOR 3
                                      |
                       +------+------+------+------+
                       |      |      |      |
                    VLAN 10 VLAN 20 VLAN 30 VLAN 40
                    ADMIN  FINANCE EMPLOYEE GUEST
Kế hoạch địa chỉ IP
Mạng	Subnet	Gateway/Thiết bị
VLAN 10	192.168.0.0/28	192.168.0.1
VLAN 20	192.168.0.16/28	192.168.0.17
VLAN 30	192.168.2.0/25	192.168.2.1
VLAN 40	192.168.3.0/24	192.168.3.1
R1 ↔ DHCP	192.168.1.0/30	R1 .1, DHCP .2
R1 ↔ TFTP	192.168.1.4/30	R1 .5, TFTP .6
R1 ↔ ISP-1	203.10.133.0/30	R1 .1
R1 ↔ ISP-2	204.120.0.0/30	R1 .1
4. Các quyết định thiết kế
VLAN theo phòng ban

Thay vì để toàn bộ client nằm trong cùng một broadcast domain, mỗi phòng ban được đặt trong một VLAN riêng.

Điều này giúp:

Giảm broadcast domain.
Dễ quản lý địa chỉ IP.
Dễ áp dụng ACL.
Dễ mở rộng chính sách bảo mật.
Sử dụng VLSM

Các VLAN không sử dụng cùng một kích thước subnet:

ADMIN      → /28
FINANCE    → /28
EMPLOYEES  → /25
GUESTS     → /24

Các phòng ban có số lượng thiết bị khác nhau nên được cấp subnet có kích thước phù hợp thay vì cấp cùng một subnet cho tất cả.

Router-on-a-Stick

R1 sử dụng các subinterface:

G0/0/1.10 → VLAN 10
G0/0/1.20 → VLAN 20
G0/0/1.30 → VLAN 30
G0/0/1.40 → VLAN 40

Cách này cho phép một interface vật lý của router thực hiện định tuyến giữa nhiều VLAN thông qua trunk 802.1Q.

DHCP tập trung

Thay vì đặt DHCP Server trong từng VLAN, một DHCP Server được sử dụng cho toàn bộ mạng.

Các subinterface trên R1 sử dụng:

ip helper-address 192.168.1.2

để chuyển tiếp DHCP request từ các VLAN tới DHCP Server.

OSPF

OSPF Area 0 được sử dụng để tự động trao đổi thông tin định tuyến giữa các mạng Layer 3.

Điều này giúp giảm sự phụ thuộc vào static route khi topology phát triển.

ACL cho Guest

Guest VLAN được coi là mạng ít tin cậy hơn.

Chính sách quan trọng nhất hiện tại:

GUEST → TFTP       DENY
GUEST → các mạng khác   tùy chính sách
GUEST → Internet   ALLOW

ACL được sử dụng để thực hiện chính sách này.

Dual-ISP

Hai ISP được triển khai theo mô hình:

ISP-1 = Primary
ISP-2 = Backup

Default route tới ISP-2 có Administrative Distance cao hơn để chỉ được sử dụng khi route chính không còn tồn tại.

NAT/PAT

Các địa chỉ IP nội bộ là private IP nên NAT/PAT được sử dụng để cho phép nhiều client nội bộ truy cập mạng bên ngoài thông qua địa chỉ WAN.

5. Kết quả

Sau khi triển khai, hệ thống được thiết kế để đạt các kết quả sau:

Hạng mục	Kết quả
VLAN	Các phòng ban được phân tách thành VLAN 10/20/30/40
Inter-VLAN Routing	R1 thực hiện định tuyến giữa các VLAN
DHCP	Các VLAN sử dụng DHCP Server tập trung thông qua DHCP Relay
OSPF	Các mạng Layer 3 được quảng bá bằng OSPF
ACL	Guest bị hạn chế truy cập TFTP Server
NAT/PAT	Client nội bộ có thể sử dụng địa chỉ WAN để truy cập Internet
Dual-ISP	ISP-1 được ưu tiên, ISP-2 đóng vai trò dự phòng
TFTP	Có khả năng sao lưu cấu hình thiết bị
Layer 2 Security	PortFast/BPDU Guard được áp dụng trên các access port phù hợp
