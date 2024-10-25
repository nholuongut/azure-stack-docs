---
title: Replace a network daughter card
description: Learn how to replace a network daughter card
author: sethmanheim

ms.topic: how-to
ms.date: 02/05/2021
ms.author: sethm
ms.reviewer: 
ms.lastreviewed: 

# Intent: 
# Keyword: 

---

# Replacing a network daughter card

Use the following procedure to replace a network daughter card or
cards.

## Prerequisites

1.  Review *Notes, cautions, and warnings* at the beginning of this
    guide

2.  Review Handling precautions.

3.  Review

    -   Required knowledge for working with scale unit nodes in Azure Stack Hub ruggedized

    -   Required knowledge for working with the Hardware Lifecycle Host on page 5 if you are working with the Hardware Lifecycle Host

4.  Complete

    -   Verifying scale unit node access and health if you are working with a scale unit node

    -   Verifying Hardware Lifecycle Host access and
        health if you are working with the Hardware Lifecycle Host

5.  Complete

    -   Powering off scale unit nodes if you are working with a scale unit node

    -   Powering off the Hardware Lifecycle
        Host if
        you are working with the Hardware Lifecycle Host


## Steps

1.  Locate the physical node in the rack.

2.  Verify that the node is powered off. The power LED should be orange.

    > [!CAUTION]
    > Before disconnecting the cables on the server you are working on, ensure that each cable is properly labeled. The cables MUST be reconnected to the same ports.
    
3.  Replace the network daughter card or cards.

    Follow the [network daughter card replacement process](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4)
    for scale unit nodes or Hardware Lifecycle Hosts.
    
4.  Power on the node.

    After reconnecting the power, if the server does not automatically
    reboot, press the power button to turn the node back on.
    
## Next steps

If you are working with a scale unit node:

1.  Complete Powering on and repairing a scale unit node.

2.  Complete Verifying scale unit node
    health. If you are
    working with the Hardware Lifecycle Host:

    -   Complete Verifying Hardware Lifecycle Host
        health
    
