# Getting Started with Your AZ-801: Configuring Windows Server Hybrid Advanced Services Workshop
 
Welcome to your AZ-801: Configuring Windows Server Hybrid Advanced Services workshop! We've prepared a seamless environment for you to explore and learn about configuring and managing Windows Server on-premises, hybrid, and infrastructure as a service (IaaS) platform workloads. Let's begin by making the most of this experience:

## Overview

In these hands-on labs, you will develop the skills required to configure, secure, protect, migrate, and monitor Windows Server workloads across on-premises, hybrid, and Azure infrastructure as a service (IaaS) environments. Working as a Windows Server hybrid administrator, you will harden Windows Server and Active Directory by implementing Windows Defender Credential Guard and the Local Administrator Password Solution (LAPS), and extend that security posture to the cloud by onboarding on-premises servers to Microsoft Defender for Cloud, Azure Arc, and Azure Update Manager. The labs also cover building resilient, highly available infrastructure using iSCSI storage and failover clustering, protecting workloads with Hyper-V Replica, Windows Server Backup, Azure Site Recovery, and Azure Backup, and migrating domain controllers, file servers, and Hyper-V virtual machines to Azure using ARM templates, Storage Migration Service, and Azure Migrate. Finally, you will monitor and troubleshoot server performance and centralize operational monitoring across on-premises and Azure VMs using Performance Monitor, Event Viewer, and Azure Monitor. By completing these labs, you will gain the practical experience needed to secure, protect, migrate, and operate Windows Server workloads in hybrid environments.

## Objectives

By the end of these labs, you will be able to:

1. **Configure security in Windows Server:** Enable Windows Defender Credential Guard using Group Policy, locate and remediate problematic Active Directory accounts, and implement the Local Administrator Password Solution (LAPS).

2. **Implement security solutions in hybrid scenarios:** Create an Azure Log Analytics workspace, configure Microsoft Defender for Cloud, provision Azure VMs running Windows Server, and onboard on-premises servers into Defender for Cloud and Azure Update Manager.

3. **Implement failover clustering:** Configure iSCSI storage, build and validate a failover cluster, and deploy a highly available file server for continuous service availability.

4. **Implement Hyper-V Replica and Windows Server Backup:** Configure Hyper-V Replica to replicate virtual machines between servers for disaster recovery, and configure Windows Server Backup to protect data on a network share.

5. **Implement Azure-based recovery services:** Create and configure an Azure Site Recovery vault, protect Hyper-V virtual machines using Azure Site Recovery, and implement Azure Backup.

6. **Upgrade and migrate Windows Server:** Deploy AD DS domain controllers in Azure using ARM templates and Azure Bastion, and migrate file servers using Storage Migration Service.

7. **Implement migration in hybrid scenarios:** Prepare, assess, and migrate on-premises Hyper-V virtual machines to Azure by using Azure Migrate.

8. **Monitor and troubleshoot Windows Server:** Establish a performance baseline with Performance Monitor, identify the source of a performance problem, and configure centralized event logs.

9. **Implement operational monitoring in hybrid scenarios:** Prepare a monitoring environment, configure monitoring of on-premises servers and Azure VMs, and evaluate Azure monitoring services.

10. **Apply hybrid Windows Server administration best practices:** Combine on-premises administration with Azure security, recovery, migration, and monitoring services to operate resilient, well-managed hybrid infrastructure.

## Pre-requisites

- Experience administering Windows Server, Active Directory Domain Services (AD DS), and Hyper-V.
- Familiarity with core Azure services such as virtual machines, virtual networks, storage accounts, Recovery Services vaults, and the Azure portal.
- Working knowledge of Windows PowerShell for day-to-day administration and automation tasks.
- Basic understanding of high availability, disaster recovery, backup, and monitoring concepts will help learners get the most from this course.

## Architecture

The lab architecture demonstrates how on-premises Windows Server infrastructure integrates with Azure to deliver secure, highly available, resilient, and well-monitored hybrid workloads. Throughout these labs, you will secure Windows Server and Active Directory accounts, build failover clusters and replicated Hyper-V environments, protect workloads using Azure-based recovery services, migrate servers and data to Azure, and monitor both on-premises and Azure resources.

1. **On-premises Windows Server Infrastructure:** The Contoso domain, hosted on domain controller SEA-DC1 and member servers SEA-SVR1 and SEA-SVR2, hosts the roles, features, and services configured throughout the labs.

2. **Security and Identity Services:** Windows Defender Credential Guard, Group Policy, Active Directory user accounts, and the Local Administrator Password Solution (LAPS) protect credentials and enforce security baselines on Windows Server.

3. **High Availability and Storage Services:** iSCSI storage, Failover Clustering, and a highly available file server role provide continuous availability for network services and applications.

4. **Disaster Recovery and Backup Services:** Hyper-V Replica, Windows Server Backup, Azure Site Recovery, and Azure Backup protect virtual machines and data against outages and disasters.

5. **Azure Migration Services:** ARM templates, Storage Migration Service, and Azure Migrate assess and migrate on-premises domain controllers, file servers, and Hyper-V virtual machines to Azure.

6. **Azure Hybrid and Monitoring Services:** Azure Arc, Microsoft Defender for Cloud, Azure Update Manager, Azure Monitor, and Log Analytics workspaces extend security, update management, and monitoring to on-premises and Azure resources.

7. **Azure Administration Tools:** Azure Portal, Azure CLI, Azure Cloud Shell, Windows PowerShell, and ARM templates are used to provision infrastructure, deploy resources, and manage the hybrid environment throughout the labs.

## Explanation of Components

1. **Windows Defender Credential Guard:** Uses virtualization-based security to isolate and protect credentials from theft, configured through Group Policy and evaluated with hardware readiness tools.

2. **Local Administrator Password Solution:** Automates the management and rotation of local administrator account passwords to reduce the risk of lateral movement attacks.

3. **Azure Log Analytics Workspace:** Collects and analyzes security, performance, and operational data gathered from on-premises and Azure resources.

4. **Microsoft Defender for Cloud:** Provides unified security posture management and threat protection for hybrid workloads, including onboarded on-premises servers.

5. **Azure Arc:** Extends Azure management, security, and monitoring capabilities to on-premises Windows Servers as if they were native Azure resources.

6. **Azure Update Manager:** Centrally manages and applies software updates across on-premises and Azure virtual machines.

7. **Failover Clustering & iSCSI Storage:** Groups multiple servers together, backed by shared iSCSI storage, to provide high availability for applications and services such as file servers.

8. **Hyper-V Replica:** Asynchronously replicates virtual machines between Hyper-V hosts to support disaster recovery and test failover scenarios.

9. **Windows Server Backup:** Provides native, PowerShell-driven backup and restore capabilities for protecting server data on a network share.

10. **Azure Site Recovery:** Orchestrates replication, test failover, and recovery of on-premises Hyper-V virtual machines to Azure for business continuity and disaster recovery.

11. **Azure Backup:** Provides cloud-based backup and retention for on-premises and Azure workloads using Recovery Services vaults.

12. **AD DS Domain Controllers in Azure:** Extends Active Directory Domain Services into Azure using ARM templates and Azure Bastion to support identity in hybrid environments.

13. **Storage Migration Service:** Migrates file servers, along with their data, security, and configuration, to new servers with minimal disruption.

14. **Azure Migrate:** Assesses and migrates on-premises Hyper-V virtual machines to Azure virtual machines with minimal downtime.

15. **Performance Monitor & Event Viewer:** Establish performance baselines, identify the source of performance problems, and centralize event logs for troubleshooting Windows Server.

16. **Azure Monitor:** Collects telemetry and metrics from on-premises and Azure VMs to provide end-to-end operational monitoring across hybrid environments.

17. **Azure Portal, Azure CLI & Windows PowerShell:** Provide graphical and command-line tools for provisioning resources, deploying ARM templates, and managing the hybrid infrastructure throughout the labs.

## Accessing Your Lab Environment
 
Once you're ready to dive in, your virtual machine and lab guide will be available in your web browser.
 
  ![Access Your VM and Lab Guide](../Media/az2l4.png)

### Virtual Machine & Lab Guide
 
Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success.
 
## Exploring Your Lab Resources
 
To better understand your lab resources and credentials, navigate to the **Environment** tab.
 
  ![](../media/lab4-envtab.png)
 
## Utilizing the Split Window Feature
 
For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the Top right corner.
 
   ![](../media/lab4-splittab.png)
 
## Managing Your Virtual Machine
 
1. Feel free to start, stop, or restart your virtual machine as needed from the **Resources** tab. Your experience is in your hands!
 
   ![](../media/lab4-restab.png)

1. To initiate the required VMs, use the dropdown menu located at the top of the lab environment

      ![](../Media/nf_g_1_6.png)
 
1. When logging into the Hyper-V virtual machines, if a message appears stating **"Press Ctrl+Alt+Delete to unlock"**, navigate to the **Actions** menu in the Virtual Machine Connection window and select the **Ctrl+Alt+Delete** option, as shown in the image below.

    ![Manage Your Virtual Machine](../Media/login.png)

1. If you face an issue while copying the content from the lab guide and pasting it into the Hyper-V virtual machines, navigate to the **Clipboard** option in the Virtual Machine Connection window and select **Type Clipboard Text**.

    ![Manage Your Virtual Machine](../Media/clipboard.png)  

## Lab Guide Zoom In/Zoom Out
 
1. To adjust the zoom level for the environment page, click the **A↕** icon **(1)** next to the timer, and then select the desired zoom percentage **(2)** from the list.

   ![](../Media/nf_g_1_7.png)
 
## Lab Duration Extension

1. To extend the duration of the lab, kindly click the **Hourglass** icon in the top right corner of the lab environment. 

    ![Manage Your Virtual Machine](../media/timextend.png)

    >**Note:** You will get the **Hourglass** icon when 10 minutes are remaining in the lab.

1. Click **OK** to extend your lab duration.
 
   ![Manage Your Virtual Machine](../Media/gext2.png)

1. If you have not extended the duration before when the lab is about to end, a pop-up will appear, giving you the option to extend. Click **OK** to proceed.

## Pasting Commands in the PowerShell/CloudShell Environment

Please make sure to use the **Shift+Insert** or **CTRL+SHIFT+V** or **CTRL+V** keys when pasting commands inside the PowerShell/CloudShell environment instead of right-clicking

## Let's Get Started with Azure Portal
 
1. On your virtual machine, click on the Azure Portal icon as shown below:
 
   ![Launch Azure Portal](../Media/nf_g_1_8.png)

 
1. You'll see the **Sign into Microsoft Azure** tab. Here, enter your credentials:
 
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
     ![Enter Your Username](../Media/sc900-image-1.png)
 
1. Next, provide your password:
 
   - **Password:** <inject key="AzureAdUserPassword"></inject>
 
      ![](../media/lab4-gs-pass.png)

1. On the **Stay Signed in?** pop, click **Yes**.

   ![](../media/lab4-gs-pass2.png)

1. If a **Welcome to Microsoft Azure** pop-up window appears, simply click **Maybe later** to skip the tour.

   ![](../media/maybelater.png)

## Support Contact
 
The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.
 
Learner Support Contacts:
 
- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Click **Next** from the bottom right corner to embark on your Lab journey!
 
   ![Start Your Azure Journey](../Media/sc900-image(3).png)
 
Now you're all set to explore the powerful world of technology. Feel free to reach out if you have any questions along the way. Enjoy your workshop!

## Happy Learning!!