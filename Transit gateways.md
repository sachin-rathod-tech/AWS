# AWS Transit Gateway (TGW) Multi-VPC connecting

### 🚀 Step 1: Create 4 Virtual Private Clouds (VPCs)
<img width="458" height="184" alt="Screenshot 2026-08-31 002214" src="https://github.com/user-attachments/assets/48ccd4df-5b2d-4687-83f5-67c0701d5c58" />

#### Created 4 separate VPCs with distinct IP address ranges (CIDR blocks):

* VPC 1 : CIDR 10.1.0.0/16
* VPC 2 : CIDR 10.2.0.0/16
* VPC 3 : CIDR 10.3.0.0/16
* VPC 4 : CIDR 168.192.0.0/16
---

### Step 2: Create Subnets 
* inside all vpc 1 subnet create 
<img width="413" height="79" alt="Screenshot 2026-08-31 002259" src="https://github.com/user-attachments/assets/b1ff149e-705c-4d69-9373-950cc416bfe5" />

#### Launched Ubuntu EC2 instances in each VPC for connectivity testing

<img width="536" height="202" alt="Screenshot 2026-08-31 002040" src="https://github.com/user-attachments/assets/f4f0a146-d5b7-4460-abbc-f41bace93f01" />

---
### Step 3: Create Transit Gateway (TGW)
<img width="458" height="71" alt="Screenshot 2026-08-31 002429" src="https://github.com/user-attachments/assets/c4934e7a-3c6f-431a-9457-11c6668b6406" />
---

### Step 4: Create Transit Gateway Attachments
* Attached all 4 VPCs to the central Transit Gateway
<img width="532" height="148" alt="Screenshot 2026-08-31 002448" src="https://github.com/user-attachments/assets/b74560d8-dd9a-46b8-902d-c170ad3588d7" />
---

### Step 5: Configure Subnet Route Tables
#### Updated Route Tables to direct cross-VPC traffic to the Transit Gateway

<img width="623" height="170" alt="Screenshot 2026-08-31 002823" src="https://github.com/user-attachments/assets/38dacdef-98b6-4d3d-b59d-0b53c170dd19" />

**same step (VPC 1, VPC 2, and VPC 3 Edit routes)**
---

## 🚨 Troubleshooting : First Ping Attempt (Failed)
* Even after updating the Route Tables, the ping command timed out:

<img width="449" height="202" alt="Screenshot 2026-08-31 000426" src="https://github.com/user-attachments/assets/8796659c-bd46-4bc7-bed9-197d1d278d1d" />

* ❌ **Root Cause:** By default, AWS Security Groups only allow SSH traffic (Port 22) and block ICMP (ping) traffic.
---

### Step 6: Update Security Group Rules (Allowing ICMP)

<img width="638" height="263" alt="Screenshot 2026-08-31 000555" src="https://github.com/user-attachments/assets/4dabe431-670b-42c1-b9d7-0cd6fd4e293f" />

#### ✅ Final Verification & Results
* **After allowing ICMP traffic, low-latency connectivity with 0% Packet Loss was successfully established across all VPCs:**

<img width="640" height="332" alt="Screenshot 2026-08-31 000738" src="https://github.com/user-attachments/assets/03c70ba6-c86f-49d3-9907-ad1f90c29709" />

---

### Step 7: SSH Jump Host Configuration (Connecting to vpc_1)
* Configured SSH access from the Management Bastion Instance (168.192.1.229) to Private VPC 1 (10.1.10.114):**
* Saved the private key (key.pem) on the Bastion Host.
  
#### Restricted key file permissions
```
chmod 400 key.pem
```
#### Established an SSH connection to the vpc_1 instance:

<img width="579" height="134" alt="Screenshot 2026-08-31 001740" src="https://github.com/user-attachments/assets/ec4a6fd5-cfea-45df-9c6f-90120256b079" />

---

 #### Result: Internet access failed as expected because the private subnet has no Internet Gateway (IGW) or NAT Gateway attached.
 ```
ping 8.8.8.8
```
<img width="608" height="309" alt="Screenshot 2026-08-31 001803" src="https://github.com/user-attachments/assets/4296e7d8-daab-4d91-9f5a-9870c46a20f9" />

####  Connectivity Test (Success):

