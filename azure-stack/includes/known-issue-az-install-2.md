---
author: sethmanheim
ms.author: sethm
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: raymondl
ms.lastreviewed: 12/9/2020

---

### When installing Az module falsely throws Admin rights required error

- Applicable: This issue applies to 2002 and later
- Cause: When installing the module from an elevated prompt, an error is thrown. The error says, `Administrator rights required`.
- Remediation: Close your session and start a new elevated PowerShell session. Make sure there isn't an existing Az. Accounts module loaded in the session.
- Occurrence: Common