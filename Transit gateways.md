# AWS Transit Gateway (TGW) Multi-VPC Architecture & Troubleshooting Guide

## 📌 Project Overview
Is project mein humne **AWS Transit Gateway (TGW)** ka use karke **4 isolated VPCs** ke beech high-speed, secure, aur centralized inter-VPC communication set up kiya hai. Is documentation mein infrastructure setup ke sath-sath troubleshoot kiye gaye real-world routing & firewall errors bhi cover kiye gaye hain.

---

## 🏗️ Architecture Diagram

```text
               +----------------------------------+
               |   Management VPC (168.192.0.0/16)|
               |     [ Ubuntu Bastion Host ]      |
               +----------------+-----------------+
                                |
                                v
               +----------------------------------+
               |      AWS TRANSIT GATEWAY         |
               |      (tgw-01d4017805769...)      |
               +---+------------+-------------+---+
                   |            |             |
        +----------+            |             +----------+
        |                       v                        |
        v              +-----------------+               v
+---------------+      | VPC 2           |      +---------------+
| VPC 1         |      | (10.2.0.0/16)   |      | VPC 3         |
| (10.1.0.0/16) |      +-----------------+      | (10.3.0.0/16) |
+---------------+                               +---------------+
