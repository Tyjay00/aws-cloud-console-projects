# Launching a Windows Server EC2 Instance on AWS

This document outlines the step-by-step process for launching a Windows Server EC2 instance on AWS, configuring its network and security, and establishing a successful Remote Desktop Protocol (RDP) connection.

## Task Overview

The objective was to deploy a **Windows Server 2022 EC2 instance** in a **public subnet**, secure it with a **Security Group allowing RDP access only from a specific public IP**.

## Prerequisites

* An active AWS Account.
* Access to the AWS Management Console.

<img width="2008" height="2684" alt="image" src="https://github.com/user-attachments/assets/d21e342e-53fa-4980-aa07-de77a3a3c6af" />



## Step-by-Step Implementation

### Navigate to the EC2 Dashboard in AWS

**Purpose:** To access the Amazon Elastic Compute Cloud (EC2) service where instances are managed.

**Action:**

*  Log in to the AWS Management Console.
*  In the search bar at the top, type "EC2" and select "EC2" from the services dropdown.
*  This will direct you to the EC2 Dashboard.

<img width="1902" height="911" alt="image" src="https://github.com/user-attachments/assets/7a8b57a8-758c-4d5e-9d35-feb8e7cddbcf" />

---

### Launch a New Instance & Choose an Amazon Machine Image (AMI)

**Purpose:** To initiate the instance creation process and select the operating system for the virtual server.

**Action:**

*  From the EC2 Dashboard, click the "**Launch instance**" button.
*  Enter a server name in the "**Name and Tags section**"
*  On the "Choose an Amazon Machine Image (AMI)" page, select "**Windows then Server 2025 Base in the Amazon Machine Image(AMI) box**".
*  Select the appropriate 64-bit (x86) AMI.

<img width="1893" height="905" alt="image" src="https://github.com/user-attachments/assets/12a7fef2-0186-4187-a056-f22d487a14a6" />

---

### Choose an Instance Type

**Purpose:** To define the hardware specifications (CPU, memory) for the instance.

*  On the "Choose an Instance Type" page, select `t2.micro` or `t3.micro` (these are generally free-tier eligible and sufficient for this task).

<img width="1895" height="352" alt="image" src="https://github.com/user-attachments/assets/63ecc912-17e2-489c-a63f-009ab8317b35" />

---

### Key pair (login) 

**Purpose:** You can use a key pair to securely connect to your instance. Ensure that you have access to the selected key pair before you launch the instance.

**Action:**

* Click "**Create new key pair:**".

<img width="1211" height="306" alt="image" src="https://github.com/user-attachments/assets/bc90d67e-69f6-4121-a195-40243dc42923" />

* Enter a name for the key e.g windows or windows-key
* Leave other settings as default.
* Click "**Download Key Pair**". Save this `.pem` file in a secure and accessible location. You will need it to retrieve the administrator password for RDP.
* Check the acknowledgment box.

<img width="1897" height="911" alt="image" src="https://github.com/user-attachments/assets/8400a796-11b9-4090-a1ea-c78b990d397e" />

---

### Configure Instance Details (Network Settings)

**Purpose:** To ensure the instance is deployed in a public subnet and receives a public IP address.

**Action:**

On the "Configure Instance Details" page:

* **Network (VPC):** Keep the default VPC unless a custom one is required.
* **Subnet:** Select a subnet that is designated as public. Crucially, ensure that "**Auto-assign Public IP**" is set to "**Enable**" (or is already enabled by the subnet's default settings). This ensures the instance receives a public IP and is accessible from the internet.
* **Firewall** Create a new security group or 
* **Allow RDP traffic from** Select My IP in the drop down box on the right.
* Leave other settings as default.
* Click "**Next: Add Storage**".

<img width="1900" height="637" alt="image" src="https://github.com/user-attachments/assets/304fd5d4-7d4f-45dc-b80b-b9036ac610b1" />

---

### Configure Storage

**Purpose:** To configure the storage volume for the instance's operating system.

**Action:**

* On the "Add Storage" page, the default root volume size (e.g., 30 GiB for Windows) is usually sufficient.
  
<img width="1168" height="475" alt="image" src="https://github.com/user-attachments/assets/c6b99250-974d-4f7c-b175-36c16bd9580d" />

---

### Configure Security Group

**Purpose:** To define firewall rules that control inbound and outbound traffic to the instance, specifically allowing RDP only from your public IP.

**Action:**

On the "Configure Security Group" page:

1.  Select "**Create a new security group**".
2.  **Security group name:** Enter a descriptive name, e.g., `windows-server-sg`.
3.  **Description:** Add a brief description, e.g., `Security group for the windows-server RDP access`.
4.  **Inbound rules:**
    * Click "**Add Rule**".
    * For **Type**, select `RDP`. This will automatically set the **Port Range** to `3389`.
    * For **Source**, select "**My IP**" from the dropdown. AWS will automatically detect your current public IP address and populate it (e.g., `XXX.XXX.XXX.XXX/32`). This ensures RDP access is restricted to your machine only.
5.  Click "**Review and Launch**".
   
<img width="1907" height="908" alt="image" src="https://github.com/user-attachments/assets/638c61d2-e3d5-47c5-b107-a1c06d31637e" />

---

### Connect to the Instance via RDP

**Purpose:** To establish a remote desktop connection to the running Windows Server.

**Action:**

*  After launching, click "**View Instances**" to go back to the EC2 Instances page.
*  Wait for your `eg, tyrones-server` instance to show a "**Running**" state and "**2/2 checks passed**" under "Status checks". This may take a few minutes.
*  Select your instance.
*  Click the "**Connect**" button at the top right.
*  On the "Connect to instance" page, select the "**RDP client**" tab.
*  Click "**Get password**".
*  Click "**Browse**" and upload the `.pem` key pair file you downloaded in Step 9.
*  Click "**Decrypt Password**". Note down the displayed Administrator password.
*  Click "**Download remote desktop file**". This will download an `.rdp` file.
*  Open the downloaded `.rdp` file.
*  When prompted, enter the Administrator password you just decrypted.
*  Accept any security warnings (e.g., "The publisher of this remote connection cannot be identified") by clicking "**Connect**" or "**Yes**".
*  You should now be connected to your Windows Server EC2 instance's desktop.

---

<img width="1895" height="912" alt="image" src="https://github.com/user-attachments/assets/9318d8f6-9c27-4a58-b188-bd4e7e9b886b" />

---

<img width="1897" height="907" alt="image" src="https://github.com/user-attachments/assets/3723e1e3-e454-4cef-a7e4-0e8f6da0d095" />

---

<img width="1891" height="913" alt="image" src="https://github.com/user-attachments/assets/00dd905b-ff78-4825-a92d-677d58f9f3b4" />

---

<img width="1918" height="998" alt="image" src="https://github.com/user-attachments/assets/7b6d03e6-4f9f-4bed-98c3-e8b3b987256e" />
 
