# Lab 07: Migrating Hyper-V VMs to Azure by using Azure Migrate

## Lab scenario

Despite its ambitions to modernize its workloads as part of the migration to Azure, the Adatum Enterprise Architecture team realizes that, due to aggressive timelines, in many cases, it will be necessary to follow the lift-and-shift approach. To simplify this task, the team starts exploring the capabilities of Azure Migrate. Azure Migrate serves as a centralized hub to assess and migrate to Azure on-premises servers, infrastructure, applications, and data.

Azure Migrate provides the following features:

- Unified migration platform: A single portal to start, run, and track your migration to Azure.
- Range of tools: A range of tools for assessment and migration. Tools include Azure Migrate: Server Assessment and Migration and modernization. Azure Migrate integrates with other Azure services and with other tools and independent software vendor (ISV) offerings.
- Assessment and migration: In the Azure Migrate hub, you can assess and migrate:
    - Servers: Assess on-premises servers and migrate them to Azure virtual machines.
    - Databases: Assess on-premises databases and migrate them to Azure SQL Database or to SQL Managed Instance.
    - Web applications: Assess on-premises web applications and migrate them to Azure App Service by using the Azure App Service Migration Assistant.
    - Virtual desktops: Assess your on-premises virtual desktop infrastructure (VDI) and migrate it to Windows Virtual Desktop in Azure.
    - Data: Migrate large amounts of data to Azure quickly and cost-effectively using Azure Data Box products.

While databases, web apps, and virtual desktops are within the scope of the next stage of the migration initiative, the Adatum Enterprise Architecture team wants to start by evaluating the use of Azure Migrate for migrating their on-premises Hyper-V virtual machines to Azure VM.

**Note:** An **[interactive lab simulation](https://mslabs.cloudguides.com/guides/AZ-801%20Lab%20Simulation%20-%20Migrating%20Hyper-V%20VMs%20to%20Azure%20by%20using%20Azure%20Migrate)** is available that allows you to click through this lab at your own pace. You may find slight differences between the interactive simulation and the hosted lab, but the core concepts and ideas being demonstrated are the same. 

## Lab Objectives
  
After completing this lab, you will be able to:

-  Prepare for assessment and migration by using Azure Migrate.
-  Assess Hyper-V for migration by using Azure Migrate.
-  Migrate Hyper-V VMs by using Azure Migrate.

## Estimated Time: 180 minutes

## Architecture diagram

![](../Media/lab7.1.png)

## Exercise 1: Prepare the lab environment

### Task 1: Deploy an Azure VM by using an Azure Resource Manager QuickStart template

1. On **Lab-VM**, start **Microsoft Edge (1)**, right click on **[301-nested-vms-in-virtual-network Azure QuickStart template](https://github.com/az140mp/azure-quickstart-templates/tree/master/demos/nested-vms-in-virtual-network)**, then **Copy link**, then paste it over the browser **(2)** and select **Deploy to Azure (3)**. (You'll find the button **Deploy to Azure** in the `README.md` file after the list of resources created by the template.) This will automatically redirect the browser to the **Hyper-V Host Virtual Machine with nested VMs** page in the Azure portal.

   ![](../Media/azm7-1.png)
   
1. If prompted, in the Azure portal, sign in by using following credentials:
   
   - Username: <inject key="AzureAdUserEmail"></inject>
  
   - Password: <inject key="AzureAdUserPassword"></inject>

     >**Note**: If prompted for MFA, please refer to the steps provided on the Getting Started page.   
  
1. On the **Hyper-V Host Virtual Machine with nested VMs** page in the Azure portal, specify the following settings (Leave others with their default values.) and click on **Review + Create (9)**:

   | Setting | Value | 
   | --- | --- |
   | Subscription | the name of the Azure subscription you are using in this lab **(1)** |
   | Resource group | Select  **AZ801-L0701-RG** |
   | Region | select **<inject key="Region" enableCopy="false"/>** **(2)** |
   | Virtual Network Name | **az801l07a-hv-vnet (3)** |
   | Host Network Interface1Name | **az801l07a-hv-vm-nic1 (4)** |
   | Host Network Interface2Name | **az801l07a-hv-vm-nic2 (5)** |
   | Host Virtual Machine Name | **az801l07a-hv-vm (6)** |
   | Host Admin Username | **Student (7)** |
   | Host Admin Password | **Pa55w.rd1234 (8)** |

   ![](../media/az801lab7img1.png)

   ![](../media/az801lab7img3.png)

1. On the **Review+create** page, click on **Create**.

   ![](../media/azm7-2.png)

    > **Note**: Wait for the deployment to complete. The deployment might take about 10 minutes.

1. Once deployment is successfully in search bar, search for **Virtual machine (1)** and select **Virtual machines (2)**.

   ![](../media/azm7-3.png)

1. On virtual machine blade from the list select **az801l07a-hv-vm**.

   ![](../media/azm7-4.png)

1. On **az801l07a-hv-vm** page, under **Networking** section select **Network settings (1)** and click on **+ create port rule (2)** and from drop down and select **inbound port rule (3)**.

   ![](../media/az801lab7img5.png)

1. On **Add inbound security rule** page for **Services** from drop down select **RDP (1)** and click on **Add (2)**.

   ![](../media/az801lab7img6.png)

1. Once the rule is successfully created, in search bar search for **Public Ip address (1)** and select **Public Ip address (2)**.

   ![](../media/azm7-6.png)

1. On **Public IP addresses** page, select **+ Create**.

1. On basics tab specify the following (Leave others with their default values.) and Select **Review + create (3)**: 
   
   | Setting | Value | 
   | --- | --- |
   | Resource group | Select  **AZ801-L0701-RG (1)** |
   | Name |**az801l07a-hv-vnet-ip (2)** |

   ![](../media/az801lab7img10.png)

1. Then click on **Create**:   

   ![](../media/azm7-8.png)

1. Once the deployment is conpleted, click on **Go to resources**.

   ![](../media/azm7-9.png)

1. From the **Overview (1)** page, select **Associate (2)** and select **Network interface (3)** for Resource type dropdown and select **az801l07a-hv-vm-nic1 (4)** from the Network interface dropdown and select **OK (5)**.
   
    ![](../Media/azm7-10.png)   

> **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
 
- Hit the Validate button for the corresponding task. If you receive a success message, you can proceed to the next task. 
- If not, carefully read the error message and retry the step, following the instructions in the lab guide.
- If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

   <validation step="123bacf9-5c15-4067-8dd3-9f0a4e3be107" />
 
### Task 2: Deploy a nested VM in the Azure VM

1. In the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Virtual machines** and then, on the **Virtual machines** page, select **az801l07a-hv-vm**.

   ![](../Media/azm7-11.png)

1. On the **az801l07a-hv-vm** page, select **Connect (1)** drop down and then select **Connect (2)**.

   ![](../Media/azm7-12.png)

1. Click on **Download RDP file** under Native RDP. 

   ![](../Media/azm7-13.png)

1. Click on **Keep**.

   ![](../Media/azm7-14.png)

1. Click on **Open file** to open the downloaded file.

   ![](../Media/azm7-15.png)

1. Select **Connect**.

   ![](../Media/azm7-16.png)

1. Click on **More choices**.

   ![](../Media/azm7-17.png)

1. Select **Use a different account**.

   ![](../Media/azm7-18.png)

1. When prompted, provide the following credentials, and then select **OK (3)**:

     | Setting | Value | 
     | --- | --- |
     | User Name |**Student (1)** |
     | Password |**Pa55w.rd1234 (2)** |

     ![](../Media/azm7-19.png)

1. Click on **Yes**.

   ![](../Media/azm7-20.png)

1. Within the Remote Desktop session to **az801l07a-hv-vm**, in the **Server Manager** window, select **Local Server (1)**, select the **On (2)** link next to the **IE Enhanced Security Configuration** label.

    ![](../media/az801lab7img11.png)

1. In the **IE Enhanced Security Configuration** dialog box, select both **Off (1)(2)** options, and then select **OK (3)**

   ![](../media/azm7-21.png)

1. From the Remote Desktop session, open **File Explorer (1)** and browse to the **F: (2)** drive. 

   ![](../media/azm7-22.png)

1. Right click on empty space, select **New (1)** and then **Folder (2)**.

   ![](../media/azm7-23.png)

1. Create two folders named **VHDs** and **VMs**.

   ![](../media/azm7-25.png)

1. Within the Remote Desktop session to **az801l07a-hv-vm**, start **Microsoft Edge (1)**, 

   - On the Welcome to Microsoft Edge page, select **Start without your data (2)**
   
     ![](../media/azm7-26.png)   

   - On the help for importing Google browsing data page, select the **Continue and continue** button

     ![](../media/azm7-27.png)  

   - Then, proceed to select **Confirm and start browsing**
   
     ![](../media/azm7-28.png)  

   - Click on **Next**

     ![](../media/azm7-29.png)     
   
   - Click on **Finish**

     ![](../media/azm7-30.png)     
   
1. On the next page, right click on [Windows Server Evaluations](https://www.microsoft.com/en-in/EvalCenter) then select **Copy link** and then paste it over the browser tab.

1. On start your evaluation today page, select **Windows server (1)** and then **Windows Server 2025 (2)** as shown below.

    ![](../media/azm7-31.png)

1. On the Windows Server 2025 page, under **Get started for free** select **Download the VHD**.

    ![](../media/azm7-32.png)

1. On **Evaluate Windows Server 2025** page provide the requested information for registration and click on **Download now (9)**.

    | Setting        | Value      | 
    | -------------- | ---------- |
    | First Name     |**ODL (1)**     |
    | Last Name      |**User (2)**    |
    | Email          |**<inject key="AzureAdUserEmail"></inject>** **(3)** |
    | Company name   | Contoso **(4)** |
    | Country/region | Enter your country **(5)** |
    | Company size   | 1 **(6)**|
    | Job Role       | Server Administrator **(7)** |
    | Phone          | Select your country code and enter phone number **(8)** |

    ![](../media/az801lab7img15.png)

1. Before downloading the VHD file please change the download location to **F:\VHDs** folder. You can change download settings location by following the below steps,

   - Select **(...) (1)** icon at top right of the corner, select **Download (2)**

     ![](../media/azm7-33.png)

   - On **Download** window click on **(...) (1)** icon and select **Downloads settings (2)** 
   
     ![](../media/azm7-34.png)   
   
   - In location field click on **change** to set the location

     ![](../media/azm7-35.png)   

   - Enter **F:\VHDs (1)** press enter and then select **Select folder (2)** 

     ![](../media/azm7-36.png)

1. Make sure the location is set to **F:\VHDs**.

    ![](../media/azm7-37.png)     
   
1. On the **Please select your windows server 2025 download** page, in English United States row, under **VHD download** select **64-bit edition**.

    ![](../media/azm7-38.png)

     >**Note**: Wait for download to complete.

      ![](../media/azm7-40.png)     

1. Within the Remote Desktop session to **az801l07a-hv-vm**, select **Start** menu, search for **Hyper-V Manager (1)** and  select **Hyper-V Manager (2)**. 

    ![](../media/azm7-39.png)

1. In the **Hyper-V Manager** console, select the **az801l07a-hv-vm (1)** node. From the right navigation pane, under **Actions** select **New (2)** and then, in the cascading menu, select **Virtual Machine (3)**. This will start the **New Virtual Machine Wizard**. 

    ![](../media/azm7-41.png)

1. On the **Before You Begin** page of the **New Virtual Machine Wizard**, select **Next >**.

    ![](../media/azm7-42.png)

1. On the **Specify Name and Location** page of the **New Virtual Machine Wizard**, specify the following settings and select **Next > (4)**:

    | Setting | Value | 
    | --- | --- |
    | Name | **az801l07a-vm1 (1)** | 
    | Store the virtual machine in a different location | selected  **(2)**| 
    | Location | **F:\VMs** **(3)**|

    ![](../media/az801lab7img20.png)

1. On the **Specify Generation** page of the **New Virtual Machine Wizard**, ensure that the **Generation 2 (1)** option is selected, and then select **Next > (2)**.

    ![](../media/azm7-44.png)
 
1. On the **Assign Memory** page of the **New Virtual Machine Wizard**, set **Startup memory** to **2048 (1)**, and then select **Next > (2)**.

    ![](../media/azm7-45.png)

1. On the **Configure Networking** page of the **New Virtual Machine Wizard**, in the **Connection** drop-down list, select **NestedSwitch (1)**, and then select **Next > (2)**.

    ![](../media/azm7-46.png)

1. Before proceeding to the next page, navigate t the File exporer    

1. On the **Connect Virtual Hard Disk** page of the **New Virtual Machine Wizard**, select the option **Use an existing virtual hard disk (1)**, click on **Browse (2)**, navigate to **F:\VHDs** folder then select downloaded **VHD** file. After set the location **(3)**, then select **Next > (4)**.

    ![](../media/azm7-49.png)
   
1. On the **Summary** page of the **New Virtual Machine Wizard**, select **Finish**.

    ![](../media/azm7-50.png)

1. In the **Hyper-V Manager** console, select the newly created virtual machine, right click **(1)** and then select **Start (2)**.

    ![](../media/azm7-51.png)

1. In the **Hyper-V Manager** console, verify that the virtual machine is running.

    ![](../media/azm7-52.png)

1. Right click and then select **Connect**. 

    ![](../media/azm7-53.png)

    >**Note:** Please wait for a few minutes for the setup to begin.

1. In the **Virtual Machine Connection** window to **az801l07a-vm1**, on the **Hi there** page, select **Next**. 

    ![](../media/azm7-54.png)

1. In the **Virtual Machine Connection** window to **az801l07a-vm1**, on the **License terms** page, select **Accept**. 

    ![](../media/azm7-55.png)

1. In the **Virtual Machine Connection** window to **az801l07a-vm1**, on the **Customize settings** page, set the password of the built-in Administrator account to **Pa55w.rd**, and then select **Finish**. 

    ![](../media/azm7-56.png)

1. In the **Virtual Machine Connection** window to **az801l07a-vm1**, in the **Action (1)** menu, select **Ctrl + Alt + Delete (2)** and then, when prompted, sign in by using **Pa55w.rd (3)** password.

    ![](../Media/s11upd.png)

    >**Note**: Click on **Accept** on Send diagnostic data to Microsoft page. 

     ![](../media/azm7-57.png)    

1. In the **Virtual Machine Connection** window to **az801l07a-vm1**, select **Start (1)**. In the **Start** menu, select **Windows PowerShell(Admin)**.

    ![](../media/azm7-58.png)

1. Then, in the **Administrator: Windows PowerShell** window, run the following to set the computer name. 

    ```powershell
    Rename-Computer -NewName 'az801l07a-vm1' -Restart
    ```
    >**Note**: If the copy paste does not work, enter the code manually and run it.

## Exercise 2: Prepare for assessment and migration by using Azure Migrate
  
### Task 1: Configure Hyper-V environment

1. Navigate back to Remote Desktop session to **az801l07a-hv-vm** and within the Remote Desktop session to **az801l07a-hv-vm**, in the browser window change download location to **Downloads**.

    ![](../media/azm7-59.png)

     >**Note**: Refer the steps in Exercise 1-> Task 2-> Step 22 to change the location to **Downloads** folder.

1. Right click on [https://aka.ms/migrate/script/hyperv](https://aka.ms/migrate/script/hyperv), select **Copy link**, then paste it over the browser tab and download the Azure Migrate configuration PowerShell script.

    >**Note**: The script performs the following tasks:

    >- Checks that you're running the script on a supported PowerShell version
    >- Verifies that you have administrative privileges on the Hyper-V host
    >- Allows you to create a local user account that the Azure Migrate service uses to communicate with the Hyper-V host (This user account is added to **Remote Management Users**, **Hyper-V Administrators**, and **Performance Monitor Users** groups on the Hyper-V host.) 
    >- Checks that the host is running a supported version of Hyper-V, and the Hyper-V role
    >- Enables the WinRM service, and opens ports 5985 (HTTP) and 5986 (HTTPS) on the host (This is required for metadata collection.) 
    >- Enables PowerShell remoting on the host
    >- Checks that the Hyper-V Integration Services is enabled on all VMs managed by the host
    >- Enables CredSSP on the host if needed

1. Within the Remote Desktop session to **az801l07a-hv-vm**, search for **Windows PowerShell ISE (1)** then right click on **Windows PowerShell ISE** and then **Run as Administrator (2)**.

    ![](../media/azm7-60.png)

1. In the **Administrator: Windows PowerShell ISE** window, on the console pane, run the following commands to copy the script to the **C:\\Labfiles\\Lab07** folder and remove the Zone.Identifier alternate data stream, which, in this case, indicates that the file was downloaded from the Internet:

    ```powershell
    New-Item -ItemType Directory -Path C:\Labfiles\Lab07 -Force
    Copy-Item -Path "$env:USERPROFILE\Downloads\MicrosoftAzureMigrate-Hyper-V.ps1" -Destination 'C:\Labfiles\Lab07'
    Unblock-File -Path C:\Labfiles\Lab07\MicrosoftAzureMigrate-Hyper-V.ps1
    Set-Location -Path C:\Labfiles\Lab07
    ```

     ![](../media/azm7-61.png)

1. In the **Administrator: Windows PowerShell ISE** window, click on **File (1)** then **Open (2)**.

    ![](../media/azm7-62.png)

1. Navigate to **C:\\Labfiles\\Lab07 (1)** folder, select  **MicrosoftAzureMigrate-Hyper-V.ps1 (2)** script and click on **Open (3)**.

    ![](../media/azm7-63.png)

1. Select **Run**. 

    ![](../media/azm7-64.png)

1. When prompted for confirmation, enter **Y** and press Enter, **with the exception of the following prompts**, in which case, enter **N** and press Enter:

   - Do you use SMB share(s) to store the VHDs?
   - Do you want to create non-administrator local user for Azure Migrate and Hyper-V Host communication? 

     ![](../media/az801lab7img26.png)

### Task 2: Create an Azure Migrate project

1. Within the Remote Desktop session to **az801l07a-hv-vm**, in the browser window, go to the Azure portal at `https://portal.azure.com/`, and sign in by using the following credentials.

    - Username: <inject key="AzureAdUserEmail"></inject>
  
    - Password: <inject key="AzureAdUserPassword"></inject>

      >**Note**: If prompted, enter the code in Authenticator app to login
    
1. In the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for **Azure Migrate (1)** and select **Azure Migrate (2)**.

    ![](../media/azm7-65.png)

1. Then, on the **Azure Migrate \| Get Started** page, under **Migration goals (1)** section, select **Servers, databases, and web apps (2)**. select **Create Project (3)**. 
 
    ![](../media/azm7-66.png)
  
1. On the **Create Project** page, specify the following settings (leave others with their default values) and select **Create (4)**:

    | Setting | Value | 
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | Select **AZ801-L0702-RG (1)** |
    | Migrate project | **az801l07a-migrate-project (2)** |
    | Geography | the name of your country or a geographical region **(3)** |

    ![](../media/az801lab7img29.png)

### Task 3: Implement the target Azure environment

1. Within the Remote Desktop session to **az801l07a-hv-vm**, in the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for **Virtual networks (1)** and select **Virtual networks (2)**. 

    ![](../media/azm7-67.png)

1. On the **Virtual networks** page. Select **+ Create** on the command bar

    ![](../media/azm7-68.png)

1. On the **Basics** tab of the **Create virtual network** page, specify the following settings (leave others with their default values) and click on **IP address (5)** tab from top:

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab **(1)** |
    | Resource group | Select **AZ801-L0703-RG (2)** |
    | Name | **az801l07a-migration-vnet (3)** |
    | Region | **<inject key="Region" enableCopy="false"/>** **(4)** |

    ![](../media/azm7-69.png)

1. On the **IP addresses** tab of the **Create virtual network** page,

    - Remove the default IP Address space by clicking on **Delete the address space**

       ![](../Media/unit4-image2.png)   
      
    - After deleting **address space**, select **Add IPV4 Address space** specify the following settings (leave others with their default values).

       ![](../Media/unit4-image3.png)

       |Setting|Value|
       |---|---|
       |Starting Address|**10.7.0.0 (1)**|
       |Address space size|**/16 (65536 Addresses) (2)**|

       - On the **IP addresses** tab of the **Create virtual network** page, select **+ Add a subnet (3)**.

         ![](../media/azm7-70.png)

1. On the **Add a subnet** page, specify the following settings (leave others with their default values) and select **Add (4)**:

    |Setting|Value|
    |---|---|
    |Name|**subnet0 (1)**|
    |Starting Address|**10.7.0.0 (2)**|
    |Size|**/24 (256 Addresses) (3)**|

    ![](../media/az801lab7img33.png)

1. Back on the **IP addresses** tab of the **Create virtual network** page, select **Review + create**.

    ![](../media/azm7-71.png)

1. On the **Review + create** tab of the **Create virtual network** page, select **Create**.

    ![](../media/azm7-72.png)

1. In the Azure portal, browse back to the **Virtual networks** page, and then, select **+ Create** on the command bar.

    ![](../media/azm7-73.png)

1. On the **Basics** tab of the **Create virtual network** page, specify the following settings (leave others with their default values) and select **IP address (5)** tab:

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab **(1)** |
    | Resource group | **AZ801-L0703-RG (2)** |
    | Name | **az801l07a-test-vnet (3)** |
    | Region | **<inject key="Region" enableCopy="false"/>** **(4)**|

    ![](../media/azm7-74.png)

1. On the **IP addresses** tab of the **Create virtual network** page, remove the default IP Address space by clicking on **Delete the address space** and after deleting **address space**.

    ![](../media/az801lab7img37.png)

1. Select **Add IPV4 Address space**

    ![](../media/azm7--75.png)

1. On the **Add IPV4 Address space** page, specify the following settings (leave others with their default values) and select **Add a subnet (3)**:

    |Setting|Value|
    |---|---|
    |Starting Address|**10.7.0.0 (1)**|
    |Address space size|**/16 (65536 Addresses) (2)**|

    ![](../media/azm7-76.png)    

1. On the **Add a subnet** page, specify the following settings (leave others with their default values) and select **Add (4)**:

    |Setting|Value|
    |---|---|
    |Name|**subnet0 (1)**|
    |Starting Address|**10.7.0.0 (2)**|
    |Subnet size|**/24 (256 Addresses) (3)**|

    ![](../media/az801lab7img33.png)

1. Back on the **IP addresses** tab of the **Create virtual network** page, select **Review + create**.

    ![](../media/azm7-77.png)

1. On the **Review + create** tab of the **Create virtual network** page, select **Create**.

1. In the Azure portal, search for **Storage account (1)** and select **Storage accounts (2)**.

    ![](../media/azm7-78.png)

1. Then, on the **Storage accounts** page, select **+ Create** on the command bar.

    ![](../media/azm7-79.png)

1. On the **Basics** tab of the **Create a storage account** page, specify the following settings (leave others with their default values) and then click on **Next (8)**:

    | Setting | Value | 
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab **(1)** |
    | Resource group | **AZ801-L0703-RG (2)** |
    | Storage account name | **str<inject key="DeploymentID" enableCopy="false"/>** **(3)** | 
    | Region | **<inject key="Region" enableCopy="false"/>** **(4)** |
    | Primary Service | Select Azure Blob Storage or Azure Data Lake Storage Gen 2 from the dropdown. (5)|
    | Performance | **Standard (6)** |
    | Redundancy | **Locally redundant storage (LRS) (7)** |

    ![](../media/az801lab7img34upd.png)

1. On the **Basics** tab of the **Create a storage account** page, select the **Data protection (1)** tab. On the **Data protection** tab of the **Create a storage account** page, uncheck the **Enable soft delete for blobs (2)** and **Enable soft delete for containers (3)** checkboxes, and then select **Review + create (4)**.

    ![](../media/az801lab7img35.png)

1.  On the **Review  + create** tab, select **Create**.

  > **Congratulations** on completing the Task! Now, it's time to validate it. Here are the steps:
  > - Hit the Validate button for the corresponding task. If you receive a success message, you have successfully validated the lab. 
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
  > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com.com.
   <validation step="b3d28692-9e1e-40f8-bbad-45ad00705c3c" />

## Exercise 3: Assess Hyper-V for migration by using Azure Migrate
  
### Task 1: Deploy and configure the Azure Migrate appliance

1. Within the Remote Desktop session to **az801l07a-hv-vm**, in the browser window, in the Azure portal, search for and select **Azure Migrate**.

1. On the **Azure Migrate \| Servers, databases, and web apps (1)** page, in the **Azure Migrate: Discovery and Assessment** section, select **Discover (2)** link.

   ![](../media/az801lab7img38.png)

1. On the **Discover** page, ensure that the **Discover using appliance** option is selected and then, in the **Are your servers virtualized?** drop-down list, select **Yes, with Hyper-V (1)**. 

1. On the **Discover** page, in the **Name your appliance** text box, type **az801l07a-vma1 (2)** and select the **Generate key (3)** button.

   ![](../media/azm7-80.png)

1. Wait for the key generation to complete and record its value. You will need it later in this exercise.

   ![](../media/azm7-81.png)

1. On the **Discover** page, in the **Download Azure Migrate appliance** text box, select the **.VHD file (1)** option, select **Download (2)**.

   ![](../media/az801lab7img40.png)

   >**Note**: Wait for the download to complete. This might take about 5 minutes.

1. Once the download completes, click on **Open file**.

   ![](../media/azm7-82.png)

1. Right click on the downloaded file and select **Extract All**.

   ![](../media/azm7-83.png)

1. Set the destination folder locatio to **F:\VMs (1)** and select **Extract (2)**.

   ![](../media/azm7-84.png)

    >**Note**: As Microsoft Edge doesn't prompt by default, you may need to manually copy the .VHD file to the F:\VMs folder.

1. Within the Remote Desktop session to **az801l07a-hv-vm**, switch to the **Hyper-V Manager** console, select the **AZ801L07A-VM1** node, and then select **Import Virtual Machine** under **Actions**. This will start the **Import Virtual Machine** wizard.

   ![](../media/az801lab7img41.png)

1. On the **Before You Begin** page of the **Import Virtual Machine** wizard, select **Next >**.

1. On the **Locate Folder** page of the **Import Virtual Machine** wizard, specify the location of the extracted **Virtual Machines** folder **F:\VMs\AzureMigrateAppliance_v25.24.02.07\Virtual Machines** and select **Next >**.

   ![](../media/az801lab7img42.png)

1. On the **Select Virtual Machine** page of the **Import Virtual Machine** wizard, select **Next >**.

   ![](../media/azm7-85.png)

1. On the **Choose Import Type** page of the **Import Virtual Machine** wizard, select **Register the virtual machine in-place (use the existing unique ID) (1)**, and then select **Next > (2)**.

   ![](../media/azm7-86.png)

1. On the **Configure Processor** page of the **Import Virtual Machine** wizard, set **Number of virtual processors** to **4 (1)**, and then select **Next > (2)**.

   ![](../media/az801lab7img43.png)

   >**Note**: In a lab environment, you can ignore any error messages referring to the change of the number of virtual processors. In production scenarios, you should ensure that the virtual appliance has the sufficient number of compute resources assigned to it.

1. On the **Connect Network** page of the **Import Virtual Machine** wizard, in the **Connection** drop-down list, select **NestedSwitch (1)**, and then select **Next > (2)**.

   ![](../media/azm7-87.png)

1. On the **Summary** page of the **Import Virtual Machine** wizard, select **Finish**.

   ![](../media/azm7-88.png)

    >**Note**: Wait for the import to complete.

1. In the **Hyper-V Manager** console, right click on the newly imported virtual machine **(1)**, select **Rename (2)**.

   ![](../media/azm7-89.png)

1. Then set its name to **az801l07a-vma1**.

1. Right click on **az801l07a-vma1** VM, select **Settings**.

   ![](../media/azm7-91.png)

1. Under **Hardware** select **Memory (1)** and under **Specify the amount of memory that the virtual machine can user** in **RAM** replace the memory size of the virtual machine to **4096 (2)** MB and click on **OK (3)**.

   ![](../media/az801lab7img44.png)

1. In the **Hyper-V Manager** console, select the newly imported virtual machine, and then select **Start**. 

   ![](../media/azm7-92.png)

1. In the **Hyper-V Manager** console, verify that the virtual machine is running **(1)**, and then select **Connect (2)**.

   ![](../media/azm7-93.png)

1. In the **Virtual Machine Connection** window to the virtual appliance, on the **License terms** page, select **Accept**. 

   ![](../media/az801lab7img45.png)

1. In the **Virtual Machine Connection** window to the virtual appliance, on the **Customize settings** page, set the password of the built-in Administrator account to **Pa55w.rd (1)**, and then select **Finish (2)**.

   ![](../media/azm7-94.png)

1. In the **Virtual Machine Connection** window to the virtual appliance, in the **Action (1)** menu, select first icon **Ctrl + Alt + Delete (2)** and then, when prompted, sign in by using the newly set password.

   ![](../Media/s11.png)

   >**Note**: Within the **Virtual Machine Connection** window to the virtual appliance, a browser window displaying **Appliance Configuration Manager** will automatically open.
   >**Note**: Please wait it might take some time to load **Appliance Configuration Manager** page.

1. On the **Appliance Configuration Manager** page, select the **I agree** button and wait for the setup prerequisites to be successfully verified. 

   ![](../media/azm7-95.png)

1. On the **Appliance Configuration Manager** page, in the **Verification of Azure Migrate project key** section, After the first two checks are completed **(1)**, in the **Register Hyper-V appliance by pasting the key here** text box, paste the key you copied into Notepad earlier in this exercise **(2)**, select **Verify (3)** and wait for process to complete. It might take around 5- 10 minutes.

   ![](../media/azm7-97.png)
   
   >**Note**: You may not be able to copy and paste the content within nested VM session so kindly select **Clipboard** at top of page in menu bar and from **Clipboard** list select **Type clipboard text** to paste the content and follow the same to step to copy and paste the content

    ![](../Media/lab7-2.png)
   
1. Once verification is completed, if **New update installed** window prompted, select **Refresh** then again click on **Verify**.

   >**Note**: Wait until verification process completes.

1. Under **Azure user Login and appliance registration status** select **Login**.

   ![](../media/azm7-98.png)

1. Then select **Copy code & login**. This will automatically open a new browser tab prompting you to enter the copied code.

   ![](../media/azm7-99.png)

1. On the **Enter code** pane in the newly opened browser tab, paste the code you copied onto the Clipboard, and then select **Next**. 

   ![](../media/azm7-100.png)

1. When prompted, sign in by providing the credentials of a user account with the Owner role in the subscription you are using in this lab.

    - Username: <inject key="AzureAdUserEmail"></inject>
  
    - Password: <inject key="AzureAdUserPassword"></inject>

   >**Note**: You need to enter the credentials manually.

   >**Note**: If prompted, enter the code in Authenticator app to login   
   
1. When prompted **Are you trying to sign in to Microsoft Azure PowerShell?**, select **Continue**, and then **close** the newly opened browser tab.

1. In the browser window, on the **Appliance Configuration Manager** page, verify that registration was successful.

   ![](../media/azm7-101.png)

1. On the **Appliance Configuration Manager** page, in the **Manage credentials and discovery sources** section, select **Add credentials**.

   ![](../media/azm7-102.png)

1. On the **Add credentials** pane, specify the following settings, and then select **Save (5)**:

   | Setting | Value | 
   | --- | --- |
   | Source type | **Hyper-V Host/Cluster (1)** |    
   | Friendly Name | **az801l07ahvcred (2)** |   
   | User Name | **Student (3)** |
   | Password | **Pa55w.rd1234 (4)** |

   ![](../media/azm7-103.png)

    >**Note**: Copy the content in labguide, then select **Clipboard** at top of page in menu bar and from **Clipboard** list select **Type clipboard text** and then paste the content in required field.

1. Within the browser window, on the **Appliance Configuration Manager** page, in the **Provide Hyper-V host/cluster details** section, select **Add discovery source**. On the **Add discovery source** pane.

   ![](../media/azm7-104.png)

1. On the Add discover source page,

   - Select the **Add single item (1)** option - Ensure that the **Discovery source** drop-down list is set to **Hyper-V Host/Cluster (2)**
   - In the **IP address /FQDN** text box, type **10.0.2.1 (3)**   
   -  In the **Map credentials** drop-down list, select the **az801l07ahvcred (4)** entry
   - Then select **Save (5)**.

     ![](../media/azm7-105.png)
 
      >**Note**: **10.0.2.1** is the IP address of the network interface of the Hyper-V host attached to the internal switch.

1. On the **Appliance Configuration Manager** page, in the **Provide Hyper-V host/cluster details** section in step 3, disable the toggle button for **Disable the slider if you don’t want to perform these features**.

   ![](../media/azm7-106.png)

1. Then select **Start discovery** located at the bottom of the page.

   ![](../media/azm7-107.png)

   >**Note**: Please wait as it might take about 15 minutes per host for metadata of discovered servers to appear in the Azure portal.

### Task 2: Configure, run, and view an assessment

1. From the **Virtual Machine Connection** window to the virtual appliance, switch to the Remote Desktop session to **az801l07a-hv-vm**.

1. In the browser window displaying the Azure portal, browse back to the **Azure Migrate | Servers, databases and web apps (1)** page and select **Refresh (2)**. In the **Azure Migrate: Discovery and assessment** section, select **Assess (3)** and then, in the drop-down menu, select **Azure VM (4)**.

   ![](../media/azm7-108.png)

1. On the **Basics** tab of the **Create assessment** page, next to the **Assessment settings** label, select **Edit**.  

   ![](../media/az801lab7img49.png)

1. On the **Assessment settings** page, specify the following settings (leave others with their default values) and select **Save (11)**:

   | Setting | Value | 
   | --- | --- |
   | Target location | **<inject key="Region" enableCopy="false"/>** **(1)** |
   | Storage type | **Premium managed disks** **(2)** |
   | Savings options  | **None** **(3)** |
   | Sizing criteria | **As on premises (4)** |
   | VM series | **Dsv3_series (5)** |
   | Comfort factor | **1 (6)** |
   | Offer | **Pay-As-You-Go (7)** |
   | Currency | US Dollar ($) **(8)** | 
   | Discount | **0 (9)** |
   | VM uptime | **31** Day(s) per month and **24** Hour(s) per day **(10)** | 

   ![](../media/az801lab7img50.png)

   >**Note**: Considering the limited time inherent to the lab environment, the only viable option in this case is an **As on-premises** assessment. 

1. Back on the **Basics** tab of the **Create assessment** page, select **Next: Select servers to assess >** to display the **Select servers to assess** tab.

   ![](../media/azm7-109.png)

1. On the **Select servers to assess** tab,

   -  Set **Assessment name** to **az801l07a-assessment (1)**
   - Ensure that the **Create new** option of the **Select or create a group** setting is selected
   - Set the group name to **az801l07a-assessment-group (2)**
   - In the list of machines to be added to the group, select **az801l07a-vm1 (3)**
   - Select **Next: Review + create assessment (4)**

     ![](../media/az801lab7img51.png)

1. Then select **Create assessment**. 

   ![](../media/azm7-110.png)

1. Back on the **Azure Migrate \| Servers, databases and web apps (1)** page, select **Refresh (2)**. In the **Azure Migrate: Discovery and Assessment** section, verify that the **Assessments** **Total** line contains the **1 (3)** entry, and select it.

   ![](../media/azm7-111.png)

1. On the **Azure Migrate: Discovery and Assessment \| Assessments** page, select the newly created assessment **az801l07a-assessment**. 

   ![](../media/azm7-112.png)

1. On the **az801l07a-assessment** page, review the information indicating Azure readiness and monthly cost estimate for both compute and storage. 

   ![](../media/azm7-113.png)

   >**Note**: In real-world scenarios, you should consider installing the Dependency agent to provide more insights into server dependencies during the assessment stage.

## Exercise 4: Migrate Hyper-V VMs by using Azure Migrate

### Task 1: Prepare for migration of Hyper-V VMs

1. Within the Remote Desktop session to **az801l07a-hv-vm**, in the browser window displaying the Azure portal, browse back to the **Azure Migrate | Servers, databases and web apps (1)** page. 
1. On the **Azure Migrate | Servers, databases and web apps** page, in the **Migration and modernization (2)** section, select the **Discover (3)** link. 

   ![](../media/azm7-114.png)

1. On the **Discover** page, specify the following settings (leave others with their default values) and select **Create resources (5)**:

   | Setting | Value | 
   | --- | --- |
   | Where do you want to migarate to? |  **Azure VM (1)** |
   | Are your machines virtualized? | **Yes, with Hyper-V (2)** |
   | Target region | **<inject key="Region" enableCopy="false"/>** **(3)** | 
   | Confirm the target region for migration | selected **(4)** | 

   ![](../media/azm7-115.png)

   >**Note**: This step automatically triggers provisioning of an Azure Site Recovery vault.

1. On the **Discover** page, in step **1. Prepare Hyper-V host servers**, select the first **Download** link (not the **Download** button), in order to download the Hyper-V replication provider software installer.

   ![](../media/az801lab7img53.png)

   > **Note:** If you receive a browser notification that says **AzureSiteRecoveryProvider.exe can't be downloaded securely**, display the context-sensitive menu of the **Download** link and then, in the menu, select **Copy link**. Open another tab in the same browser window, paste the link you copied, and then press Enter.

1. Once the download completes, select the **Open file** link in the browser **Downloads** section. This will start the **Azure Site Recovery Provider Setup (Hyper-V server)** wizard.

   ![](../media/azm7-116.png)

1. On the **Microsoft Update** page of the **Azure Site Recovery Provider Setup (Hyper-V server)** wizard, select **Off**, and then select **Next**.

   ![](../media/az801lab7img54.png)

1. On the **Provider installation** page of the **Azure Site Recovery Provider Setup (Hyper-V server)** wizard, select **Install**.

   ![](../media/azm7-117.png)

   >**Note**: Wait until installation completes.
   >**Note**: Please don't exit **Provider installation** page after installation completes you need to page in next task.

1. Switch to the Azure portal and then, on the **Discover machines** page, in step 1 of the procedure for preparing on-premises Hyper-V hosts, select the **Download** button in order to download the vault registration key.

   ![](../media/azm7-118.png)

1. Switch to the **Provider installation** page of the **Azure Site Recovery Provider Setup (Hyper-V server) (1)** wizard and select **Register (2)**. This will start the **Microsoft Azure Site Recovery Registration Wizard**.

   ![](../media/azm7-119.png)

1. On the **Vault Settings** page of the **Microsoft Azure Site Recovery Registration Wizard**, select **Browse**.

   ![](../media/azm7-121.png)

1. Browse to the **Downloads (1)** folder, select the vault credentials file **(2)**, and then select **Open (3)**.

   ![](../media/azm7-122.png)

1. Back on the **Vault Settings** page of the **Microsoft Azure Site Recovery Registration Wizard**, select **Next**.

   ![](../media/azm7-123.png)

1. On the **Proxy Settings** page of the **Microsoft Azure Site Recovery Registration Wizard**, accept the default settings and select **Next**.

   ![](../media/azm7-124.png)

    >**Note**: Registration process may take 5 minutes kindly wait to complete.

1. On the **Registration** page of the **Microsoft Azure Site Recovery Registration Wizard**, select **Finish**.

   ![](../media/azm7-125.png)

1. Refresh the browser window displaying the **Discover** page.

1. In the portal search and select **Azure migrate** and under **Migration goals** section select **Azure Migrate | Servers, databases and web apps**.

1. On the **Azure Migrate | Servers, databases and web apps (1)** page, in the **Migration and modernization** section, select the **Discover (2)**. 

   ![](../media/azm7-126.png)

1. On the **Discover** page, 

   - Where do you want to migarate to?:  select **Azure VM (1)** from the drop-down
   - **Are your machines virtualized?** drop-down list: Select **Yes, with Hyper-V (2)** 
   - **Do you want to install a new replication appliance or scale-out existing setup?** drop-down list: Select **Install a replication appliance (3)**
   - Then select **Finalize registration (4)**.

     ![](../media/azm7-128.png)

     >**Note**: It might take up to 5 minutes for the discovery of virtual machines to complete.

### Task 2: Configure replication of Hyper-V VMs

1. Once you receive the confirmation that the registration was finalized, browse back to the **Azure Migrate | Servers, databases and web apps (1)** page and then, in the **Migration and modernization** section, select the **Replicate (2)** link. 

   ![](../media/azm7-129.png)

    >**Note**: You might have to refresh the browser page displaying the **Azure Migrate | Servers, databases and web apps** page.

1. On the **Specify intent** page, in the **Are your machines virtualized?** drop-down list, select **Yes, with Hyper-V** and then select **Continue**.

   ![](../media/az801lab7img55.png)

1. On the **Virtual machines** tab of the **Replicate** page, specify the following settings (leave others with their default values) and select **Next (5)**:

   | Setting | Value | 
   | --- | --- |
   | Import migration settings from an Azure Migrate assessment | **Yes, apply migration settings from an Azure Migrate assessment (1)** |
   | Select group | **az801l07a-assessment-group (2)** |
   | Select assessment | **az801l07a-assessment (3)** |
   | Virtual machines | Select **az801l07a-vm1 (4)** |

   ![](../media/azm7-130.png)

    >**Note**: Even if the Azure VM readiness status does not show as Ready, please proceed with the next steps.

1. On the **Target settings** tab of the **Replicate** page, specify the following settings (leave others with their default values) and select **Next (6)**:

   | Setting | Value | 
   | --- | --- |
   | Subscription | the name of the Azure subscription you are using in this lab **(1)** |
   | Resource group | **AZ801-L0703-RG (2)** |
   | Cache Storage Account | select **str<inject key="DeploymentID" enableCopy="false"/>** **(3)** | 
   | Virtual Network | **az801l07a-migration-vnet (4)** |
   | Subnet | **subnet0 (5)** |

   ![](../media/az801lab7img57.png)

   >**Note**: **If you are unable to see the Cache Storage Account option and select** **str<inject key="DeploymentID" enableCopy="false"/>** **and encounter an error when selecting it please wait for 10 minutes and perform the above from step 1**.

1. On the **Compute** tab of the **Replicate** page, ensure that the **Standard_D2s_v3 (1)** is selected in the **Azure VM Size** drop-down list. In the **OS Type** drop-down list, select **Windows (2)** and then select **Next (3)**.

   ![](../media/azm7-131.png)

1. On the **Disks** tab of the **Replicate** page, accept the default settings and select **Next**.

1. On the **Tags** tab of the **Replicate** page, accept the default settings and select **Next**.

1. On the **Review + Start replication** tab of the **Replicate** page, select **Replicate**.  

   ![](../media/azm7-132.png)

1. To monitor the status of replication, back on the **Azure Migrate | Servers, databases and web apps (1)** page, select **Refresh** and then, in the **Migration and modernization** section, select the **Overview (2)** and on the **Azure Migrate: Migration and modernization** page.

   ![](../media/azm7-136.png)

1. Under **Migration** section select **Replications (1)**. Examine the **Replication Status** column in the list of the replicating machines **(2)**.

   ![](../media/azm7-138.png)

1. Wait until the status changes to **Protected**. This might take additional 15 minutes.

   ![](../media/azm7-134.png)

    >**Note**: You will need to refresh the **Migration and modernization | Replications** to update the **Status** information.

### Task 3: Perform migration of Hyper-V VMs

1. In the Azure portal, on the **Migration and modernization | Replications** page, select the entry representing the **az801l07a-vm1** virtual machine.

   ![](../media/azm7-134.png)

1. On the **az801l07a-vm1** page, select **Test migration**.

   ![](../media/az801lab7img59.png)

1. On the **Test migration** page, in the **Virtual network** drop-down list, select **az801l07a-test-vnet (1)** and then select **Test migration (2)**.

   ![](../media/azm7-135.png)

    >**Note**: Wait for the test migration to complete. This might take about 5 - 10 minutes.

1. In the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Virtual machines** and then, on the **Virtual machines** page, note the entry representing the newly replicated virtual machine **az801l07a-vm1-test**.

   ![](../media/azm7--141.png)

    > **Note:** Initially, the virtual machine will have the name consisting of the **asr-** prefix and randomly generated suffix, but will be renamed eventually to **az801l07a-vm1-test**.

     ![](../media/azm7-139.png)   

1. In the Azure portal, browse back to the **Migration and modernization | Replications (1)** page, select **Refresh (2)**, and then verify that the **az801l07a-vm1** virtual machine is listed with the **Cleanup test failover pending (3)** status.

   ![](../media/azm7-142.png)

1. On the **Migration and modernization | Replicating machines** page, select the entry representing the **az801l07a-vm1** virtual machine.

1. On the **az801l07a-vm1** replicating machines page, select **Clean up test migration**.

   ![](../media/azm7-143.png)

1. On the **Test migrate cleanup** page, select the checkbox **Testing is complete. Delete test virtual machine (1)** and then select **Cleanup Test (2)**.

   ![](../media/azm7-144.png)

1. Once the test failover cleanup job completes, refresh the browser page displaying the **az801l07a-vm1** replicating machines page and note that the **Migrate** icon in the toolbar automatically becomes available.

   ![](../media/azm7-145.png)

1. On the **az801l07a-vm1** replicating machines page, select the **Migrate** link. 

   ![](../media/az801lab7img62.png)

1. On the **Migrate** page, ensure that **Yes (1)** is selected in the **Shutdown virtual machines and perform a planned migration with no data loss?** drop-down list, and then select **Migrate (2)**.

   ![](../media/azm7-146.png)

1. To monitor the status of migration, browse back to the **Azure Migrate | Servers, databases and web apps (1)** page. In the **Migration and modernization** section, select the **Replicating servers (2)** entry.

   ![](../media/azm7-147.png)

1. Then, on the **Migration and modernization | Replicating machines** page, examine the **Status** column in the list of the replicating machines. Verify that the status displays the **Planned failover was initiated** status

   ![](../media/azm7-148.png)

1. Refresh the page, untill you get the status displays the **Planned failover finished** status.

   ![](../media/azm7-149.png)

   >**Note**: Wait for the deployment to complete. This might take about 10 minutes.

   >**Note**: Migration is supposed to be a non-reversible action. If you want to see the completed information, browse back to the **Azure Migrate | Servers, databases and web apps** page, refresh the page, and then verify that the **Migrated Servers** entry in the **Migration and modernization** section has the value of **1**.
   
1. Refresh the page and please wait until the **Replication status** indicates **Completing planned failover**. It might take more time so no need to wait, please proceed with the next step.

    ![](../Media/display.png)
   
1. In the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Virtual machines** and then, on the **Virtual machines** page, note the entry representing the newly replicated virtual machine **az801l07a-vm1**.

   ![](../media/azm7-150.png)

   >**Note**: Migration is supposed to be a non-reversible action. If you want to see the completed information, browse back to the **Azure Migrate | Servers, databases and web apps** page, refresh the page, and then verify that the **Migrated Servers** entry in the **Migration and modernization** section has the value of **1**.

  > **Congratulations** on completing the Task! Now, it's time to validate it. Here are the steps:
  > - Hit the Validate button for the corresponding task. If you receive a success message, you have successfully validated the lab. 
  > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
  > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com.com.
   <validation step="4b1bcbea-9d56-4ff6-ae60-cc67dbff6812" />

## Review

In this lab, you have completed:

+ Prepare the lab environment
+ Prepare for assessment and migration by using Azure Migrate
+ Assess Hyper-V for migration by using Azure Migrate
+ Migrate Hyper-V VMs by using Azure Migrate

## You have successfully completed the lab.

