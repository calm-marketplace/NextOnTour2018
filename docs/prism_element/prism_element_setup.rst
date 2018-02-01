**********************************
**Configuring HPOC Prism Element**
**********************************

.. contents::


**This Setup Guide is designed with these assumptions**
********************************************************

1. AOS 5.5.x (or higher)
2. AHV (for 5.5)
3. Using 1 HPOC


**Connectivity & HPOC Info:**
******************************

+-------------------------------------+------------------------------------+
| IP                                  |          Cluster IP                |
+-------------------------------------+------------------------------------+
| Username                            |          Prism User                |
+-------------------------------------+------------------------------------+
| Password                            |          Prism Password            |
+-------------------------------------+------------------------------------+
| HPOC Subnet Used                    |          Subnet used for CVM/Hosts |
+-------------------------------------+------------------------------------+
| Subnet Mask                         |          255.255.255.128           |
+-------------------------------------+------------------------------------+
| Gateway                             |          HPOC Gateway              |
+-------------------------------------+------------------------------------+
| DNS Servers                         |          10.21.253.10,10.21.253.11 |
+-------------------------------------+------------------------------------+

**Example (From HPOC Automation Email):**

.. code-block:: bash

	Cluster IP: https://10.21.93.37:9440/console/#login

	Position: 1 SVM IP: 10.21.93.29 Hypervisor IP: 10.21.93.25 IPMI IP: 10.21.93.33
	Position: 2 SVM IP: 10.21.93.30 Hypervisor IP: 10.21.93.26 IPMI IP: 10.21.93.34
	Position: 3 SVM IP: 10.21.93.31 Hypervisor IP: 10.21.93.27 IPMI IP: 10.21.93.35
	Position: 4 SVM IP: 10.21.93.32 Hypervisor IP: 10.21.93.28 IPMI IP: 10.21.93.36


	LOGIN CREDENTIALS

	Prism UI Credentials: admin/Password
	CVM Credentials: nutanix/Password
	AHV Host Credentials: root/Password


	CLUSTER CREATION DETAILS

	Number of Initial Nodes: 3
	AOS Version: 5.5
	Hypervisor Version: AHV 20170830.58 (5.5)


	NETWORK INFORMATION

	Subnet Mask: 255.255.255.128
	Gateway: 10.21.9.31
	Nameserver IP: 10.21.253.10


**Overview**
************

In this guide we will configure Prism Element for the HPOC you have checked out. This includes the Storage Containers, User VM Network(s), Image Service, Authentication & Roles.


**Configure Prism Element for Workshop**
*****************************************

Start by logging in and accepting the EULA.


**Step 1 — Configure Storage Containers**
*****************************************

1. Go to Storage --> Table
2. Delete the "default-container-xxxxx"
3. Create a new Storage Container called "training"


**Step 2 — Setup user VM network**
**********************************

1. Go to VM
2. Select "Network Config"
3. Select "User VM Interfaces"
4. Select "Create Network"

+-------------------------------------+------------------------------------+
| Name                                |          training                  |
+-------------------------------------+------------------------------------+
| VLAN ID                             |          0                         |
+-------------------------------------+------------------------------------+
| Enable IP Address Management        |          Checked                   |
+-------------------------------------+------------------------------------+
| Network IP Address / Prefix Length  |          10.x.x.0/25               |
+-------------------------------------+------------------------------------+
| Gateway                             |          10.x.x.1                  |
+-------------------------------------+------------------------------------+
| DNS Servers                         |          10.21.253.10,10.21.253.11 |
+-------------------------------------+------------------------------------+
| Domain Search                       |          nutanixdc.local           |
+-------------------------------------+------------------------------------+
| Domain name                         |          nutanixdc.local           |
+-------------------------------------+------------------------------------+
| TFT Server                          |          empty                     |
+-------------------------------------+------------------------------------+
| Boot File                           |          empty                     |
+-------------------------------------+------------------------------------+
| Create Pool                         |          10.x.x.50-10.x.x.120      |
+-------------------------------------+------------------------------------+
| Override DHCP Server                |          Unchecked                 |
+-------------------------------------+------------------------------------+

5. Click **Save**


**Step 3 — Image Configuration**
*********************************

Verify Image Configurations has what you need for your Workshop

1. Go to Gear --> Image Configuration
2. Depending on what you selected when reserving your HPOC you will see a CentOS7 Image & Windows 2012r2 Image
3. You may also see a VM for each already deployed. You can decide if you want to use them or delete them.
4. If you need upload any other ISOs or Images now is a good time to do so


**Step 4 — Configure Data Services IP**
***************************************

1. Select the Cluster in the upper left-hand corner
2. Add the ISCSI Data Services IP = 10.x.x.38
3. Click **Save**


**Step 5 — UI Settings**
************************

Change UI Session Values

1. Go To Gear --> UI Settings
2. Enable animated background particles = Unchecked
3. Session Timeout for Current User = 30 minutes
4. Default Session Timeout for all Users = 2 hours
5. Session Timeout override = Allow unlimited
6. Click **Save**

.. _Active-Directory: ../active_directory/active_directory_setup.rst
