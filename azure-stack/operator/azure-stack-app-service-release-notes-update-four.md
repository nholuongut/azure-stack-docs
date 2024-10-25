---
title: App Service on Azure Stack Hub Update 4 release notes
description: Learn about improvements, fixes, and known issues in Update 4 for App Service on Azure Stack Hub.
author: sethmanheim
manager: stefsch

ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ROBOTS: NOINDEX

# Intent: As an Azure Stack operator, I want to know the release notes for Azure App Service on Azure Stack Update 4.
# Keyword: app service update 4 azure stack
---

# App Service on Azure Stack Hub Update 4 release notes

These release notes describe improvements, fixes, and known issues in Azure App Service on Azure Stack Hub Update 4. Known issues are divided into three sections: issues directly related to deployment, issues with the update process, and issues with the build (post-installation).

> [!IMPORTANT]
> Apply the 1809 update to your Azure Stack Hub integrated system or deploy the latest Azure Stack Development Kit (ASDK) before deploying Azure App Service 1.4.

## Build reference

The App Service on Azure Stack Hub Update 4 build number is **78.0.13698.5**

## Prerequisites

Refer to the [Prerequisites for deploying App Service on Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) before beginning deployment.

Before you begin the upgrade of Azure App Service on Azure Stack Hub to 1.4:

- Ensure all roles are ready in the Azure App Service administration in the Azure Stack Hub administrator portal.

- Backup App Service Secrets using the App Service Administration in the Azure Stack Hub Admin Portal

- Back up the App Service and Master Databases:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Back up the Tenant App content file share

  > [!Important]
  > Cloud operators are responsible for the maintenance and operation of the File Server and SQL Server.  The resource provider does not manage these resources.  The cloud operator is responsible for backing up the App Service databases and tenant content file share.

- Syndicate the **Custom Script Extension** version **1.9** from Azure Marketplace.

## New features and fixes

Azure App Service on Azure Stack Hub Update 4 includes the following improvements and fixes:

- Resolution for [CVE 2018-8600](https://aka.ms/CVE20188600) cross-site scripting (XSS) vulnerability.

- Added support for App Service 2018-02-01 API version.

- Updates to **App Service Tenant, Admin, Functions portals and Kudu tools**. Consistent with Azure Stack Hub portal SDK version.

- Updates **Azure Functions runtime** to **v1.0.11959**.

- Updates to core service to improve reliability and error messaging enabling easier diagnosis of common issues.

- **Updates to the following app frameworks and tools**:
  - Added NodeJS 10.6.0
  - Added NPM 6.1.0
  - Added Zulu OpenJDK 8.31.0.2
  - Added Tomcat 8.5.34 and 9.0.12
  - Added PHP Versions:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Update to Python versions:
    - 2.7.15
    - 3.6.6
  - Updated Git for Windows to v 2.17.1.2
  - Updated Kudu to 78.11022.3613
  
- **Updates to underlying operating system of all roles**:
  - [2018-10 Cumulative Update for Windows Server 2016 for x64-based Systems (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Resolved template validation issue when deploying Wordpress, DNN, and Orchard CMS gallery items.

- Resolved configuration issue when Azure Stack Hub rotates the Azure Resource Manager client certificate.

- Restored functionality in the cross-origin resource sharing settings in the App Service user portal.

- Error message is now displayed in App Service administrator portal when the resource provider control plane can't connect to the configured SQL Server instance.

- Ensure endpoint is specified in custom storage connection string when specified in new Function app.

## Post-deployment steps

> [!IMPORTANT]  
> If you've provided the App Service resource provider with a SQL Always On Instance you *must* [add the appservice_hosting and appservice_metering databases to an availability group](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) and synchronize the databases to prevent any loss of service in the event of a database failover.

## Post-update steps (optional)

For customers wishing to migrate to a contained database for existing Azure App Service on Azure Stack Hub deployments, execute these steps after the Azure App Service on Azure Stack Hub 1.4 update has completed:

> [!IMPORTANT]
> The migration procedure takes approximately 5-10 minutes. The procedure involves killing the existing database login sessions. Plan for downtime to migrate and validate Azure App Service on Azure Stack Hub post migration. If you completed these steps after updating to Azure App Service on Azure Stack Hub 1.3 then these steps aren't required.

1. Add [AppService databases (appservice_hosting and appservice_metering) to an Availability group](/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Enable contained database.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Converting a database to partially contained, the conversion will incur downtime as all active sessions need to be killed.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    ```

1. Migrate logins to contained database users.

    ```sql
        USE appservice_hosting
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO

        USE appservice_metering
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

**Validate**

1. Check if SQL Server has containment enabled.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Check existing contained behavior.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

## Known issues (post-installation)

- Workers are unable to reach file server when App Service is deployed in an existing virtual network and the file server is only available on the private network. This issue is called out in the Azure App Service on Azure Stack Hub deployment documentation.

If you chose to deploy into an existing virtual network and an internal IP address to connect to your file server, you must add an outbound security rule which enables SMB traffic between the worker subnet and the file server. Go to the WorkersNsg in the administrator portal and add an outbound security rule with the following properties:

 * Source: Any
 * Source port range: *
 * Destination: IP addresses
 * Destination IP address range: Range of IPs for your file server
 * Destination port range: 445
 * Protocol: TCP
 * Action: Allow
 * Priority: 700
 * Name: Outbound_Allow_SMB445

## Known issues for cloud admins operating Azure App Service on Azure Stack Hub

Refer to the documentation in the [Azure Stack Hub 1809 Release Notes](./release-notes.md?view=azs-1809&preserve-view=true)

## Next steps

- For an overview of Azure App Service, see [Azure App Service on Azure Stack Hub overview](azure-stack-app-service-overview.md).
- For more info about how to prepare to deploy App Service on Azure Stack Hub, see [Prerequisites for deploying App Service on Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
