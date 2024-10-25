---
title: Azure Stack Hub SQL resource provider 1.1.93.x release notes 
description: View the release notes to see what's new in the Azure Stack Hub SQL resource provider 1.1.93.x update.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020

# Intent: As an Azure Stack Hub operator, I want the release notes for the SQL resource provider 1.1.93.x update.
# Keyword: azure stack hub sql resource provider 1.1.93.x release notes

---

# SQL resource provider 1.1.93.x release notes

These release notes describe the improvements and known issues in SQL resource provider version 1.1.93.x.

## Build reference
Download the SQL resource provider binary and then run the self-extractor to extract the contents to a temporary directory. The resource provider has a minimum corresponding Azure Stack Hub build. The minimum Azure Stack Hub release version required to install this version of the SQL resource provider is listed below:

> |Supported Azure Stack Hub version|SQL resource provider version|
> |-----|-----|
> |Version 2108*， 2102, 2008, 2005|[SQL RP version 1.1.93.5](https://aka.ms/azshsqlrp11935)|  
> |     |     |

> [!NOTE]
> It is supported to run SQL RP 1.1.93.x on Azure Stack 2108, however it is an known issue that the monitoring panel cannot load.

> [!IMPORTANT]
> Apply the minimum supported Azure Stack Hub update to your Azure Stack Hub integrated system before deploying the latest version of the MySQL resource provider.

## New features and fixes

This version of the Azure Stack Hub SQL resource provider includes the following improvements and fixes:

- **Update the base VM to a specialized Windows Server.** This Windows Server version is specialize for Azure Stack Hub Add-On RP Infrastructure and it is not visible to the tenant marketplace. Make sure to download the **Microsoft AzureStack Add-On RP Windows Server** image before deploying or upgrading to this version of the SQL resource provider.
- **Support removing orphaned database metadata and hosting server metadata.** When a hosting server cannot be connected anymore, the tenant will have an option to remove the orphaned database metadata from the portal. When there is no orphaned database metadata linked to the hosting server, the operator will be able to remove the orphaned hosting server metadata from the admin portal.
- **Make KeyVaultPfxPassword an optional argument when performing secrets rotation.** Check [this document](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) for details.
- **Other bug fixes.**

It's recommended that you apply SQL resource provider 1.1.93.5 after Azure Stack Hub is upgraded to the 2005 release.

## Known issue
Deployment of 1.1.93.0 version may fail if the wrong AzureRmContext is used. It is recommended to upgrade to 1.1.93.5 version directly. 

When redeploying the SQL resource provider while the same version had deployed already (for example, when SQL resource provider 1.1.93.5 is already deployed, and the same version is deployed again), the VM that is hosting the SQL resource provider will be stopped. To fix this issue, go to the admin portal, locate and restart the VM named sqlvm\<version\> in the resource group named system.\<region\>.sqladapter.

## Next steps

- [Learn more about the SQL resource provider](azure-stack-sql-resource-provider.md).
- [Prepare to deploy the SQL resource provider](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Upgrade the SQL resource provider from a previous version](azure-stack-sql-resource-provider-update.md).

