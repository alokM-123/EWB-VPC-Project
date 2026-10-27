# AWS VPC Peering Project

## AWS VPC Peering with EC2 Private Connectivity

##  Overview

This project demonstrates private communication between two Amazon Virtual Private Clouds (VPCs) using **AWS VPC Peering**. It enables connectivity between EC2 instances in different VPCs using private IPv4 addresses without requiring public internet communication between them.

##  Architecture

### VPC Configuration

| Resource | Name | CIDR Block / Details |
|---|---|---|
| VPC A | EWB A | `20.20.0.0/16` |
| VPC B | EWB B | `20.40.0.0/16` |
| Public Subnet | EWB A Public Subnet-1 | `20.20.1.0/24` |
| Private Subnet | EWB A Private Subnet-1 | `20.20.2.0/24` |
| Private Subnet | EWb B Private Subnet-2 | `20.40.1.0/24` |
| Internet Gateway | `EWB_IGW` | `igw-0e807432b02981259` |
| VPC Peering Connection | `ewb-peering` | Connects VPC A and VPC B |

### EC2 Instances

| Instance Name | Operating System | Purpose |
|---|---|---|
| `EWB-BASTION` | Ubuntu Linux | Bastion host for SSH access |
| `EWB-A` | Ubuntu Linux | EC2 instance in VPC A's private subnet |
| `EWB-B` | Ubuntu Linux | EC2 instance in VPC B's private subnet |

##  Routing Configuration

Route tables were configured to enable communication between the two VPCs through the VPC peering connection.

- **VPC A:** Traffic destined for `20.40.0.0/16` is routed through the VPC peering connection.
- **VPC B:** Traffic destined for `20.20.0.0/16` is routed through the VPC peering connection.
- Existing local routes were retained in both VPCs.

##  Security Configuration

Security groups were configured to allow the required SSH and ICMP traffic for remote access and connectivity testing.

### EWB-BASTION-SG
- **SSH (TCP 22):** Allowed from the current public IPv4 address with a `/32` CIDR.

### EWB-A-SG
- **SSH (TCP 22):** Allowed from `65.0.85.26/32`.
- **Custom ICMP - IPv4 Echo Request:** Allowed from `20.40.1.89/32` (EWB-B).

### EWB-B-SG
- **SSH (TCP 22):** Allowed from `65.0.85.26/32`.
- **Custom ICMP - IPv4 Echo Request:** Allowed from `20.20.2.247/32` (EWB-A).

These rules were configured to support SSH access and ping tests between the instances.

##  Testing and Results

Connectivity was tested in both directions between EC2 instances EWB-A and EWB-B using their private IPv4 addresses.

### 1. Connectivity from EWB-A to EWB-B

SSH access sequence:

```bash
ssh -i ewb-project.pem ubuntu@65.0.85.26
ssh -i ewb-project.pem ubuntu@20.20.2.247
```

Ping test:

```bash
ping -c 4 20.40.1.89
```

**Result:**

```text
4 packets transmitted, 4 received, 0% packet loss, time 3087ms
rtt min/avg/max/mdev = 0.210/0.272/0.394/0.074 ms
```

### 2. Connectivity from EWB-B to EWB-A

SSH access sequence:

```bash
ssh -i ewb-project.pem ubuntu@65.0.85.26
ssh -i ewb-project.pem ubuntu@20.40.1.89
```

Ping test:

```bash
ping -c 4 20.20.2.247
```

**Result:**

```text
4 packets transmitted, 4 received, 0% packet loss, time 3055ms
rtt min/avg/max/mdev = 0.183/0.212/0.239/0.020 ms
```

###  Outcome

Both ping tests completed successfully with **0% packet loss**, demonstrating successful bidirectional ICMP connectivity between the private EC2 instances through VPC peering.

##  Skills Demonstrated

- Amazon VPC configuration
- IPv4 CIDR addressing and subnet creation
- Public and private subnet architecture
- Route tables and network routing
- VPC Peering configuration
- Amazon EC2 instance management
- Security group configuration
- Linux SSH access
- Network connectivity testing using `ping`
- AWS networking troubleshooting

##  Cleanup

To avoid unnecessary AWS charges, terminate EC2 instances and delete project resources when they are no longer required.

Before deleting resources, remove dependencies in the appropriate order, including VPC peering connections, network interfaces, subnets, route tables, and VPCs. Release any associated Elastic IP addresses when they are no longer needed.

##  Author

**ALOK MUNGAPATIL**
