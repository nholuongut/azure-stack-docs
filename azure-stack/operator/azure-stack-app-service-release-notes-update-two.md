---
title: App Service on Azure Stack Hub Update 2 release notes
description: Learn about improvements, fixes, and known issues in Update 2 for App Service on Azure Stack Hub.
author: sethmanheim
manager: stefsch

ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2019
ROBOTS: NOINDEX

# Intent: As an Azure Stack operator, I want to know the release notes for Azure App Service on Azure Stack Update 2.
# Keyword: app service update 2 azure stack
---

# App Service on Azure Stack Hub Update 2 release notes

These release notes describe improvements, fixes, and known issues in Azure App Service on Azure Stack Hub Update 2. Known issues are divided into three sections: issues directly related to deployment, issues with the update process, and issues with the build (post-installation).

> [!IMPORTANT]
> Apply the 1804 update to your Azure Stack Hub integrated system or deploy the latest Azure Stack Development Kit (ASDK) before deploying Azure App Service 1.2.

## Build reference

The App Service on Azure Stack Hub Update 2 build number is **72.0.13698.10**.

## Prerequisites

> [!IMPORTANT]
> New deployments of Azure App Service on Azure Stack Hub now require a three-subject wildcard certificate due to improvements in the way in which SSO for Kudu is handled in Azure App Service. The new subject is: **\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>**

Refer to the [Prerequisites for deploying App Service on Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) before beginning deployment.

## New features and fixes

Azure App Service on Azure Stack Hub Update 2 includes the following improvements and fixes:

- Updates to **App Service Tenant, Admin, Functions portals and Kudu tools**. Consistent with Azure Stack Hub portal SDK version.

- Updates **Azure Functions runtime** to **v1.0.11612**.

- Updates to core service to improve reliability and error messaging enabling easier diagnosis of common issues.

- **Updates to the following application frameworks and tools**:
  - Added .NET Framework 4.7.1
  - Added **Node.JS** versions:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Added **NPM** versions:
    - 5.6.0
  - Updated .NET Core components to be consistent with Azure App Service in public cloud.
  - Updated Kudu

- Auto swap of deployment slots feature enabled - [Configuring Auto Swap](/azure/app-service/deploy-staging-slots#configure-auto-swap).

- Testing in production feature enabled - [Introduction to Testing in Production](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

- Azure Functions Proxies enabled - [Work with Azure Functions Proxies](/azure/azure-functions/functions-proxies).

- App Service admin extension UX support added for:
  - Secret rotation
  - Certificate rotation
  - System credential rotation
  - Connection string rotation

## Known issues (post-installation)

- Workers are unable to reach file server when App Service is deployed in an existing virtual network and the file server is only available on the private network.

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

Refer to the documentation in the [Azure Stack Hub 1804 Release Notes](./release-notes.md?view=azs-1804&preserve-view=true)

## Next steps

- For an overview of Azure App Service, see [Azure App Service on Azure Stack Hub overview](azure-stack-app-service-overview.md).
- For more information about how to prepare to deploy App Service on Azure Stack Hub, see [Prerequisites for deploying App Service on Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
