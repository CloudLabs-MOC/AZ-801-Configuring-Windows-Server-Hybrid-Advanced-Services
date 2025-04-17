# Lab 08: Monitoring and troubleshooting Windows Server

## Lab Overview

In this hands-on lab, you will learn to monitor and troubleshoot the performance of a Windows Server. You will establish a performance baseline by creating data collector sets and analyzing key system metrics. You will also simulate performance issues to identify the source of problems and use Performance Monitor to capture and analyze performance data. Lastly, you will configure centralized event logs to gather system and application events from remote servers, enabling effective monitoring and troubleshooting across your environment.

## Lab Objectives
In this lab, you will complete the following tasks:
+ Exercise 1: Establishing a performance baseline
+ Exercise 2: Identifying the source of a performance problem
+ Exercise 3: Viewing and configuring centralized event logs
  
## Estimated timing: 75 minutes

## Architecture diagram

![](../Media/lab8.1.png)

## Exercise 1: Establishing a performance baseline

In this exercise, you will create and start a custom data collector set in Performance Monitor to establish a baseline for the server’s performance. You will select relevant performance counters, simulate typical server workloads, and collect data. After data collection, you will analyze the performance metrics to establish baseline values that can be used for future comparisons when troubleshooting performance issues.

> **Note**: After starting the Data Collector Set, there might be a delay of 10 minutes for the results to appear.

### Task 1: Create and start a data collector set

In this task, you will create a custom data collector set in Performance Monitor on SEA-SVR2 to track various performance counters like processor time, memory usage, disk activity, and network traffic, providing data for establishing a performance baseline.

1. Connect to **SEA-SVR2**, and then, if needed, sign in as **CONTOSO\\Administrator** with the password **Pa55w.rd**.

1. On **SEA-SVR2**, in the **Type here to search** text box next to the **Start** button, enter **Performance (1)**, and then, in the **Best match** list, select **Performance Monitor (2)**.

   ![](../Media/az-801-lab8-image1.png)

1. In Performance Monitor, expand the **Data Collector Sets** node in the navigation pane, and then select **User Defined**.

    ![](../Media/az-801-lab8-image2.png)
   
1. Right-click on the **User Defined**, select **New**, and then select **Data Collector Set**. This will launch the **Create new Data Collector Set** wizard.

     ![](../Media/az-801-lab8-image3.png)
   
1. On the **How would you like to create this new data collector set?** page of the **Create new Data Collector Set** wizard, enter **SEA-SVR2 Performance** in the **Name** box. Select the **Create manually (Advanced)** option, and then select **Next**.

   ![](../Media/az-801-lab8-image4.png)

1. On the **What type of data do you want to include?** page, select the **Performance counter** checkbox, and then select **Next**.

     ![](../Media/az-801-lab8-image5.png)
   
1. On the **Which performance counters would you like to log?** page, select **Add**.

   ![](../Media/az-801-lab8-image6.png)

1. In the **Available counters(1)** list, expand **Processor (2)**, select **% Processor Time (3)**, ensure that the **_Total (4)** entry is selected in the **Instances of selected object** section, and then select **Add >> (5)**.

   ![](../Media/az-801-lab8-image7.png)

1. In the **Available counters** list, expand **Memory (1)**, select **Pages/sec (2)**, and then select **Add >> (3)**.

   ![](../Media/az-801-lab8-image8.png)

   ![](../Media/az-801-lab8-image9.png)
   
1. In the **Available counters** list, expand **PhysicalDisk (1)**, select **% Disk Time (2)**, ensure that the **_Total (3)** entry is selected in the **Instances of selected object** section, and then select **Add >> (4)**.

   ![](../Media/az-801-lab8-image10.png)

1. Select **Avg. Disk Queue Length (1)**, ensure that the **_Total (2)** entry is selected in the **Instances of selected object** section, and then select **Add >> (3)**.

   ![](../Media/az-801-lab8-image11.png)
  
1. In the **Available counters (1)** list, expand **System**, select **Processor Queue Length (2)**, and then select **Add >> (3)**.

   ![](../Media/az-801-lab8-image12.png)

1. In the **Available counters (1)** list, expand **Network Interface (2)**, select **Bytes Total/sec (3)**, ensure that the **All instances (4)** entry is selected in the **Instances of selected object (5)** section, select **Add >> (6)**, and then select **OK (7)**.

   ![](../Media/az-801-lab8-image13.png)

1. On the **Which performance counters would you like to log?** page, enter **1 (1)** in the **Sample interval** box, and then select **Next (2)**.

   ![](../Media/az-801-lab8-image14.png)

1. On the **Where would you like the data to be saved?** page, select **Next**.

   ![](../Media/az-801-lab8-image15.png)

1. On the **Create the data collector set?** page, ensure that the **Save and close** option is selected, and then select **Finish**.

   ![](../Media/az-801-lab8-image16.png)

1. In Performance Monitor, in the results pane, right-click on the **SEA-SVR2 Performance (1)**, and then select **Start (2)**.

   ![](../Media/az-801-lab8-image17.png)

### Task 2: Create a typical workload on the server

In this task, you will simulate a typical server workload by creating and copying large files using PowerShell. This will allow the Performance Monitor to capture relevant data while the server is under load.

1. On **SEA-SVR2**, in the **Type here to search** text box next to the **Start** button, search for **Windows PowerShell**, right-click on the **Windows PowerShell** and then select **Run as administrator**.

1. To create a file of a specific size, at the Windows PowerShell command prompt, enter the following command and press Enter:

   ```powershell
   fsutil file createnew bigfile 104857600
   ```

1. To copy the newly created file to the root of drive **C** of **SEA-DC1**, enter the following command and press Enter:

   ```powershell
   Copy-Item -Path .\bigfile -Destination \\SEA-DC1.contoso.com\c$\ -Force
   ```

1. To copy the file from the root of drive **C** of **SEA-DC1** to the current working directory, enter the following command and press Enter:

   ```powershell
   Copy-Item -Path \\SEA-DC1.contoso.com\c$\bigfile -Destination .\bigfile2 -Force
   ```

1. To delete both files in the current working directory, enter the following command and press Enter:

   ```powershell
   Remove-Item -Path .\bigfile* -Force
   ```

1. To delete the file in the root of drive **C** of **SEA-DC1**, enter the following command and press Enter:

   ```powershell
   Remove-Item -Path \\SEA-DC1.contoso.com\c$\bigfile -Force
   ```

1. Leave the Windows PowerShell window open.

### Task 3: Analyze the collected data

In this task, you will analyze the performance data reports in Performance Monitor to identify key system metrics like memory usage, disk time, processor usage, and network activity.

1. On **SEA-SVR2**, switch to Performance Monitor.

1. In the navigation pane, right-click or access the context menu for **SEA-SVR2 Performance (1)**, and then select **Stop (2)**.

   ![](../Media/az-801-lab8-image18.png)

1. In Performance Monitor, in the navigation pane, expand **Reports (1)**, expand **User Defined (2)**, expand **SEA-SVR2 Performance (3)**, select **SEA-SVR2\_*DateTime*-000001 (4)**.

   ![](../Media/az-801-lab8-image19.png) 

   >**Note:** (where *DateTime* designates the current timestamp), and then review the report data.
   
1. On the menu bar, select **Change graph type** or press Ctrl+G, and then select **Report**.

   ![](../Media/az-801-lab8-image20.png)

1. Record the values that are listed in the report for later analysis. Recorded values include:

   - **Memory\\Pages/sec**
   - **Network Interface\\Bytes Total/sec**
   - **PhysicalDisk\\% Disk Time**
   - **PhysicalDisk\\Avg. Disk Queue Length**
   - **Processor\\% Processor Time**
   - **System\\Processor Queue Length**

      ![](../Media/az-801-lab8-image21.png)

## Exercise 2: Identifying the source of a performance problem

In this exercise, you will identify the source of a performance problem by creating additional workloads on the server. You will use the CPU Stress utility to simulate a high CPU load and capture performance data during this time. By comparing the performance data before and during the stress, you will diagnose potential performance issues and identify resource bottlenecks.

### Task 1: Create additional workload on the server

In this task, you will run the CPU Stress utility (CPUStres64.exe) to simulate additional CPU load on SEA-SVR2. This increased workload will help identify performance problems that could arise under stress conditions.

1. On **SEA-SVR2**, open File Explorer.

1. In File Explorer, browse to **C:\Labfiles\Lab08**.

1. Double-click on **CPUSTRES64.EXE**.

   ![](../Media/az-801-lab8-image22.png)

   >**Note**: **CPUSTRES64.EXE** is a SysInternals utility that can be used to simulate CPU activity by running up to 64 threads in a loop.

1. In the **CPUSTRES License Agreement** dialog box, select **Agree**.

    ![](../Media/az-801-lab8-image23.png)
   
1. In the **CPU Stress** dialog box, right-click on the highlighted thread at the top of the list of running threads, select **Activity Level**, and then select **Busy (75%)**.

   ![](../Media/az-801-lab8-image24.png)

### Task 2: Capture performance data by using a data collector set

In this task, you will capture performance data while the server is under the added CPU stress, providing insights into how the server is performing under load and offering comparisons with baseline performance metrics.

1. On **SEA-SVR2**, switch to Performance Monitor.

1. In Performance Monitor, expand **Data Collector Sets**, and select **User Defined**.

1. In the results pane, right-click or access the context menu for **SEA-SVR2 Performance**, and then select **Start**.

   ![](../Media/az-801-lab8-image25.png)
   
   > **Note**: Wait 1 minute to allow the data capture to occur.

### Task 3: Remove the workload and review the performance data

In this task, after removing the CPU stress workload, you will stop the data collector set and review the performance data to determine the impact of the additional workload on system performance.

1. On **SEA-SVR2**, switch to **CPU Stress** dialog box and close it. 

1. Switch to Performance Monitor.

1. In the navigation pane, right-click on the **SEA-SVR2 Performance**, and then select **Stop**.

   ![](../Media/az-801-lab8-image26.png)
   
1. In Performance Monitor, in the navigation pane, expand **Reports (1)**, expand **User Defined (2)**, expand **SEA-SVR2 Performance (3)**, select **SEA-SVR2\_*DateTime*-000002 (4)** (where *DateTime* designates the current timestamp), and then review the report data.

1. On the menu bar, select **Change graph type (5)** or press Ctrl+G, and then select **Report (6)**.

   ![](../Media/az-801-lab8-image27.png)

1. As before, record the values that are listed in the report. Recorded values include:

   - **Memory\\Pages/sec**
   - **Network Interface\\Bytes Total/sec**
   - **PhysicalDisk\\% Disk Time**
   - **PhysicalDisk\\Avg. Disk Queue Length**
   - **Processor\\% Processor Time**
   - **System\\Processor Queue Length**

      ![](../Media/az-801-lab8-image28.png)
     
## Exercise 3: Viewing and configuring centralized event logs

In this exercise, you will configure centralized event log management by setting up event forwarding from one server to another. You will enable the necessary prerequisites, such as time synchronization and remote management, and create an event subscription to collect critical events from the source server. You will then verify that the event forwarding is set up correctly and that the events are being received on the target server.

### Task 1: Configure subscription prerequisites

In this task, you will be configuring the prerequisites for event log forwarding, including setting up Windows Remote Management (WinRM), ensuring time synchronization, and enabling the necessary firewall rules on SEA-SVR2 and SEA-DC1.

1. On **SEA-SVR2**, switch to Windows PowerShell.

1. To enable creating and managing subscriptions of events forwarded to **SEA-SVR2**, enter the following command and press Enter:

   ```powershell
   WECUtil qc /q
   ```

1. To ensure that the event source and collector have their local date and time synchronized, enter the following command and press Enter:

   ```powershell
   w32tm /resync /computer:SEA-DC1.contoso.com
   ```

1. To allow WinRM connectivity in case of Kerberos authentication issues, enter the following command and press Enter:

   ```powershell
   Set-Item WSMan:localhost\client\trustedhosts -Value *.contoso.com -Force
   ```

1. To establish a PowerShell Remoting session to **SEA-DC1**, enter the following command and press Enter:

   ```powershell
   Enter-PSSession -ComputerName SEA-DC1.contoso.com
   ```

1. To ensure that Windows Remote Management (WinRM) is enabled on **SEA-DC1**, enter the following command and press Enter:

   ```powershell
   winrm qc
   ```

   > **Note**: Verify that the WinRM service is already running and that it's set up for remote management.

   ![](../Media/az-801-lab8-image29.png)

1. To ensure that the relevant Windows Defender Firewall with Advanced Security rules are enabled on **SEA-DC1**, enter the following command and press Enter:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Event Log Management' -Enabled True -Profile Domain -PassThru
   ```

   > **Note**: Leave the Windows PowerShell window open.

1. On **SEA-SVR2**, in the **Type here to search** text box next to the **Start** button, enter **Active**, and then, in the **Best match** list, select **Active Directory Users and Computers**.

   ![](../Media/az-801-lab8-image30.png)

1. In the **Active Directory Users and Computers** console, select the **Builtin** container under **contoso.com**. 

1. In the **Builtin (1)** container, select the **Event Log Readers (2)** group.

    ![](../Media/az-801-lab8-image31.png)

1. Right-click on the **Event Log Readers** group, select **Properties (3)**, and then, in the **Event Log Readers Properties** dialog box, select the **Members** tab.

    ![](../Media/az-801-lab8-image32.png)

1. On the **Members** tab, select **Add**.

    ![](../Media/az-801-lab8-image33.png)

1. In the **Select Users, Contacts, Computers, Service Accounts or Groups** dialog box, select **Object Types**.

   ![](../Media/az-801-lab8-image34.png)

1. In the **Object Types** dialog box, select the **Computers** checkbox, and then select **OK**.

   ![](../Media/az-801-lab8-image35.png)

1. In the **Select Users, Computers, Service Accounts or Groups** dialog box, enter **SEA-SVR2** in the **Enter the object names to select** box, and then select **OK**.

    ![](../Media/az-801-lab8-image36.png)

1. In the **Event Log Readers Properties** dialog box, select **OK**.

   ![](../Media/az-801-lab8-image37.png)

### Task 2: Create a subscription and verify the results

In this task, you will create an event subscription to collect and forward critical events from SEA-DC1 to SEA-SVR2. After setting up the subscription, you will verify that the forwarded events appear in Event Viewer, ensuring centralized logging is working correctl

1. On **SEA-SVR2**, in the **Type here to search** text box next to the **Start** button, enter **Event**, and then, in the **Best match** list, select **Event Viewer**.

     ![](../Media/az-801-lab8-image47.png)
   
1. In **Event Viewer**, select **Subscriptions** in the navigation pane.

    ![](../Media/az-801-lab8-image38.png)
   
1. Right-click or access the context menu for **Subscriptions (1)**, and then select **Create Subscription (2)**.

   ![](../Media/az-801-lab8-image39.png)

1. In the **Subscription Properties** dialog box, enter **SEA-DC1 Events** in the **Subscription name** box.

1. Ensure that the **Collector initiated** option is selected, and then select **Select Computers**.

1. In the **Computers** dialog box, select **Add Domain Computers**.

   ![](../Media/az-801-lab8-image40.png)

1. In the **Select Computer** dialog box, enter **SEA-DC1** in the **Enter the object name to select** box, and then select **OK**.

     ![](../Media/az-801-lab8-image41.png)
   
1. In the **Computers** dialog box, select **OK**.

    ![](../Media/az-801-lab8-image42.png)
   
1. In the **Subscription Properties – SEA-DC1 Events** dialog box, select **Select Events**.

   ![](../Media/az-801-lab8-image43.png)

1. In the **Logged** drop-down list, select **Last 24 hours**.

1. In the **Event level** dialog box, select the **Critical**, **Error**, **Warning**, and **Information** checkboxes.

1. In the **Event logs** drop-down list, expand **Windows Logs**, and then select the **System** and **Application** checkboxes.

    ![](../Media/az-801-lab8-image44.png)
   
1. In the **Query Filter** dialog box, select **OK**.

    ![](../Media/az-801-lab8-image45.png)
   
1. Back in the **Subscription Properties – SEA-DC1 Events** dialog box, select **OK**.

1. On **SEA-SVR2**, switch to the **Event Viewer** window, and then expand **Windows Logs** in the navigation pane.

1. Select **Forwarded Events** and verify that the forwarded events include those generated on **SEA-DC1**.

   ![](../Media/az-801-lab8-image46.png)

   >**Note:** You have to wait for sometime, till the results shows.

### Review
In this lab, you have completed:
- Established a performance baseline.
- Identified the source of a performance problem.
- Viewied and configured centralized event logs.

### You have successfully completed the lab
