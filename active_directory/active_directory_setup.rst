******************************************************************************
**Install and Configure Active Directory on Windows 2012R2 Domain Controller**
******************************************************************************

.. contents::


**Connectivity Instructions:**
******************************

+------------+--------------------------------------------------------+
| DC IP      |                                           10.x.x.40    |
+------------+--------------------------------------------------------+
| Username   |                                           Administrator|
+------------+--------------------------------------------------------+
| Password   |                                           HPOC Password| 
+------------+--------------------------------------------------------+


Overview
********

In this guide we will setup a Windows Domain Controller, and configure Active Directory Domain Services. We will also create 20 users and a bootcamp users group for them.


**Step 1a — Deploying the Windows 2012R2 Server from scratch**
**************************************************************

Do this step if you are deploying from a Widows 2012R2 ISO

1. Go To VM --> Table
2. Create VM 
3. Name = DC
4. Description = bootcamp.local Domain Controller
5. vCPUS = 2 Cores =1
6. Memory = 4gig
7. Set First CDROM to use W2K12R2 ISO
8. Add Disk on Bootcamp Storage Container 
9. Add 2nd CDROM and set to use virtio drivers 1.0.1 ISO 
10. Add New NIC, and select the bootcamp network
11. IP Address = 10.x.x.40
12. Power on VM, and run through the Windows Installation
13. Use the HPOC password as the Administrator password

**Note:** Remember to selcet all 3 drivers from VirtIO disk when you are at the "Select Install Disk" section.


**OR Do This**


**Step 1b — Deploying the Windows 2012R2 Server from Pre-Deployed HPOC Image**
******************************************************************************

Do this step if you are using the Pre-Deployed Windows 2012R2 VM
(These Images are deployed if you select Windows VMs during HPOC Reservation Process)

1. Go To VM --> Table
2. Select the **Windows 2012 VM** and click **Update**
3. Change Name from "Windows 2012 VM" to "DC"
4. Change Description to "bootcamp.local Domain Controller"
5. Delete the Rx-Automation-Network NIC
6. Add Bootcamp NIC
7. IP Address = 10.x.x.40
8. Power on VM
9. Go through the configuration screens
10. Use the HPOC password as the Administrator password


**Step 2 — Configuring Windows Server Settings**
************************************************

In This step we will configure the Windows OS settings we need before we install the AD Role

1. Log into the DC VM
2. Select **Local Server** from the left ribbon in Server Manager
3. In the Properties Window, select the Ethernet/Network settings
4. Right click on Ethernet, and configure the IPV4 address with the following info
	
+------------+--------------------------------------------------------+
| IP         |                                        10.x.x.40       |
+------------+--------------------------------------------------------+
| Netmask    |                                        255.255.255.128 |
+------------+--------------------------------------------------------+
| Gateway    |                                        10.x.x.1        | 
+------------+--------------------------------------------------------+	
| DNS 1      |                                        10.21.253.10    |
+------------+--------------------------------------------------------+
| DNS 2      |                                        10.21.253.11    |
+------------+--------------------------------------------------------+	
	
5. In the Properties Window, Make Suere Remote Desktop is "Enabled"
6. In the Properties Window, Make Suere Windows Firewall is "Off"
7. In the Properties Window, select Computer Name.

+----------------+----------------------------------------------------+
| Computer Name  |                                    DC              |
+----------------+----------------------------------------------------+	

8. Restart Computer


**Step 3 — Installing Domain Services Role (AD)**
*************************************************

In this step we will be installing Domain Services Role

1. Log into the DC VM
2. Select **Add roles and feature** from the Dashboard in Server Manager
3. Hit **Next** on the initial screen
4. Installation Type: Role-based or Feature Based installation – Hit **Next**
5. Select Default Server (DC) - Hit **Next**
6. Select Active Director Domain Services
7. Add Features - Hit **Next**
8. Select AD DS - Hit **Next**
9. Confirmation - Hit **Install** (Check "Restart the destination server automatically if required")

**Note:** Monitor the install, and select **Close** when you see the installation succeeded 


**Step 4 — Configuring Domain Services Role (AD)**
**************************************************

In this step we will be configuring Active Directory for use by our Workshop

1. Select **AD DS** from the left ribbon in Server Manger
2. Click **more…** from yellow highlighted bar
3. Click **Promote this serverto a domain controller** under action
4. Select "Add a new forest" - hit **Next**

+-------------------+------------------------------------------------+
| Root domain name  |                                bootcamp.local  |
+-------------------+------------------------------------------------+	
	
5. DSRM Password = HPOC Password - hit **Next**
6. DNS Options - hit **Next**
7. NetBIOS Name - hit **Next**
8. Paths - hit **Next**
9. Review Options - hit **Next**
10. PreReq Check - hit **Install**

**Note:** Server will reboot automatically


**Step 5 — Adding Workshop Users & Group**
******************************************

In this step we will run a powershell script that will create the "Bootcamp Users" AD group, and user01-user20 (also adding them to the Bootcamp Users group)

**Download the following script and csv**
add-users.ps1_
add-users.csv_

1. Log into the DC VM
2. create a directory called "scripts" at the root of C:
3. Create a directory called "logs" in "c:\scripts"
4. Copy over the add-users.ps1 and add-users.csv to "C:\scripts"
5. Upadate the password in "c:\scripts\add-user.csv" to match the HPOC password
6. Open Powershell, and run the add-user.ps1
7. Open Active Directory User & Computers, and verify the users and group are there.


**Note:** Now you can head back to the Prism Element Setup, and configure Authentication and Roles.


.. code-block::

Import-module activedirectory

$Users=Import-csv c:\scripts\add-users.csv


$a=1;
$b=1;
$failedUsers = @()
$usersAlreadyExist =@()
$successUsers = @()
$VerbosePreference = "Continue"
$LogFolder = "c:\scripts\logs"
$GroupName = "Bootcamp Users"
$OU = "CN=Users, DC=BOOTCAMP,DC=LOCAL"

NEW-ADGroup -name $GroupName –GroupScope Global

ForEach($User in $Users)
{
 $User.FirstName = $User.FirstName.substring(0,1).toupper()+$User.FirstName.substring(1).tolower()
   $FullName = $User.FirstName
   $Sam = $User.FirstName 
   $dnsroot = '@' + (Get-ADDomain).dnsroot
   $SAM = $sam.tolower()
   $UPN = $SAM + "$dnsroot"
   $email = $Sam + "$dnsroot"
   $password = $user.password
try {
    if (!(get-aduser -Filter {samaccountname -eq "$SAM"})){
        New-ADUser -Name $FullName -AccountPassword (ConvertTo-SecureString $password -AsPlainText -force) -GivenName $User.FirstName  -Path $OU -SamAccountName $SAM -UserPrincipalName $UPN -EmailAddress $Email -Enabled $TRUE
        Add-ADGroupMember -Identity $GroupName -Member $Sam
        Write-Verbose "[PASS] Created $FullName"
        $successUsers += $FullName
    }
  
}
catch {
    Write-Warning "[ERROR]Can't create user [$($FullName)] : $_"
    $failedUsers += $FullName
}
}
if ( !(test-path $LogFolder)) {
    Write-Verbose "Folder [$($LogFolder)] does not exist, creating"
    new-item $LogFolder -type directory -Force 
}

Write-verbose "Writing logs"
$failedUsers |ForEach-Object {"$($b).) $($_)"; $b++} | out-file -FilePath  $LogFolder\FailedUsers.log -Force -Verbose
$successUsers | ForEach-Object {"$($a).) $($_)"; $a++} |out-file -FilePath  $LogFolder\successUsers.log -Force -Verbos



.. _add-users.ps1: ../active_directory/scripts/add-users.ps1
.. _add-users.csv: ../active_directory/scripts/add-users.csv







































