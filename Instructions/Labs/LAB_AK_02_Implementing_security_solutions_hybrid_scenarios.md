# Lab 02: Implementing Security Solutions in Hybrid Scenarios 

## Lab Overview

In this hands-on lab, you will implement security solutions in a hybrid environment by integrating on-premises and cloud-based security services. You will create and configure an Azure Log Analytics workspace, enable Microsoft Defender for Cloud, provision Azure virtual machines, onboard on-premises servers to Azure Arc, and configure monitoring and update management.

## Lab Objectives

In this lab, you'll be working on:

- Exercise 1: Creating an Azure Log Analytics workspace
- Exercise 2: Configuring Microsoft Defender for Cloud
- Exercise 3: Provisioning Azure VMs running Windows Server
- Exercise 4: Onboarding on-premises Windows Server into Microsoft Defender for Cloud and Azure Update Manager

## Estimated timing: 80 minutes

## Exercise 1: Creating an Azure Log Analytics workspace

In this exercise, you will create an Azure Log Analytics workspace to collect and analyze security and operational data from your hybrid environment.

### Task 1: Create an Azure Log Analytics workspace 

In this task, you will create a Log Analytics workspace in Azure, which will serve as a central location for monitoring and analyzing data collected from your on-premises and cloud resources.

1. Connect to **SEA-SVR2**, and if needed, sign in as **CONTOSO\\Administrator** with the password **Pa55w.rd**.

1. On **SEA-SVR2**, click on Azure Portal shortcut to go to the Azure portal, and sign in by using the credentials of a user account with the Owner role in the subscription you'll be using in this lab.

    ![](../media/e1t1s2.png)

1. On **Sign in to Microsoft Azure** blade, you will see a login screen, in that enter the following email/username and then click on **Next**. 
   * Email/Username: <inject key="AzureAdUserEmail"></inject>

1. Now enter the following password and click on **Sign in**.
   * Password: <inject key="AzureAdUserPassword"></inject>


1. On **SEA-SVR2**, in the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Log Analytics workspaces(1)**, and then, on the **Log Analytics workspaces** page, select **+ Create**.

      ![](../Media/lab2-t1.png)  

      ![](../Media/lab2-t2.png)  

1. On the **Basics** tab of the **Create Log Analytics workspace** tab, enter the following settings, select **Review + Create (5)**, and then select **Create (6)**:

   | Settings | Value |
   | --- | --- |
   | Subscription | Leave the default value (1) |
   | Resource group | select **AZ801-L0201-RG (2)** from the drop-down list |
   | Log Analytics Workspace | any unique name (3) |
   | Region | <inject key="Resource group Region"></inject> (4) |

      ![](../Media/p1.png)  

      ![](../Media/p2.png)  

   >**Note**: Wait for the deployment to complete. The deployment should take about 1 minute.

## Exercise 2: Configuring Microsoft Defender for Cloud

In this exercise, you will enable Microsoft Defender for Cloud to enhance the security posture of your hybrid environment. You will configure Defender plans, enable automatic agent installation, and set up security data collection.

### Task 1: Enable enhanced security of Defender for Cloud

In this task, you will configure enhanced security settings for Microsoft Defender for Cloud, enabling specific Defender plans and monitoring features to protect your environment.

1. On **SEA-SVR2**, in the Azure portal, in the **Search resources, services, and docs** text box, on the toolbar, search for and select **Microsoft Defender for Cloud**.

1. On the **Microsoft Defender for Cloud \| Overview** page, if you see any pop-up regarding **Enhance your security posture by enabling Defender CSPM**, select **No thanks**

   ![](../Media/p3.png)  

1. On **SEA-SVR2**, in the Microsoft Edge window displaying the Azure portal, on the **Microsoft Defender for Cloud | Overview** page, in the **Management (1)** section of the vertical menu on the left, select **Environment settings (2)**.

1. On the **Environment settings** page, select the entry representing your **Azure subscription (3)**.

   ![](../Media/p4.png) 

1. On the **Settings \| Defender plans (1)** page, select **Enable all plans (2)**. In the **Plan selection** page, select **Microsoft Defender for APIs Plan 1 (3)**, and then select **Save (4)**.

   ![](../Media/p5.png) 

1. In the **Settings | Defender plans** page, select **Save**.

   ![](../Media/p6.png) 

   >**Note:**  You may encounter notifications about auto-provisioning update failures. You can safely ignore these notifications as you will only use the Servers plan in this lab. Note that you can selectively disable individual Microsoft Defender plans listed on the same page.

1. Set all of the plans to **Off (2)** except for the **Servers (1)** and select **Save (3)**. Confirm when asked if you are sure you want to downgrade.

    >**Note:**  When presented with the resource types selection for the Databases plan, toggle each entry to Off and then select Continue. For the purpose of this lab, you can safely ignore any notications about individual database resources failing to save.

    ![](../Media/p7.png) 

    ![](../Media/p8.png) 

1. On the **Settings & monitoring** tab, in the list of extensions, set **Vulnerability assessment for machines** to **On (1)**, and select the **Edit configuration (3)** link.

1. Select the **Settings & monitoring** tab, and in the list of extensions, set **Guest Configuration agent (preview)** to **On (2)**.

    ![](../Media/p9.png) 

1. On the **Extension deployment configuration** page, ensure that the **Microsoft Defender vulnerability management (1)** option is selected, and then select **Apply (2)**.

   ![](../Media/p10.png) 

1. On the **Settings & monitoring** page, select **Continue**.  

   ![](../Media/p11.png) 
 
1. On the **Defender plan** page, select **Save** and then close the page.

   ![](../Media/p6.png) 

1. Browse back to the **Microsoft Defender for Cloud | Overview** page, and then, in the **Management** section of the vertical menu on the left, select **Environment settings**.
1. On the **Environment settings** page, expand the entry representing your **Azure subscription** and select the entry representing the **Log Analytics workspace** you created in the previous exercise.

   ![](../Media/p12.png) 

1. On the **Settings \| Defender plans** page, enable the **Servers (1)** Defender plan, and then select **Save (2)**.

   ![](../Media/p13.png) 

   >**Note:**  To enable all Defender for Cloud features including threat protection capabilities, you must enable enhanced security features on the subscription containing the applicable workloads. Enabling it at the workspace level doesn't enable just-in-time VM access, adaptive application controls, and network detections for Azure resources. In addition, the only Microsoft Defender plans available at the workspace level are Microsoft Defender for servers and Microsoft Defender for SQL servers on machines.

1. On the **Settings \| Defender plans** page, in the vertical menu on the left side, in the **Settings** section, select **Data collection**.

1. On the **Settings \| Data collection (1)**, select **All Events (2)**, and then select **Save (3)**.

   ![](../Media/p14.png) 

   >**Note:**  Selecting a data collection tier in Defender for Cloud only affects the storage of security events in your Log Analytics workspace. The Log Analytics agent will still collect and analyze the security events required for Defender for Cloud's threat protection, regardless of the level of security events you choose to store in your workspace. Choosing to store security events enables investigation, search, and auditing of those events in your workspace.

## Exercise 3: Provisioning Azure VMs running Windows Server

In this exercise, you will deploy an Azure virtual machine using an Azure Resource Manager (ARM) template. This virtual machine will be used for monitoring, security analysis, and management.

### Task 1: Deploy an Azure VM by using an Azure Resource Manager template

In this task, you will deploy an Azure virtual machine using an ARM template, specifying necessary parameters such as region, resource group, and credentials.

1. In the **Search resources, services, and docs text box**, on the toolbar, search for and select **Deploy a custom template**.

   ![](../Media/lab2-t3.png) 

1. In the **Custom deployment** page, select **Build your own template in the editor**.

   ![](../Media/lab2-t4.png) 

1. On the **Edit template** page, select **Load file (1)**, upload the template file **L02-rg_template.json (2)**(Navigate to C:\Labfiles\Lab02) and then select **Save (3)**.

   ![](../Media/lab2-t6.png) 

   ![](../Media/lab2-t5.png) 

1. On the **Custom deployment** page, specify the following settings, and leave the other settings with their default values:

   |Setting|Value|
   |---|---|
   |Subscription|Leave the default value|
   |Resource group| Select **AZ801-L0202-RG (1)** from the dropdown list |
   |Region|Leave the default region|
   |Admin Username| Enter **Student (2)** |
   |Admin Password|Enter **Pa55w.rd1234 (3)** |

1. Select **Review + create (4)**, and then select **Create (5)**.

   ![](../Media/lab2-t7.png)

   ![](../Media/lab2-t8.png)

   >**Note**: The deployment might take about 3 minutes.

1. Verify that the deployment completed successfully.

## Exercise 4: Onboarding on-premises Windows Server into Microsoft Defender for Cloud and Azure Update Manager

In this exercise, you will onboard an on-premises Windows Server machine into Azure Arc to enable monitoring, security management, and update management within Azure.

### Task 1: Install Azure Arc agents on an On-Premises Server

In this task, you will install and configure the Azure Arc agent on an on-premises Windows Server, allowing it to be managed within the Azure portal.

1. On **SEA-SVR2**, in the Microsoft Edge window displaying the Azure portal, type **Arc (1)**, then select **Azure Arc (2)**.

   ![](../Media/p15.png) 

1. In the navigation pane under **Azure Arc resources**, select **Machines (1)**.

   ![](../Media/p16.png) 

1. Select **+ Add/Create (2)**, and in the dropdown, select **Add a machine (3)**. 

1. Select **Generate script** from the **Add a single server** section. 

   ![](../Media/p17.png) 
1. In the **Add a server with Azure Arc** page, under **Project details**, select **AZ801-L0201-RG (1)** resource group. 

1. Under **Server details**, select **<inject key="Resource group Region"></inject> (2)** as the region. 

1. Review the SQL Server and Connectivity options. Leave the default values and select **Next (3)**. 

   ![](../Media/p18.png) 

1. In the **Tags** tab, review the default available tags and Select **Next**. 

   ![](../Media/p19.png) 

1. In the **Add a server with Azure Arc** tab, scroll down and select the **Download** button.

   ![](../Media/p20.png) 

   >**Note**: if your browser blocks the download, allow it in the Microsoft Edge browser; select the ellipsis button (…), and then select Keep.

1. Right-click the **Windows Start** button and select **Windows PowerShell (Admin)**

1. Copy and paste the below command into PowerShell and press **Enter**

   ```
    cd C:\Users\Administrator.contoso\Downloads
   ```
 
1. Copy and paste the below command and press **Enter**

   ```
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted 
   ```

1. Enter **A** for **Yes to All** and press **Enter**.

1. Copy and paste the below command and press **Enter**

   ```
   .\OnboardingScript.ps1
   ```

1. Enter **R** to **Run once** and press **Enter** (this may take a couple minutes).

   >The setup process opens a new Microsoft Edge browser tab to authenticate the Azure Arc agent. Select your admin account, wait for the message Authentication complete. Return to Windows PowerShell and wait for the installation to complete before closing the window.

1. Return to the Azure portal page where you downloaded the script and select **Close**.

1. Close the **Add servers with Azure Arc** page and navigate back to the **Azure Arc Machines** page.

1. Select **Refresh** until the **SEA-SVR2** server name appears and the Status is **Connected** in the Arc console. 

   ![](../Media/p21.png) 

### Task 2: Enable Change Tracking and Inventory on the Arc machine

In this task, you will enable change tracking and inventory monitoring to ensure that modifications to the system are logged and can be reviewed.

1. In the navigation pane under **Azure Arc- Machines- SEA-SVR2**, under **Operations (1)** select **Inventory (2)**.   

   ![](../Media/p22.png) 

1. On the **Change Tracking and Inventory** page, click **Enable (4)**.

   >Notice that the Log Analytics workspace (3) you created is listed under **Enable change tracking and inventory feature with AMA**.

1. Wait for the deployment of the Change Tracking feature to complete. This may take up to 5 minutes so proceed to the next steps.

### Task 3: Enable Monitoring using Insights

In this task, you will configure Azure Monitor Insights to collect performance and dependency data for the onboarded machine.

1. Navigate to the **SEA-SVR2** Azure Arc machine and under **Settings (1)**, select **Extensions (2)**.  

   ![](../Media/p23.png) 

   >**Note:** You can view the addition of the ChangeTracking and Azure Monitor Agent extensions (3).

1. Under **Monitoring (1)** select **Insights (2)**, and select **Enable (3)**.  

   ![](../Media/p24.png) 

1. On the **Monitoring configuration** page under **Data collection rule**, select **Create New**.

   ![](../Media/p25.png) 

1. In the **Create new rule** page, enter **Arc (1)** for the **Data collection rule** name.

   ![](../Media/p26.png) 

1. Under **Processes and dependencies**, select **Enable processes and dependencies (map) (2)**.

1. Leave the name of the Azure subscription(3) you are using in this lab.

1. From the **Log Analytics workspaces (4)** drop-down menu, select the Log analytic workspace that you created earlier.

1. Select **Create(5)**, then **Configure**.

   ![](../Media/p27.png) 

   >**Note**: This deployment may take some time. Continue with other tasks and you can return to this later.

### Task 4: Enable Monitoring with Windows Updates 

In this task, you will configure update management for the onboarded Windows Server to ensure that it receives necessary security patches and updates.

1. On **SEA-SVR2**, Windows Update is disabled by default. Make sure that on the **SEA-SVR2** server Windows Update is NOT disabled. You must enable it before you proceed to the next step.

1. Open the Services console and select the **Windows Update** service.

   ![](../Media/lab2-t9.png) 

   ![](../Media/lab2-t10.png) 

1. Right-click **Windows Update** and select **Properties** from the context menu. Set the startup type to **Automatic** and start the service. 

   ![](../Media/lab2-t11.png) 

   ![](../Media/p28.png) 

1. Close the Services console.

1. In the Azure Portal search bar, type **Azure Update Manager**

1. In the navigation pane, under Resources, select **Machines**. You should see **SEA-SVR2** Azure Arc-enabled server listed on the Machines page. Select the **SEA-SVR2** machine.

1. It will redirect to the **Operations (1)** > **Updates (2)**

   ![](../Media/p29.png) 

1. In the **Recommended updates (3)** tab, under **Periodic assessment**, click **Enable now (4)**.

1. From the drop down menu for the **SEA-SVR2** Arc-enabled server, under **Periodic assessment**, select **Enable (1)**, and then **Save (2)**. 

   ![](../Media/p30.png) 

1. On the **Updates** page, select **Check for updates (1)**. 

1. On the **Trigger assess now** window, select **OK (2)**.

   ![](../Media/p31.png) 

   >**Note:** The missing updates will appear in a few minutes. You can revisit the Azure Arc machine periodically and you should see the updates reflected shortly after.

### Task 5: Verify Azure Policy compliance, change tracking, Inventory, Insights monitoring, and Azure Updates

In this task, you will verify the successful configuration of monitoring, compliance policies, and update management by reviewing insights, inventory data, and applied security policies.

1.	Navigate to the **SEA-SVR2** Azure Arc machine, and in the navigation pane under **Monitoring**, select **Insights (1)**.

1.	Select **Performance data (2)**. You should be able to see the performance data.  

    ![](../Media/p32.png) 

1. Select the **Map (1)** tab and view the dependency map.  

   ![](../Media/p33.png) 

   >**Note**: If you don’t see any data, return to the **Performance** tab and refresh. Then refresh the **Map** section. You should see the data and process dependency information for **SEA-SVR2**.

1. In the navigation pane, under **Operations**, select **Inventory(1) ** to view the inventory data.   

   ![](../Media/p35.png) 

1. Select **Change Tracking (1)** to view the data that was changed.  

   ![](../Media/lab2-t12.png) 

### Review

In this lab, you have completed:

- Created an Azure Log Analytics workspace.
- Configured Defender for Cloud.
- Provisioned Azure VMs running Windows Server.
- Onboarded on-premises Windows Server into Defender for Cloud and Azure update manager.

### You have successfully completed the lab.
