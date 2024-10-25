---
title: Scale unit node actions in Azure Stack Hub 
description: Learn about scale unit node actions, including power on, power off, disable, resume, and how to view node status in Azure Stack Hub integrated systems.
author: sethmanheim

ms.topic: how-to
ms.date: 1/19/2021
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 1/19/2021

# Intent: As an Azure Stack operator, I want to learn about the scale unit node actions I can take.
# Keyword: azure stack scale unit node actions

---


# Scale unit node actions in Azure Stack Hub

This article describes how to view the status of a scale unit. You can view the unit's nodes. You can run node actions like power on, power off, shut down, drain, resume, and repair. Typically, you use these node actions during field replacement of parts, or to help recover a node.

> [!Important]  
> All node actions described in this article should target one node at a time.

## View the node status

In the administrator portal, you can view the status of a scale unit and its associated nodes.

To view the status of a scale unit:

1. On the **Region management** tile, select the region.
2. On the left, under **Infrastructure resources**, select **Scale units**.
3. In the results, select the scale unit.
4. On the left, under **General**, select **Nodes**.

   View the following information:

   - The list of individual nodes.
   - Operational Status (see list below).
   - Power Status (running or stopped).
   - Server model.
   - IP address of the baseboard management controller (BMC).
   - Total number of cores.
   - Total amount of memory.
   
    Node actions can also raise expected alerts in the administrator portal. 

![status of a scale unit](media/azure-stack-node-actions/multinodeactions.png)

### Node operational states

| Status | Description |
|----------------------|-------------------------------------------------------------------|
| Running | The node is actively participating in the scale unit. |
| Stopped | The node is unavailable. |
| Adding | The node is actively being added to the scale unit. |
| Repairing | The node is actively being repaired. |
| Maintenance | The node is paused, and no active user workload is running. |
| Requires Remediation | An error has been detected that requires the node to be repaired. |

### Azure Stack Hub shows Adding status after an operation

Azure Stack Hub may show the operational node status as **Adding** after an operation like drain, resume, repair, shutdown or start was executed.
This can happen when the Fabric Resource Provider Role cache did not refresh after an operation. 

Before applying the following steps ensure that no operation is currently in progress. Update the endpoint to match your environment.

### [Az modules](#tab/az1)

1. Open PowerShell and add your Azure Stack Hub environment. This requires [Azure Stack Hub PowerShell to be installed](./powershell-install-az-module.md) on your computer.

    ```powershell
    Add-AzEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Connect-AzAccount -Environment AzureStack
    ```

2. Run the following command to restart the Fabric Resource Provider Role.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Validate the operational status of the impacted scale unit node changed to **Running**. You can use the Administrator portal or the following PowerShell command:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. If the node operational status is still shown as **Adding** continue to open a support incident.

### [AzureRM modules](#tab/azurerm1)

1. Open PowerShell and add your Azure Stack Hub environment. This requires [Azure Stack Hub PowerShell to be installed](./powershell-install-az-module.md) on your computer.

    ```powershell
    Add-AzureRMEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
    Add-AzureRMAccount -Environment AzureStack
    ```

2. Run the following command to restart the Fabric Resource Provider Role.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Validate the operational status of the impacted scale unit node changed to **Running**. You can use the Administrator portal or the following PowerShell command:

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. If the node operational status is still shown as **Adding** continue to open a support incident.

---



## Scale unit node actions

When you view information about a scale unit node, you can also perform node actions like:

 - Start and stop (depending on current power status).
 - Disable and resume (depending on operations status).
 - Repair.
 - Shutdown.

The operational state of the node determines which options are available.

You need to install Azure Stack Hub PowerShell modules. These cmdlets are in the **Azs.Fabric.Admin** module. To install or verify your installation of PowerShell for Azure Stack Hub, see [Install PowerShell for Azure Stack Hub](powershell-install-az-module.md).

## Stop

The **Stop** action turns off the node. It's the same as pressing the power button. It doesn't send a shutdown signal to the operating system. For planned stop operations, always try the shutdown operation first.

This action is typically used when a node no longer responds to requests.

To run the stop action, open an elevated PowerShell prompt, and run the following cmdlet:

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

In the unlikely case that the stop action doesn't work, retry the operation and if it fails a second time use the BMC web interface instead.

For more information, see [Stop-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## Start

The **start** action turns on the node. It's the same as if you press the power button.

To run the start action, open an elevated PowerShell prompt, and run the following cmdlet:

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

In the unlikely case that the start action doesn't work, retry the operation. If it fails a second time, use the BMC web interface instead.

For more information, see [Start-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## Drain

The **drain** action moves all active workloads to the remaining nodes in that particular scale unit.

This action is typically used during field replacement of parts, like the replacement of an entire node.

> [!Important]
> Make sure you use a drain operation on a node during a planned maintenance window, where users have been notified. Under some conditions, active workloads can experience interruptions.

To run the drain action, open an elevated PowerShell prompt, and run the following cmdlet:

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

For more information, see [Disable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## Resume

The **resume** action resumes a disabled node and marks it active for workload placement. Earlier workloads that were running on the node don't fail back. (If you use a drain operation on a node be sure to power off. When you power the node back on it's not marked as active for workload placement. When ready, you must use the resume action to mark the node as active.)

To run the resume action, open an elevated PowerShell prompt, and run the following cmdlet:

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

For more information, see [Enable-AzsScaleUnitNode](/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## Repair

> [!CAUTION]  
> Firmware leveling is critical for the success of the operation described in this article. Missing this step can lead to system instability, a decrease in performance, security threats, or failure when Azure Stack Hub automation deploys the operating system. Always consult your hardware partner's documentation when replacing hardware to ensure the applied firmware matches the OEM Version displayed in the [Azure Stack Hub administrator portal](azure-stack-updates.md).<br><br>
For more information and links to partner documentation, see [Replace a hardware component](azure-stack-replace-component.md).

| Hardware Partner | Region | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | All | [Cisco Integrated System for Microsoft Azure Stack Hub Operations Guide](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Release Notes for Cisco Integrated System for Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | All | [Cloud for Microsoft Azure Stack Hub 14G (account and login required)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud for Microsoft Azure Stack Hub 13G (account and login required)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPAN | [Fujitsu managed service support desk (account and login required)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Fujitsu support IT products and systems](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [Fujitsu MySupport (account and login required)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | All | [HPE ProLiant for Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | All | [ThinkAgile SXM Best Recipes](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

The **repair** action repairs a node. Use it only for either of the following scenarios:

- Full node replacement (with or without new data disks).
- After hardware component failure and replacement (if advised in the field replaceable unit [FRU] documentation).

> [!Important]  
> See your OEM hardware vendor's FRU documentation for exact steps when you need to replace a node or individual hardware components. The FRU documentation will specify whether you need to run the repair action after replacing a hardware component.

When you run the repair action, you need to specify the BMC IP address.

To run the repair action, open an elevated PowerShell prompt, and run the following cmdlet:

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## Shutdown

The **shutdown** action first moves all active workloads to the remaining nodes in the same scale unit. Then the action gracefully shuts down the scale unit node.

After you start a node that was shut down, you need to run the [resume](#resume) action. Earlier workloads that were running on the node don't fail back.

If the shutdown operation fails, attempt the [drain](#drain) operation followed by the shutdown operation.

To run the shutdown action, open an elevated PowerShell prompt, and run the following cmdlet:

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## Next steps

- [Install Azure Stack PowerShell](./powershell-install-az-module.md)
- [Learn about the Azure Stack Hub Fabric operator module](/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0&preserve-view=true)
- [Monitor Add node operations](./azure-stack-add-scale-node.md#monitor-add-node-operations)
