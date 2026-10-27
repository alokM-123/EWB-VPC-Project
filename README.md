AWS-VPC-Peering-Project
AWS VPC Peering with EC2 private connectivity

Overview
This project demonstrates private communication between two Amazon Virtual Private Clouds (VPCs) using VPC Peering.

Architecture
VPC A: 20.20.0.0/16
VPC B: 20.40.0.0/16
VPC A subnet: Public(20.20.1.0/24) and private subnets(20.20.2.0/24)
VPC B subnet: Private(20.40.1.0/24)
INTERNET GATEWAY EWB_IGW : igw-0e807432b02981259
VPC Peering: ewb-peering
EC2 Instance EWB-BASTION: Linux/UNIX (UBUNTU)
EC2 Instance EWB-A: Linux/UNIX (UBUNTU)
EC2 Instance EWB-B: Linux/UNIX (UBUNTU)

Routing Configuration
VPC A routes traffic for 20.40.0.0/16 through the peering connection.
VPC B routes traffic for 20.20.0.0/16 through the peering connection.
Existing local routes were retained.

Security Configuration
EWB-BASTION-SG: SSH / TCP 22 current public IPv4 /32
EWB-A-SG: [SSH / TCP 22 65.0.85.26/32] AND [Custom ICMP - IPv4 Echo request 20.40.1.89/32 (ewb-b)]
EWB-B-SG: [SSH / TCP 22 65.0.85.26/32][Custom ICMP - IPv4 Echo request 20.20.2.247/32 (ewb-a)]
Security groups were configured to permit the required traffic.
ICMP traffic from VPC A was allowed to test connectivity.

Testing and Results
Connectivity was tested from EC2 Instance A to EC2 Instance B using:
SSH -C EWB-PROJECT.PEM UBUNTU@65.0.85.26
SSH -C EWB-PROJECT.PEM UBUNTU@20.20.2.247
 ping -c 4 20.40.1.89
--- 20.40.1.89 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3087ms
rtt min/avg/max/mdev = 0.210/0.272/0.394/0.074 ms

Connectivity was tested from EC2 Instance B to EC2 Instance A using:
SSH -C EWB-PROJECT.PEM UBUNTU@65.0.85.26
SSH -C EWB-PROJECT.PEM UBUNTU@20.40.1.89
 ping -c 4 20.20.2.247
--- 20.20.2.247 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3055ms
rtt min/avg/max/mdev = 0.183/0.212/0.239/0.020 ms

Skills Demonstrated
Amazon VPC
IPv4 CIDR addressing and subnets
Route tables and routing
VPC Peering
Amazon EC2
Security groups
Linux networking and connectivity testing
Cleanup
Project resources should be terminated or deleted when no longer needed to avoid unnecessary AWS charges.

Author Name : ALOK MUNGAPATIL
