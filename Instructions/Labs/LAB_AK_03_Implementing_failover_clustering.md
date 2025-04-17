
# Lab 03: Implementing failover clustering

## Lab Scenario

As the business of Contoso, Ltd. grows, it's becoming increasingly important that many of the applications and services on its network are always available. Contoso has many services and applications that must be available to internal and external users who work in different time zones around the world. Many of these applications can't be made highly available by using Network Load Balancing (NLB). Therefore, you should use a different technology to make these applications highly available.

As one of the senior network administrators at Contoso, you're responsible for implementing failover clustering on the servers that are running Windows Server to provide high availability for network services and applications. You're also responsible for planning the failover cluster configuration and deploying applications and services on the failover cluster.


## Lab Objectives

In this lab, you'll be working on:

- Exercise 1: Configuring iSCSI Storage.
- Exercise 2: Configuring a Failover Cluster.
- Exercise 3: Deploying and Configuring a Highly Available File Server.
- Exercise 4: Validating the Deployment of the Highly Available File Server.

## Estimated time: **80 minutes**

## Architecture diagram

![](../Media/lab3.1.png)

## Exercise 1: Configuring iSCSI storage

In this exercise, you will be configuring iSCSI storage to be used in a failover cluster environment. You will install necessary features and roles, configure virtual disks, and initiate connections from two servers.

### Task 1: Install Failover Clustering

In this task, you will be installing the Failover Clustering feature and management tools on two servers (SEA-SVR1 and SEA-SVR2) and installing the iSCSI Target server role on SEA-DC1 using PowerShell commands.

1. Connect to **SEA-SVR2**, and then, if needed, sign in as **Contoso\\Administrator** with a password of **Pa55w.rd**.

1. On **SEA-SVR2**, select **Start**, and right-click on **Windows PowerShell**, and select **Run as administrator**.

1. To install the Failover Clustering server feature including the management tools on **SEA-SVR1** and **SEA-SVR2**, at the Windows PowerShell command prompt, enter the following commands, and after entering each command, press Enter:

   ```powershell
   Install-WindowsFeature -Name Failover-Clustering -IncludeManagementTools
   Install-WindowsFeature -ComputerName 'SEA-SVR1.contoso.com' -Name Failover-Clustering -IncludeManagementTools
   ```

   > **Note**: Wait for the installation process to complete. The installation should take about 1 minute.

1. To install iSCSI Target server role service on **SEA-DC1**, at the Windows PowerShell command prompt, enter the following command and press Enter:

   ```powershell
   Install-WindowsFeature -ComputerName 'SEA-DC1.contoso.com' -Name FS-iSCSITarget-Server -IncludeManagementTools
   ```

   > **Note**: Wait for the installation process to complete. The installation should take about 1 minute.

1. Minimize the Windows Powershell window.

### Task 2: Configure iSCSI virtual disks

In this task, you will be creating iSCSI virtual disks on SEA-DC1. This includes establishing PowerShell Remoting sessions to SEA-DC1 and SEA-SVR1, starting the iSCSI Initiator service on both SEA-SVR1 and SEA-SVR2, and creating the iSCSI Target on SEA-DC1.

>**Important:** The lab uses **SEA-DC1**, which serves as an Active Directory Domain Services (AD DS) domain controller to host shared iSCI storage for a Windows Server-based cluster. This is not meant to represent in any way a recommended configuration but rather is done to simplify the lab configuration and minimize the number of lab virtual machines. In any production environment, domain controllers should not be used to host shared storage for failover clusters. Instead, such storage should be hosted on highly available infrastructure. 

1. On **SEA-SVR2**, select **Start**, and right-click on **Windows PowerShell**, and select **Run as administrator** to start a new session.

1. To establish a PowerShell Remoting session to **SEA-DC1**, in the newly opened **Windows PowerShell** window, enter the following command and press Enter:

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1.contoso.com
   ```

1. To create iSCSI virtual disks on **SEA-DC1**, on **SEA-SVR2**, in the PowerShell Remoting session to **SEA-DC1**, enter the following commands, and after entering each command, press Enter:

   ```powershell
   New-Item -ItemType Directory C:\Storage -Force
   ```
   ```powershell
   New-IscsiVirtualDisk C:\Storage\disk1.VHDX -size 10GB
   ```
   ```powershell
   New-IscsiVirtualDisk C:\Storage\disk2.VHDX -size 10GB
   ```
   ```powershell
   New-IscsiVirtualDisk C:\Storage\disk3.VHDX -size 10GB
   ```
1. Minimize the Windows Powershell window.

1. On **SEA-SVR2**, select **Start**, and right-click on **Windows PowerShell**, and select **Run as administrator** to start a new session.

1. To establish a PowerShell Remoting session to **SEA-SVR1**, in the newly opened **Windows PowerShell** window, enter the following command, and then press Enter:

   ```powershell
   Enter-PSSession -ComputerName SEA-SVR1.contoso.com
   ```

   > **Note:** At this point, you should have three **Windows PowerShell** windows opened. You will use the first one to run commands locally on **SEA-SVR2**, while using the other two to interact with **SEA-DC1** and **SEA-SVR1**. You can easily recognize each of them by identifying the PowerShell prompt (for the second and third one, the prompt will contain **[SEA-DC1.contoso.com]** and **[SEA-SVR1.contoso.com]** prefix, respectively).

1. To start the Microsoft iSCSI Initiator service on **SEA-SVR2**, at the **Windows PowerShell** prompt providing access to the local session, enter the following commands, and after entering each command, press Enter:

   ```powershell
   Start-Service -ServiceName MSiSCSI
   Set-Service -ServiceName MSiSCSI -StartupType Automatic
   ```

1. To start the Microsoft iSCSI Initiator service on **SEA-SVR1**, switch to the **Windows PowerShell** window hosting PowerShell Remoting session to **SEA-SVR1**, enter the following commands, and after entering each command, press Enter:

   ```powershell
   Start-Service -ServiceName MSiSCSI
   Set-Service -ServiceName MSiSCSI -StartupType Automatic
   ```

1. To create the Microsoft iSCSI Target on **SEA-DC1**, switch to the **Windows PowerShell** window hosting PowerShell Remoting session to **SEA-DC1**, enter the following command, and then press Enter:

   ```powershell
   New-IscsiServerTarget -TargetName iSCSI-L03 -InitiatorIds IQN:iqn.1991-05.com.microsoft:sea-svr1.contoso.com,IQN:iqn.1991-05.com.microsoft:sea-svr2.contoso.com
   ```

## Exercise 2: Configuring a failover cluster

In this exercise, you will be configuring a failover cluster, which will include connecting to the iSCSI targets, initializing the disks, and creating the cluster itself.

### Task 1: Connect clients to the iSCSI targets

In this task, you will be connecting to the iSCSI target hosted on SEA-DC1 from both SEA-SVR1 and SEA-SVR2. This will involve adding target mappings and establishing iSCSI connections using PowerShell.

1. To mount the iSCSI disks on **SEA-DC1**, from **SEA-SVR2**, in the **Windows PowerShell** window hosting PowerShell Remoting session to **SEA-DC1**, enter the following commands, and after entering each command, press Enter:

   ```powershell
   Add-IscsiVirtualDiskTargetMapping -TargetName “iSCSI-L03” -DevicePath “C:\Storage\Disk1.VHDX”
   Add-IscsiVirtualDiskTargetMapping -TargetName “iSCSI-L03” -DevicePath “C:\Storage\Disk2.VHDX”
   Add-IscsiVirtualDiskTargetMapping -TargetName “iSCSI-L03” -DevicePath “C:\Storage\Disk3.VHDX”
   ```

1. To connect to the iSCSI Target hosted on **SEA-DC1** from **SEA-SVR2**, switch to the **Windows PowerShell** prompt providing access to the local session, enter the following commands, and after entering each command, press Enter:

   ```powershell
   New-iSCSITargetPortal -TargetPortalAddress SEA-DC1.contoso.com  
   Connect-iSCSITarget -NodeAddress iqn.1991-05.com.microsoft:sea-dc1-iSCSI-L03-target
   Get-iSCSITarget | fl
   ```
   > **Note:** Verify that after you run the last command, the value for the *IsConnected* variable is True.

1. To connect to the iSCSI Target hosted on **SEA-DC1** from **SEA-SVR1**, switch to the Windows PowerShell window hosting PowerShell Remoting session to **SEA-SVR1**, enter the following commands, and after entering each command, press Enter:

   ```powershell
   New-iSCSITargetPortal -TargetPortalAddress SEA-DC1.contoso.com 
   Connect-iSCSITarget -NodeAddress iqn.1991-05.com.microsoft:sea-dc1-iSCSI-L03-target
   Get-iSCSITarget | fl
   ```

   > **Note:** Verify that after you run the last command, the value for the *IsConnected* variable is True.

### Task 2: Initialize the disks

In this task, you will be initializing the newly connected iSCSI disks on SEA-SVR2. You will make sure the disks are online, partitioned, formatted, and assigned drive letters.

1. To list the disks on **SEA-SVR2**, switch to the Windows PowerShell prompt providing access to the local session, enter the following command, and then press Enter:

   ```powershell
   Get-Disk
   ```

   >**Note:** Ensure that the three iSCSI disks are listed with the **Offline** operational status. These should be disks with numbers 2, 3, and 4.

1. To initialize the disks, at the Windows PowerShell prompt providing access to the local session, enter the following commands, and after entering each command, press Enter:

   ```powershell
   Get-Disk | Where OperationalStatus -eq 'Offline' | Initialize-Disk -PartitionStyle MBR
   New-Partition -DiskNumber 2 -Size 5gb -AssignDriveLetter
   New-Partition -DiskNumber 3 -Size 5gb -AssignDriveLetter
   New-Partition -DiskNumber 4 -Size 5gb -AssignDriveLetter
   Format-Volume -DriveLetter E -FileSystem NTFS
   Format-Volume -DriveLetter F -FileSystem NTFS
   Format-Volume -DriveLetter G -FileSystem NTFS
   ```
   > **Note:** Verify the disk numbers match the previous command output before running the commands. Verify that each command completed successfully.

### Task 3: Create a failover cluster

In this task, you will be creating a failover cluster on SEA-SVR2, adding SEA-SVR1 as a node to the cluster, and verifying the successful creation and addition of nodes to the cluster.

1. To create a failover cluster, on **SEA-SVR2**, at the Windows PowerShell prompt providing access to the local session, enter the following command, and then press Enter:

   ```powershell
   New-Cluster -Name SEA-CL03 -Node SEA-SVR2.contoso.com -StaticAddress 172.16.10.125
   ```

   > **Note:** The command should return the name of the newly created cluster (**SEA-CL03**). 

1. To add **SEA-SVR1** as another node to the newly created cluster, on **SEA-SVR2**, at the Windows PowerShell prompt providing access to the local session, enter the following command, and then press Enter:

   ```powershell
   Add-ClusterNode -Cluster SEA-CL03 -Name SEA-SVR1.contoso.com
   ```

   > **Note:** Verify that the command is completed successfully.

## Exercise 3: Deploying and configuring a highly available file server

In this exercise, you will be deploying a highly available file server on the failover cluster. You will configure the file server role, add a shared folder, and adjust failover settings to ensure high availability.

### Task 1: Add the file server application to the failover cluster

In this task, you will be adding the File Server role to the failover cluster (SEA-CL03). You will configure a client access point (FSCluster) and select the cluster disks for the file server.

1. On **SEA-SVR2**, select **Start**, in the **Start** menu, select **Server Manager**, and then, in **Server Manager**, select **Failover Cluster Manager (2)** in the **Tools (1)** menu.

   ![](../Media/lab3z1.png)  

   >**Note:** **Failover Cluster Manager** console will be automatically connected to **SEA-CL03** because **SEA-SVR2** is one of the cluster nodes.

1. Expand the **SEA-CL03.contoso.com** node, select **Roles** and verify that the cluster does not host any roles at this point.

   ![](../Media/lab3z2.png) 

1. Select the **Nodes (1)** node and verify that the **SEA-SVR1** and **SEA-SVR2** nodes are displayed with the **Up (2)** status.

   ![](../Media/lab3z3.png) 

1. Expand the **Storage** node and select **Disks (1)**. Notice that three cluster disks are displayed with the **Online (2)** status.

   ![](../Media/lab3z4.png) 

1. On the **Failover Cluster Manager** page, right-click or access the context menu for **Roles (1)**, and then select **Configure Role (2)**. This will start with **High Availability Wizard**.

   ![](../Media/lab3z5.png) 

1. On the **Before You Begin** page of **High Availability Wizard**, select **Next**.

1. On the **Select Role** page of **High Availability Wizard**, select **File Server (1)**, and then select **Next (2)**.

   ![](../Media/lab3z6.png) 

1. On the **File Server Type** page of **High Availability Wizard**, ensure that the **File Server for general use (1)** option is selected, and then select **Next (2)**.

   ![](../Media/lab3z7.png) 

1. On the **Client Access Point** page of **High Availability Wizard**, in the **Name** box, enter **FSCluster (1)**.

1. In the **Address** box, enter **172.16.10.130 (2)**, and then select **Next (3)**.

    ![](../Media/lab3z8.png) 

1. On the **Select Storage** page of **High Availability Wizard**, select **Cluster Disk 1** and **Cluster Disk 2**, and then select **Next (2)**.

   ![](../Media/lab3z9.png) 

1. On the **Confirmation** page of **High Availability Wizard**, select **Next**.

   ![](../Media/lab3z10.png) 

1. On the **Summary** page of **High Availability Wizard**, select **Finish**.

   ![](../Media/lab3z11.png) 

   > **Note:** In the **Storage** node, with the **Disks (1)** node selected, verify that three cluster disks are online. **Cluster Disk 1** and **Cluster Disk 2** should be assigned to **FSCluster (2)**.

     ![](../Media/lab3z12.png) 

### Task 2: Add a shared folder to a highly available file server

In this task, you will be configuring a shared folder (Docs) on the newly created file server role (FSCluster). You will also define access settings for this shared folder to ensure proper user access.

1. On **SEA-SVR2**, in **Failover Cluster Manager**, select **Roles (1)**, select **FSCluster (2)**, and then in the Actions pane, select **Add File Share (3)**. 

    ![](../Media/lab3z13.png) 

   > **Note:** This will start the **New Share Wizard**.

1. On the **Select Profile** page, ensure that the **SMB Share - Quick (1)** profile is selected, and then select **Next (2)**.

   ![](../Media/lab3z14.png) 

1. On the **Share Location** page, select **Next**.

    ![](../Media/lab3z15.png) 

1. On the **Share Name** page, enter **Docs (1)** for the share name, and then select **Next (2)**.

    ![](../Media/lab3z16.png)  

1. On the **Other Settings** page, select **Next**.

1. On the **Permissions** page, select **Next**.

1. On the **Confirmation** page, select **Create**.

    ![](../Media/lab3z17.png) 

1. On the **View results** page, select **Close**.

   ![](../Media/lab3z18.png) 

### Task 3: Configure the failover and failback settings

In this task, you will be configuring the failover and failback settings for the FSCluster file server, including specifying the preferred owner of the cluster role and enabling automatic failback.

1. **On SEA-SVR2**, in the **Failover Cluster Manager** console, with the **FSCluster (2)** selected in the **Roles (1)** node, in the Actions pane, select **Properties (3)**.

   ![](../Media/lab3z19.png) 

1. Select the **Failover (1)** tab, and then select the **Allow failback (2)** option.

1. Select the **Failback between (3)** option, and then enter the following values:

   - **4** in the first text box
   - **5** in the second text box.

     ![](../Media/lab3z20.png) 

1. Select the **General (5)** tab.

1. In the **Preferred owners** section, ensure that **SEA-SVR1 (1)** is listed as the first entry, and then select **OK (2)**.

   ![](../Media/lab3z21.png) 

## Exercise 4: Validating the deployment of the highly available file server

In this exercise, you will be validating the deployment of the highly available file server. This will include testing failover capabilities and checking the quorum configuration for the file server role.

### Task 1: Validate the highly available file server deployment

In this task, you will be testing the failover and failback capability of the FSCluster file server. You will move the file server role between SEA-SVR2 and SEA-SVR1 to ensure continued access to the shared folder.

1. On **SEA-SVR2**, open File Explorer and browse to the **\\\\FSCluster\\Docs** folder.

   >**Note:** In File Explorer, ensure that you are in **Quick access**. Select the **Quick access** path, replace it with **\\\FSCluster\Docs**, and press **Enter**.

1. Inside the **Docs** folder, right-click or access the context menu in an empty area of the folder, select **New (1)**, and then select **Text Document (2)**.

    ![](../Media/lab3z22.png) 

1. To accept the default name of the document as **New Text Document.txt**, press Enter.

1. On **SEA-SVR2**, switch to the **Failover Cluster Manager** console, right-click or access the context menu for **FSCluster**, select **Move (1)**, select **Select Node (2)**.

   ![](../Media/lab3z23.png) 

1. Select **SEA-SVR1 (1)**, and then select **OK (2)**.

   ![](../Media/lab3z24.png) 

1. On **SEA-SVR2**, switch back to File Explorer and verify that you can still access the content of the **\\\\FSCluster\\Docs** folder.

### Task 2: Validate the failover and quorum configuration for the File Server role

In this task, you will be validating the quorum configuration by testing access to the shared folder when one node is offline. You will also configure the cluster quorum settings to use a disk witness.

1. On **SEA-SVR2**, switch to the **Failover Cluster Manager** console and identify the current owner of the **FSCluster** role.

   ![](../Media/lab3z25.png) 

1. Select **Nodes**, and then right-click or access the context menu of the node (SEA-SVR1) you identified in the previous step.

1. In the context menu, select **More Actions (2)**, and then select **Stop Cluster Service (3)**.

    ![](../Media/lab3z26.png) 

1. Switch to File Explorer and verify that you can still access the content of **\\\\FSCluster\\Docs** folder.

1. Switch to the **Failover Cluster Manager** console, and then, right-click or access the context menu of the node with the **Down** status.

1. In the context menu, select **More Actions (1)**, and then select **Start Cluster Service (2)**.

   ![](../Media/lab3z27.png) 

1. In the **Failover Cluster Manager** console, right-click or access the context menu for the **SEA-CL03.Contoso.com (1)** cluster, select **More Actions (2)**, and then select **Configure Cluster Quorum Settings (3)**. This will start the **Configure Cluster Quorum Wizard**.

    ![](../Media/lab3z28.png) 

1. On the **Before You Begin** page, select **Next**.

1. On the **Select Quorum Configuration Option** page, ensure that the **Use default quorum configuration (1)** option is selected, and then select **Next (2)**.

    ![](../Media/lab3z29.png) 

1. On the **Confirmation** page, note that, by default, **Cluster Disk 3** is selected as the **Disk Witness**, and select **Next**.

   ![](../Media/lab3z30.png) 

1. On the **Summary** page, select **Finish**.

1. In the **Failover Cluster Manager** console, browse to the **Disks (1)** node, select **Cluster Disk 3 (2)** configured as the disk witness, and then, in the Actions pane, select **Take Offline (3)**.

   ![](../Media/lab3z31.png)  

1. When prompted for confirmation, select **Yes**.

1. Switch to File Explorer and verify that you can still access the content of the **\\\\FSCluster\\Docs** folder.

1. Switch to the **Failover Cluster Manager** console, and then, in the list of disks within the **Disks** node, select **Cluster Disk 3 (1)** configured as the disk witness, and then, in the Actions pane, select **Bring Online (2)**.

   ![](../Media/lab3z32.png)
   

## Review

In this lab, you have completed the following:

- Configuration of iSCSI storage
- Configuration of  a failover cluster
- Deployment and configuration of a highly available file server
- Validate the deployment of the highly available file server

## You have successfully completed the lab.

