---
title: Apply an OEM update to Azure Stack Hub
description: Learn to apply an original equipment manufacturer (OEM) update to Azure Stack Hub.
author: sethmanheim
ms.topic: how-to
ms.date: 06/24/2021
ms.author: sethm
ms.lastreviewed: 03/04/2020
ms.reviewer: ppacent

# Intent: As an Azure Stack Hub operator, I want to apply an OEM update to Azure Stack Hub so I can get driver updates and security patches.
# Keyword: OEM update azure stack hub

---

# Apply Azure Stack Hub original equipment manufacturer (OEM) updates

You can apply original equipment manufacturer (OEM) updates to your Azure Stack Hub hardware components to get driver updates, firmware updates, and security patches. These updates are done while minimizing impact on your users. In this article, you can learn about OEM updates, OEM contact information, and how to apply an OEM update.

## Overview of OEM updates

In addition to Microsoft Azure Stack Hub updates, many OEMs also release regular updates for your Azure Stack Hub hardware, such as driver and firmware updates. These updates are referred to as **OEM package updates**. To understand whether your OEM releases OEM package updates, check your [OEM's Azure Stack Hub documentation](#oem-contact-information).

These OEM package updates are uploaded into the **updateadminaccount** storage account and applied via the Azure Stack Hub administrator portal. For more information, see [Applying OEM updates](#apply-oem-updates).

Ask your OEM about their specific notification process to ensure OEM package update notifications reach your organization.

Some hardware vendors may require a *hardware vendor VM* that handles the internal firmware update process. For more information, see [Configure hardware vendor VM](#configure-hardware-vendor-vm).

## OEM contact information

This section contains OEM contact information and links to OEM Azure Stack Hub reference material.

| Hardware Partner | Region | URL |
|-----|----|-----|
| Cisco | All | [Cisco Integrated System for Microsoft Azure Stack Hub Operations Guide](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_01000.html)<br><br>[UCS C-Series Rack-Mount UCS-Managed Server Software](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/series.html) |
| Dell EMC | All | [Cloud for Microsoft Azure Stack Hub 14G (account and login required)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack Hub 13G (account and login required)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu managed service support desk (account and login required)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA & US | [Fujitsu support IT products and systems](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | All | [HPE ProLiant for Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM Best Recipes](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Microsoft | All | [Azure Stack Hub Ruggedized](../ruggedized/oem-release-notes-rca.md)
| Wortmann |  | [OEM/firmware package](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/)<br>[terra Azure Stack Hub documentation (including FRU)](https://aka.ms/aa6zktc)

## Apply OEM updates

Apply the OEM packages with the following steps:

> [!IMPORTANT]
> Before applying updates in Azure Stack Hub, ensure you've completed **ALL** steps in the [Pre-update checklist](release-notes-checklist.md) and have scheduled an appropriate maintenance window for the update type that you're applying.

1. Contact your OEM to:
      - Determine the current version of your OEM package.
      - Find the best method to download your OEM package.
2. Before applying an OEM package update, always apply the latest Azure Stack Hub hotfix available on your system's current Azure Stack Hub version. For more information on hotfixes, see [Azure Stack Hub hotfixes](azure-stack-servicing-policy.md).
3. Prepare your OEM package with the steps outlined in [Download update packages for integrated systems](azure-stack-servicing-policy.md).
4. Apply the updates with the steps outlined in [Apply updates in Azure Stack Hub](azure-stack-apply-updates.md).

## Configure hardware vendor VM

Some hardware vendors may require a virtual machine (VM) to help with the OEM update process. Your hardware vendor is responsible for creating these VMs and documenting if you require `ProxyVM` or `HardwareManager` for **-VMType** when running the **Set-OEMExternalVM** cmdlet, as well as which credential should be used for **-Credential**. Once the VMs are created, configure them with the **Set-OEMExternalVM** from the privileged endpoint.

For more information on the privileged endpoint in Azure Stack Hub, see [Using the privileged endpoint in Azure Stack Hub](azure-stack-privileged-endpoint.md).

1. Access the privileged endpoint.

    ```powershell
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS> `
    -ConfigurationName PrivilegedEndpoint -Credential $cred `
    -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    ```

2. Configure the hardware vendor VM using the **Set-OEMExternalVM** cmdlet. The cmdlet validates the IP address and credentials for **-VMType** `ProxyVM`. For **-VMType** `HardwareManager`, the cmdlet won't validate the input. The **-Credential** parameter provided to **Set-OEMExternalVM** is one that will be clearly documented by the hardware vendor documentation.  It is *NOT* the CloudAdmin credential used with the privileged endpoint, or any other existing Azure Stack Hub credential.

    ```powershell
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        {
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## Next steps

- [Azure Stack Hub updates](azure-stack-updates.md)
