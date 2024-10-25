---
author: sethmanheim
ms.author: sethm
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: raymondl
ms.lastreviewed: 12/9/2020

---

### Error thrown when installing the Az modules

- Applicable: This issue applies to 2002 and later
- Cause: When installing the module, an error is thrown. The error message begins: `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` Or the error message may include the following text: `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- Remediation: Run the following cmdlet in the same session:  
    `Install-Module PowershellGet -MinimumVersion 2.3.0 -Force`  
Close your session and start a new elevated PowerShell session.
- Occurrence: Common
