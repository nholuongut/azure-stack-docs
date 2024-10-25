---
title: Replace scale unit node on Azure Stack Hub integrated system
titleSuffix: Azure Stack Hub
description: Learn how to replace a physical scale unit node on an Azure Stack Hub integrated system.
author: sethmanheim

ms.topic: how-to
ms.date: 03/04/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019

# Intent: As an Azure Stack operator, I want to replace a physical scale unit node in Azure Stack.
# Keyword: replace scale unit node azure stack

---


# Replace a scale unit node on an Azure Stack Hub integrated system

This article describes the general process to replace a physical computer (also referred to as a scale unit node) on an Azure Stack Hub integrated system. Actual scale unit node replacement steps will vary based on your original equipment manufacturer (OEM) hardware vendor. See your vendor's field replaceable unit (FRU) documentation for detailed steps that are specific to your system.

> [!CAUTION]  
> Firmware leveling is critical for the success of the operation described in this article. Missing this step can lead to system instability, performance decrease, security threads, or prevent Azure Stack Hub automation from deploying the operating system. Always consult your hardware partner's documentation when replacing hardware to ensure the applied firmware matches the OEM Version displayed in the [Azure Stack Hub administrator portal](azure-stack-updates.md). For more information and links to partner documentation, see [Replace a hardware component](azure-stack-replace-component.md).

The following flow diagram shows the general FRU process to replace an entire scale unit node.

![Flow chart for replace node process](media/azure-stack-replace-node/replacenodeflow.png)

*This action may not be required based on the physical condition of the hardware.

> [!Note]  
> If the shutdown operation does fail, it's recommended to use the drain operation followed by the stop operation. For more information, see [Scale unit node actions in Azure Stack Hub](./azure-stack-node-actions.md).

## Review alert information

If a scale unit node is down, you'll receive the following critical alerts:

- Node not connected to network controller
- Node inaccessible for virtual machine placement
- Scale unit node is offline

![List of alerts for scale unit down](media/azure-stack-replace-node/nodedownalerts.png)

If you open the **Scale unit node is offline** alert, the alert description contains the scale unit node that's inaccessible. You may also receive additional alerts in the OEM-specific monitoring solution that's running on the hardware lifecycle host.

![Details of node offline alert](media/azure-stack-replace-node/nodeoffline.png)

## Scale unit node replacement process

The following steps are provided as a high-level overview of the scale unit node replacement process. See your OEM hardware vendor's FRU documentation for detailed steps that are specific to your system. Don't follow these steps without referring to your OEM-provided documentation.

1. Use the **Shutdown** action to gracefully shut down the scale unit node. This action may not be required based on the physical condition of the hardware.

2. In the unlikely case the shutdown action fails, use the [Drain](azure-stack-node-actions.md#drain) action to put the scale unit node into maintenance mode. This action may not be required based on the physical condition of the hardware.

   > [!NOTE]  
   > In any case, only one node can be disabled and powered off at the same time without breaking the S2D (Storage Spaces Direct).

3. After the scale unit node is in maintenance mode, use the [Stop](azure-stack-node-actions.md#stop) action. This action may not be required based on the physical condition of the hardware.

   > [!NOTE]  
   > In the unlikely case that the Power off action doesn't work, use the baseboard management controller (BMC) web interface instead.

4. Replace the physical computer. Typically, this replacement is done by your OEM hardware vendor.
5. Use the [Repair](azure-stack-node-actions.md#repair) action to add the new physical computer to the scale unit.
6. Use the privileged endpoint to [check the status of virtual disk repair](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). With new data drives, a full storage repair job can take multiple hours depending on system load and consumed space.
7. After the repair action has finished, validate that all active alerts have been automatically closed.

## Next steps

- For information about replacing a physical disk while the system is powered on, see [Replace a disk](azure-stack-replace-disk.md). 
- For information about replacing a hardware component that requires the system to be powered off, see [Replace a hardware component](azure-stack-replace-component.md).
