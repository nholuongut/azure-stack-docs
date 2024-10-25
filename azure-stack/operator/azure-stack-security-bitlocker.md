---
title: Data at Rest Encryption
titleSuffix: Azure Stack Hub
description: Learn how Azure Stack Hub protects your data with encryption at rest.
author: sethmanheim

ms.topic: how-to
ms.date: 03/04/2020
ms.author: sethm
ms.reviewer: fiseraci
ms.lastreviewed: 04/21/2021

# Intent: As an Azure Stack operator, I want to learn to protect my data with encryption at rest.
# Keyword: rest encryption azure stack

---


# Data at rest encryption in Azure Stack Hub

Azure Stack Hub protects user and infrastructure data at the storage subsystem level using encryption at rest. By default, Azure Stack Hub's storage subsystem is encrypted using BitLocker. Systems deployed before release 2002 use BitLocker with 128-bit AES encryption; systems deployed starting with 2002, or newer, use BitLocker with AES-256 bit encryption. BitLocker keys are persisted in an internal secret store. 

Data at rest encryption is a common requirement for many of the major compliance standards (for example, PCI-DSS, FedRAMP, HIPAA). Azure Stack Hub enables you to meet those requirements with no extra work or configurations required. For more information on how Azure Stack Hub helps you meet compliance standards, see the [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Data at rest encryption protects your data against being accessed by someone who physically stole one or more hard drives. Data at rest encryption doesn't protect against data being intercepted over the network (data in transit), data currently being used (data in memory), or, more in general, data being exfiltrated while the system is up and running.

## Retrieving BitLocker recovery keys

Azure Stack Hub BitLocker keys for data at rest are internally managed. You aren't required to provide them for regular operations or during system startup. However, support scenarios may require BitLocker recovery keys to bring the system online.  

> [!WARNING]
> Retrieve your BitLocker recovery keys and store them in a secure location outside of Azure Stack Hub. Not having the recovery keys during certain support scenarios may result in data loss and require a system restore from a backup image.

Retrieving the BitLocker recovery keys requires access to the [privileged endpoint](azure-stack-privileged-endpoint.md) (PEP). From a PEP session, run the Get-AzsRecoveryKeys cmdlet.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys -raw
```

Parameters for *Get-AzsRecoveryKeys* cmdlet:

| Parameter | Description | Type | Required |
|---------|---------|---------|---------|
|*raw* | Returns data mapping between recovery key, computer name, and password id(s) of each encrypted volume.  | Switch | No, but recommended |

## Troubleshoot issues

In extreme circumstances, a BitLocker unlock request could fail resulting in a specific volume to not boot. Depending on the availability of some of the components of the architecture, this failure could result in downtime and potential data loss if you don't have your BitLocker recovery keys.

> [!WARNING]
> Retrieve your BitLocker recovery keys and store them in a secure location outside of Azure Stack Hub. Not having the recovery keys during certain support scenarios may result in data loss and require a system restore from a backup image.

If you suspect your system is experiencing issues with BitLocker, such as Azure Stack Hub failing to start, contact support. Support requires your BitLocker recovery keys. The majority of the BitLocker related issues can be resolved with a FRU operation for that specific VM/host/volume. For the other cases, a manual unlocking procedure using BitLocker recovery keys can be done. If BitLocker recovery keys aren't available, the only option is to restore from a backup image. Depending on when the last backup was done, you may experience data loss.

## Next steps

- [Learn more about Azure Stack Hub security](azure-stack-security-foundations.md).
- For more information on how BitLocker protects CSVs, see [protecting cluster shared volumes and storage area networks with BitLocker](/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).
