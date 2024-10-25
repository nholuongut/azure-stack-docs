---
title: Configure security controls in Azure Stack Hub
description: Learn how to configure security controls in Azure Stack Hub.
author: sethmanheim
ms.topic: how-to
ms.date: 03/04/2020
ms.author: sethm
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019

# Intent: As an Azure Stack Hub operator, I want to learn how to configure security controls in Azure Stack Hub so I can customize them to my liking.
# Keyword: configure security controls azure stack hub

---

# Configure Azure Stack Hub security controls

This article explains the security controls that can be changed in Azure Stack Hub and highlights the tradeoffs where applicable.

Azure Stack Hub architecture is built on two security principle pillars: assume breach and hardened by default. For more information on Azure Stack Hub security, see [Azure Stack Hub infrastructure security posture](azure-stack-security-foundations.md). While the default security posture of Azure Stack Hub is production-ready, there are some deployment scenarios that require additional hardening.

## TLS version policy

The Transport Layer Security (TLS) protocol is a widely adopted cryptographic protocol to establish encrypted communication over the network. TLS has evolved over time and multiple versions have been released. Azure Stack Hub infrastructure exclusively uses TLS 1.2 for all its communications. For external interfaces, Azure Stack Hub currently defaults to use TLS 1.2. However, for backwards compatibility, it also supports negotiating down to TLS 1.1. and 1.0. When a TLS client requests to communicate over TLS 1.1 or TLS 1.0, Azure Stack Hub honors the request by negotiating to a lower TLS version. If the client requests TLS 1.2, Azure Stack Hub will establish a TLS connection using TLS 1.2.

Since TLS 1.0 and 1.1 are incrementally being deprecated or banned by organizations and compliance standards you can now configure the TLS policy in Azure Stack Hub. You can enforce a TLS 1.2 only policy where any attempt of establishing a TLS session with a version lower than 1.2 isn't permitted and is rejected.

> [!IMPORTANT]
> Microsoft recommends using TLS 1.2 only policy for Azure Stack Hub production environments.

### Get TLS policy

Use the [privileged endpoint (PEP)](azure-stack-privileged-endpoint.md) to view the TLS policy for all Azure Stack Hub endpoints:

```powershell
Get-TLSPolicy
```

Example output:

```powershell
TLS_1.2
```

### Set TLS policy

Use the [privileged endpoint (PEP)](azure-stack-privileged-endpoint.md) to set the TLS policy for all Azure Stack Hub endpoints:

```powershell
Set-TLSPolicy -Version <String>
```

Parameters for *Set-TLSPolicy* cmdlet:

| Parameter | Description | Type | Required |
|-----|-----|-----|-----|
| *Version* | Allowed version(s) of TLS in Azure Stack Hub | String | yes |

Use one of the following values to configure the permitted TLS versions for all Azure Stack Hub endpoints:

| Version value | Description |
|-------|-------|
| *TLS_All* | Azure Stack Hub TLS endpoints support TLS 1.2, but down negotiation to TLS 1.1 and TLS 1.0 is allowed. |
| *TLS_1.2* | Azure Stack Hub TLS endpoints support TLS 1.2 only. |

Updating the TLS policy takes a few minutes to complete.

#### Enforce TLS 1.2 configuration example

This example sets your TLS policy to enforce TLS 1.2 only.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Example output:

```powershell
VERBOSE: Successfully setting enforce TLS 1.2 to True
VERBOSE: Invoking action plan to update GPOs
VERBOSE: Create Client for execution of action plan
VERBOSE: Start action plan
<...>
VERBOSE: Verifying TLS policy
VERBOSE: Get GPO TLS protocols registry 'enabled' values
VERBOSE: GPO TLS applied with the following preferences:
VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
VERBOSE: TLS 1.2 is enforced
```

#### Allow all versions of TLS (1.2, 1.1, and 1.0) configuration example

This example sets your TLS policy to allow all versions of TLS (1.2, 1.1, and 1.0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Example output:

```powershell
VERBOSE: Successfully setting enforce TLS 1.2 to False
VERBOSE: Invoking action plan to update GPOs
VERBOSE: Create Client for execution of action plan
VERBOSE: Start action plan
<...>
VERBOSE: Verifying TLS policy
VERBOSE: Get GPO TLS protocols registry 'enabled' values
VERBOSE: GPO TLS applied with the following preferences:
VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
VERBOSE: TLS 1.2 is not enforced
```

## Legal notice for PEP sessions

There are scenarios where it's useful to display a legal notice, upon login to a [privileged endpoint (PEP)](azure-stack-privileged-endpoint.md) session. The [Set-AzSLegalNotice](../reference/pep/set-azslegalnotice.md) and [Get-AzSLegalNotice](../reference/pep/get-azslegalnotice.md) cmdlets are used to manage the caption and body of such legal notice text.

To set the legal notice caption and text, see the [Set-AzSLegalNotice cmdlet](../reference/pep/set-azslegalnotice.md). If the legal notice caption and text have previously been set, you can review them by using the [Get-AzSLegalNotice cmdlet](../reference/pep/get-azslegalnotice.md).

## Next steps

- [Learn about Azure Stack Hub infrastructure security posture](azure-stack-security-foundations.md).
- [Learn how to rotate your secrets in Azure Stack Hub](azure-stack-rotate-secrets.md).
- [Update Windows Defender Antivirus on Azure Stack Hub](azure-stack-security-av.md).
