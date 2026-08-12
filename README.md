# SOHO Network Infrastructure

Mô hình mạng doanh nghiệp nhỏ/Home Office được thiết kế và triển khai trên Cisco Packet Tracer.

## Tổng quan

Dự án mô phỏng hệ thống mạng cho một doanh nghiệp có nhiều phòng ban và nhiều khu vực làm việc.

Các chức năng chính:

- Phân chia mạng bằng VLAN
- Inter-VLAN Routing bằng Router-on-a-Stick
- DHCP và DHCP Relay
- Định tuyến động bằng OSPF
- Kiểm soát truy cập bằng ACL
- NAT/PAT
- Hai ISP với cơ chế dự phòng
- Sao lưu cấu hình bằng TFTP
- PortFast và BPDU Guard

## Topology

<img width="1186" height="627" alt="image" src="https://github.com/user-attachments/assets/e2b6cdca-4fa4-45b3-94e8-4edcabdb963f" />


## Phân chia mạng

```text
VLAN 10 - ADMIN
192.168.0.0/28
Gateway: 192.168.0.1

VLAN 20 - FINANCE
192.168.0.16/28
Gateway: 192.168.0.17

VLAN 30 - EMPLOYEES
192.168.2.0/25
Gateway: 192.168.2.1

VLAN 40 - GUESTS
192.168.3.0/24
Gateway: 192.168.3.1

```
Document về dự án: https://docs.google.com/document/d/1Ru9yMy7W62ek3yqdUgbilZUew1_0s6J-GJgKD-yxP1k/edit?usp=sharing
