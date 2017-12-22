*************************************************
**Introduction – Configuring HPOC Prism Element**
*************************************************

.. contents::


**This Setup Guide is designed with these assumtpitons**
********************************************************

AOS 5.5.x (or higher)
AHV
Using 1 HPOC (If using more then one, do the same on both)


**Connectivity Instructions:**
******************************

+------------+--------------------------------------------------------+
| IP         |                                           Cluster IP   |
+------------+--------------------------------------------------------+
| Username   |                                           Cluster User |
+------------+--------------------------------------------------------+
| Password   |                                           Cluster Pass | 
+------------+--------------------------------------------------------+


Overview
************

In this guide we will configure Prism Element for the HPOC you have checked out. This includes the Storage Containers, User VM Network(s), Image Service, Authentication & Roles.


**Configure Prism Element for Workshop**
*****************************************

Start by loging in and accepting the EULA.


**Step 1 — Configure Storage Containers**
*****************************************

1. Go to Storage --> Table
2. Delete the "default-container-xxxxx"
3. Create a new Storage Container called "Bootcamp"


**Step 2 — Setup user VM network**
**********************************

1. Go to VM --> Table
2. Select "Network Config"
3. Select "User VM Interfaces"
4. Select "Create Network"
	
+-------------------------------------+------------------------------------+
| Name                                |          bootcamp                  |
+-------------------------------------+------------------------------------+
| VLAN ID                             |          0                         |
+-------------------------------------+------------------------------------+
| Enable IP Address Management        |          Checked                   | 
+-------------------------------------+------------------------------------+
| Network IP Address / Prefix Length  |          10.x.x.0/23               | 
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

**Step 3 — Image Configurations**
*********************************

Verify Image Configurations has what you need for your Workshop

1. Go to Gear --> Image Configuration
2. Depending on what you selceted when reserving your HPOOC you will see a CentOS7 Image & Windows 2012r2 Image
3. You may also see a VM for each already deployed. You can decide if you want to use them or delete them.
4. If you need upload any other ISOs or Images now is a good time to do so	


**Step 4 — Configure Data Services IP**
***************************************

1. Select the Cluster in the upper left-hand corner
2. ISCSI Data Services IP = 10.x.x.38


**Step 5 — UI Settings**
************************

Change Session Timeout Values

1. Go To Gear --> UI Settings
2. Session Timeout for Current User = 30 minutes
3. Default Session Timeout for all Users = 2 hours
4. Session Timeout override = Allow unlimited


**Step 6 — Setup Authentication and Role Mapping**
*******************************************

**Note:** Setup & Configure a Domain Controller (Active-Directory_ ) for use with authentication

1. Go To Gear --> Authentication
2. Select **New Directory**

+----------------------------+----------------------------------------+
| Dircetory Type             |           Active Directory             |
+----------------------------+----------------------------------------+
| Name                       |           Bootcamp                     |
+----------------------------+----------------------------------------+
| Domain                     |           bootcamp.local               | 
+----------------------------+----------------------------------------+
| Directory URL              |           ldap://10.x.x.40             | 
+----------------------------+----------------------------------------+
| Service Account Name       |           administrator@bootcamp.local |
+----------------------------+----------------------------------------+
| Service Account Password   |           HPOC Password                |
+----------------------------+----------------------------------------+

3. Click on the yellow ! next to Bootcamp
4. Click on the **Click Here** to go to the Role Mapping screen
5. Click **New Mapping**

+----------------------------+----------------------------------------+
| Dircetory                  |           Bootcamp                     |
+----------------------------+----------------------------------------+
| LDAP Type                  |           group                        |
+----------------------------+----------------------------------------+
| Role                       |           Cluster Admin                | 
+----------------------------+----------------------------------------+
| Values                     |           Bootcamp Users               | 
+----------------------------+----------------------------------------+

6. Close the Roale Mapping and Authentication windows
7. Log out of Prism Element
8. Log in as **user01@bootcamp.local**

**Note:** If you are able to log in then you have completed Prism Element and AD setup





	
.. _Active-Directory: ../active_directory/active_directory_setup.rst