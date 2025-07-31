# Multi-VPC Setup and Peering

This guide explains how I set up a multi-VPC architecture in AWS, including creating two distinct VPCs (Development and Production), configuring their subnets, attaching Internet Gateways, setting up route tables, and establishing a VPC Peering Connection between them.

---
<img width="2964" height="2804" alt="image" src="https://github.com/user-attachments/assets/750182d7-6056-42f0-bc0a-d4487933f5a8" />

---
### 1. Create Two New VPCs

1.  Navigate to the Amazon VPC console.
2.  **Create Development VPC:**
    * **Name:** `Development VPC`
    * **IPv4 CIDR block:** `10.0.0.0/16`
3.  **Create Production VPC:**
    * **Name:** `Production VPC`
    * **IPv4 CIDR block:** `172.16.0.0/16`
4.  Ensure both VPCs are successfully created and visible in your VPC dashboard.

<img width="1895" height="911" alt="Development_ VPC_CIDR" src="https://github.com/user-attachments/assets/1e25fbed-c4dc-45b6-af38-552c72a29fc8" />

---

<img width="1891" height="906" alt="Production_VPC_CIDR" src="https://github.com/user-attachments/assets/19e903db-909e-4f7b-96bf-d1cbd5e8d1fc" />

### 2. Create Subnets within Each VPC

For both the Development VPC (`10.0.0.0/16`) and the Production VPC (`172.16.0.0/16`), create a Public and a Private Subnet.

* **Development VPC Subnets (Example CIDRs):**
    * **Public Subnet:** `10.0.1.0/24` (or similar)
    * **Private Subnet:** `10.0.2.0/24` (or similar)
* **Production VPC Subnets (Example CIDRs):**
    * **Public Subnet:** `172.16.1.0/24` (or similar)
    * **Private Subnet:** `172.16.2.0/24` (or similar)

Allocate an Availability Zone for each subnet.

<img width="1888" height="907" alt="Subnets" src="https://github.com/user-attachments/assets/8c3c01ea-7671-493f-a99b-24deee0e7f27" />

---
### 3. Attach Internet Gateway (IGW) to Each VPC

For both the Development VPC and Production VPC:

1.  Create a new Internet Gateway.
2.  Attach the newly created Internet Gateway to its respective VPC. This is essential for public subnets to access the internet.

<img width="1906" height="357" alt="internet_gateways" src="https://github.com/user-attachments/assets/948c306c-ec89-4f46-8e13-29eddfabdfae" />

---

### 4. Configure Route Tables for Each VPC

For each VPC (Development and Production):

1.  **Create a Public Route Table:**
    * Associate this route table with the public subnet(s) in its VPC.
    * Add a route:
        * **Destination:** `0.0.0.0/0` (all traffic)
        * **Target:** To the attached Internet Gateway. This allows resources in the public subnet to reach the internet.
2.  **Create a Private Route Table:**
    * Associate this route table with the private subnet(s) in its VPC.
    * Ensure this route table has a local route for the VPC's CIDR. Initially, it will not have a route to the internet unless you plan to use a NAT Gateway later.


### 5. Establish VPC Peering Connection

In the VPC console, navigate to **Peering Connections**.

1.  **Create Peering Connection:**
    * **Requester VPC:** Select your `Development VPC` (`10.0.0.0/16`).
    * **Accepter VPC:** Select your `Production VPC` (`172.16.0.0/16`).
    * Ensure both VPCs are in the same AWS account and region for this type of peering.
2.  Once created, you will need to **Accept** the Peering Request from the accepter VPC side.
3.  The peering connection status should change to `Active`.

<img width="1902" height="907" alt="peering_connection" src="https://github.com/user-attachments/assets/f9043536-8fe2-4d56-8ef4-0c0e8122957e" />

---
### 6. Update Route Tables for Peering

1.  **For the Development VPC's Route Table(s)** (both public and private, if you want traffic from private subnets to reach the peered VPC):
    * Add a route:
        * **Destination:** `172.16.0.0/16` (CIDR of the Production VPC)
        * **Target:** Select the VPC Peering Connection you just created.
2.  **For the Production VPC's Route Table(s)** (both public and private):
    * Add a route:
        * **Destination:** `10.0.0.0/16` (CIDR of the Development VPC)
        * **Target:** Select the same VPC Peering Connection.
     
<img width="1895" height="903" alt="dev_public_rtb_peering" src="https://github.com/user-attachments/assets/a576e052-1cb4-4e35-a741-45a3a96750e1" />

---
<img width="1901" height="905" alt="prod_private_rtb_peering" src="https://github.com/user-attachments/assets/69fc90e3-53cc-4b47-826b-be606ab211ed" />
