# Deploy Grafana on Amazon ECS with Fargate

This guide walks you through the process of deploying a Grafana container on AWS Elastic Container Service (ECS) using the Fargate launch type, accessible via a public IP address.

---
### The Architecture

<img width="2728" height="2324" alt="image" src="https://github.com/user-attachments/assets/7e8fe4c7-1d12-40f3-b08a-72071a1f058e" />


---
### Create an ECS Cluster

<img width="1903" height="555" alt="Step1-NavigatetoECS" src="https://github.com/user-attachments/assets/6a6ea6e8-6e25-4305-a931-246b6353df43" />

---

* **Navigate to ECS:** Go to the AWS Management Console, search for "ECS" and select "Elastic Container Service".
* **Create Cluster:** In the ECS dashboard, click on "Clusters" in the left navigation pane.
* Click "**Create Cluster**".
* **Choose Cluster Template:** Select "**Fargate**" as the cluster template. Click "**Next Step**".
* **Configure Cluster:**
    * **Cluster name:** Give your cluster a meaningful name (e.g., `grafana-cluster`, mine was named csn-demo).
    * Leave other settings as default for now.
* Click "**Create**".

<img width="1920" height="692" alt="ECS-Cluster1" src="https://github.com/user-attachments/assets/89c122b3-a46c-4b51-8a88-92711dfd4d89" />

---

### Create a Security Group for Grafana

*  **Navigate to VPC:** Go to the AWS Management Console, search for "VPC" and select "VPC".
*  **Security Groups:** In the left navigation pane, click "Security Groups".
*  **Create Security Group:** Click "**Create security group**".
*  **Configure Security Group:**
    * **Security group name:** `grafana-sg` (or similar).
    * **Description:** "Security group for Grafana on port 3000".
    * **VPC:** Select the VPC where your ECS cluster will reside (usually your default VPC).
*  **Add Inbound Rule:**
*   * Click "**Add rule**" under "Inbound rules".
    * **Type:** "Custom TCP".
    * **Port range:** `3000`.
    * **Source:** "Anywhere-IPv4" (`0.0.0.0/0`) for public access.
        * **Note:** In a production environment, you'd restrict this to known IPs for security.
*  Click "**Create security group**".

<img width="1919" height="616" alt="Inboundrules" src="https://github.com/user-attachments/assets/30d922a5-d46f-492e-9aee-5e73aee7b43d" />

---

### 3. Create a Task Definition for Grafana

*  **Navigate to ECS:** Go back to the ECS dashboard.
*  **Task Definitions:** In the left navigation pane, click "Task Definitions".
*  **Create new Task Definition:** Click "**Create new Task Definition**".
*  **Choose Launch Type Compatibility:** Select "**Fargate**". Click "**Next step**".
*  **Configure Task and Container Definitions:**
    * **Task Definition Name:** `grafana-task-definition` (or similar).
    * **Task Role:** (Optional) If you don't have one, leave it as `None`.
    * **Network Mode:** `awsvpc`.
    * **Task execution role:** Choose `ecsTaskExecutionRole` (if it exists, otherwise AWS will create one automatically when you create the first Fargate task).
    * **Task memory (GB):** e.g., `0.5 GB` (you can adjust this later if needed).
    * **Task CPU (vCPU):** e.g., `0.25 vCPU` (you can adjust this later if needed).
*  **Add Container:** Click "**Add container**".
    * **Container name:** `grafana`.
    * **Image:** `grafana/grafana`.
    * **Port mappings:**
        * **Host port:** Leave blank (Fargate handles dynamic port mapping).
        * **Container port:** `3000`.
        * **Protocol:** `tcp`.
    * Leave other settings as default for now.
*  Click "**Add**".
*  Click "**Create**".

<img width="1920" height="689" alt="Task-def1" src="https://github.com/user-attachments/assets/d46f49e8-c87a-4533-b43f-c0004e5c7649" />
<img width="1883" height="910" alt="taskdefshowinggrafanaport" src="https://github.com/user-attachments/assets/8ba8775d-f0f1-4a35-b858-d64151fd2454" />

---

### 4. Create an ECS Service

*   **Navigate to ECS:** Go back to the ECS dashboard.
*   **Clusters:** Click "Clusters" and select your `grafana-cluster`.
*   **Services Tab:** Go to the "Services" tab.
*   **Create:** Click "**Create**".
*   **Configure service:**
    * **Launch type:** `Fargate`.
    * **Task Definition:** Select your `grafana-task-definition`.
    * **Service name:** `grafana-service` (or similar).
    * **Number of desired tasks:** `1` (for a single instance).
    * **Minimum healthy percent:** `100`.
    * **Maximum healthy percent:** `200`.
*   Click "**Next step**".
*   **Configure network:**
    * **Cluster VPC:** Select your VPC.
    * **Subnets:** Select at least one public subnet. Ensure this is a public subnet for the PUBLIC-IP access later.
    * **Security groups:** Select your `grafana-sg`.
    * **Public IP:** Ensure "**Enabled**" is selected so your task gets a public IP address.
    * **Load balancing:** Select "**None**" for this task.
*   Click "**Next step**".
*   **Set Auto Scaling (optional) & Next Step:** Click "**Next step**" (you can skip auto-scaling for this task).
*   ***Review and create:** Review your settings and click "**Create Service**".

Once the service is created and the task is running (it might take a few minutes for the task to reach `RUNNING` status), go to your `grafana-cluster`, then the "Services" tab, and take a screenshot of your `grafana-service` showing its status as "RUNNING".

<img width="1920" height="770" alt="ECS-cluster" src="https://github.com/user-attachments/assets/7af7c634-64a1-4379-8c6e-d3133824b7e1" />

---

### Access Grafana in your Browser

*   **Find Public IP:**
    * In the ECS dashboard, go to your `grafana-cluster`, then the "Tasks" tab.
    * Click on your running `grafana` task.
    * In the task details, look for the "Network" section and note down the "Public IP" address.
*   **Open Grafana:**
    * Open your web browser.
    * Go to `http://<PUBLIC-IP>:3000` (replace `<PUBLIC-IP>` with the actual public IP you noted down).
*   **Login to Grafana:**
    * You should see the Grafana login page.
    * **Username:** `admin`
    * **Password:** `admin`
    * You'll likely be prompted to change the password on first login.

<img width="1918" height="981" alt="Grafana-login1" src="https://github.com/user-attachments/assets/89908355-6df3-4522-8534-dbcd3a597ba6" />

---
<img width="1920" height="1004" alt="image4" src="https://github.com/user-attachments/assets/683dc177-8d5c-41e1-b7b2-461280dc06a5" />

---
