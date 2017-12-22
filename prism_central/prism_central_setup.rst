**********************************
**Configuring HPOC Prism Central**
**********************************

.. contents::


**This Setup Guide is designed with these assumtpitons**
********************************************************

1. AOS 5.5.x (or higher)
2. AHV (for 5.5)


**Connectivity Instructions:**
******************************

+--------------------------+------------------------------------------+
| Prism Cetnral IP         |                             Cluster IP   |
+--------------------------+------------------------------------------+
| Username                 |                             Cluster User |
+--------------------------+------------------------------------------+
| Password                 |                             Cluster Pass | 
+--------------------------+------------------------------------------+


**Overview**
************

In this guide we will configure Prism Cetnral for the HPOC you have checked out. This includes SSP and Calm (Apps).


**Configure Prism Central for Workshop**
****************************************

We will start bu installing prism Central


**Step 1 — Install Prism Central**
**********************************

1. Click on **Register or create new** on the Prism Central Windget (On the main Cluster page)
2. Click on **Deploy** in the "I want to deploy a new Prims Central instance" box
3. If 5.5.x shows under available version, click **Download**.
4. If it does not show up, the you will need to click on **upload the Prism Central binary** (The tar & json files should be available on POCFS/HPOC-AFS)
5. Click on **Install** next to 5.5.x
6. Input the following info, and then click **Deploy**:

+--------------------------+------------------------------------------+
| VM Name                  |                             PC           |
+--------------------------+------------------------------------------+
| Container                |                             Bootcamp     |
+--------------------------+------------------------------------------+
| VM Sizing                |                             Small        | 
+--------------------------+------------------------------------------+
| AHV Network              |                             bootcamp     | 
+--------------------------+------------------------------------------+
| IP Address               |                             10.x.x.39    | 
+--------------------------+------------------------------------------+

**Note:** Once the Prism Central deployment task finishes, move on

7. In s seperate browser tab, got to https://10.x.x.39:9440
8. Log in with admin / Nutanix/4u
9. Change password to HPOC Password
10. Continue log in with admin / HPOC Password
11. Accept the EULA


**Step 2 — Register Prism Central**
***********************************

1. Go to the Prism Element browser tab
2. Click on **Register or create new** on the Prism Central Windget (On the main Cluster page)
3. Click on **Connect** in the "I already have a Prism Central insteance deployed" box
4. Click **Next**
5. Enter the following info, and then Click **Connect**:

+--------------------------+------------------------------------------+
| Prism Central IP         |                          10.x.x.39       |
+--------------------------+------------------------------------------+
| Username                 |                          admin           |
+--------------------------+------------------------------------------+
| Password                 |                          HPOC Password   | 
+--------------------------+------------------------------------------+

6. You should now see **OK** int he Prism Central Widget (On the main Cluster page)


**Step 3 — UI Settings**
************************

Change Session Timeout Values

1. Go To Gear --> UI Settings
2. Session Timeout for Current User = 30 minutes
3. Default Session Timeout for all Users = 2 hours
4. Session Timeout override = Allow unlimited


**Step 4 — Setup Authentication and Role Mapping**
*******************************************

1. Go To Gear --> Authentication
2. Select **New Directory**

+----------------------------+----------------------------------------+
| Directory Type             |           Active Directory             |
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
| Directory                  |           Bootcamp                     |
+----------------------------+----------------------------------------+
| LDAP Type                  |           group                        |
+----------------------------+----------------------------------------+
| Role                       |           Cluster Admin                | 
+----------------------------+----------------------------------------+
| Values                     |           Bootcamp Users               | 
+----------------------------+----------------------------------------+

6. Close the Roale Mapping and Authentication windows
7. Log out of Prism Central
8. Log in as **user01@bootcamp.local**










