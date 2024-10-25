---
author: sethmanheim
ms.author: sethm
ms.service: azure-stack
ms.topic: include
ms.date: 07/14/2021
ms.reviewer: sethm
ms.lastreviewed: 07/14/2021
---

> [!NOTE]
> Secret rotation for value-add resource providers (RPs) is currently only supported via PowerShell. Also, you must proactively rotate secrets for value-add RPs on a regular basis, as administrative alerts are currently not generated.

Like the Azure Stack Hub infrastructure, value-add resource providers use both internal and external secrets. Secrets can take multiple forms, including passwords and the encryption keys maintained by X509 certificates. As an operator, you're responsible for:

- Providing updated external secrets, such as a new TLS certificate used to secure resource provider endpoints.
- Managing resource provider secret rotation on a regular basis.

In preparation for the rotation process:

1. Review [Azure Stack Hub public key infrastructure (PKI) certificate requirements](../operator/azure-stack-pki-certs.md#certificate-requirements) for important prerequisite information before acquiring/renewing your X509 certificate, including details on the required PFX format. Also review the requirements specified in the [Optional PaaS certificates section](../operator/azure-stack-pki-certs.md#optional-paas-certificates), for your specific value-add resource provider.

2. If you haven't already, [Install PowerShell Az module for Azure Stack Hub](../operator/powershell-install-az-module.md) before continuing. Version 2.0.2-preview or later is required for Azure Stack Hub secret rotation. For more information, see [Migrate from AzureRM to Azure PowerShell Az in Azure Stack Hub](../operator/migrate-azurerm-az.md).
