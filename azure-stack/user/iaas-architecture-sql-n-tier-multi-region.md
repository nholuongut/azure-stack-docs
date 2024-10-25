---
title:  Run N-tier application in multiple Azure Stack Hub regions for high availability 
description: Learn how to run an N-tier application in multiple Azure Stack Hub regions for high availability.
author: sethmanheim

ms.topic: how-to
ms.date: 2/1/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019

# Intent: Notdone: As a < type of user >, I want < what? > so that < why? >
# Keyword: Notdone: keyword noun phrase

---


# Run an N-tier application in multiple Azure Stack Hub regions for high availability

This reference architecture shows a set of proven practices for running across an N-tier application multiple Azure Stack Hub regions, in order to achieve availability and a robust disaster recovery infrastructure. In this document, Traffic Manager is used to achieve high availability, however if Traffic Manager is not a preferred choice in your environment, a pair of highly available load balancers could also be substituted in.

> [!NOTE]  
> Please note the Traffic Manager used in the architecture below needs to be configured in Azure and the endpoints used to configure the Traffic Manager profile need to be publicly routable IPs.

## Architecture

This architecture builds on the one shown in [N-tier application with SQL Server](iaas-architecture-windows-sql-n-tier.md).

![Highly available network architecture for Azure N-tier applications](./media/iaas-architecturesql-n-tier-multi-region/image1.png)

-   **Primary and secondary regions**. Use two regions to achieve higher availability. One is the primary region. The other region is for failover.

-   **Azure Traffic Manager**. [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) routes incoming requests to one of the regions. During normal operations, it routes requests to the primary region. If that region becomes unavailable, Traffic Manager fails over to the secondary region. For more information, see the section [Traffic Manager configuration](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server#traffic-manager-configuration).

-   **Resource groups**. Create separate [resource groups](/azure/azure-resource-manager/resource-group-overview) for the primary region, the secondary region. This gives you the flexibility to manage each region as a single collection of resources. For example, you could redeploy one region, without taking down the other one. Link the resource groups, so that you can run a query to list all the resources for the application.

-   **Virtual networks**. Create a separate virtual network for each region. Make sure the address spaces do not overlap.

-   **SQL Server Always On Availability Group**. If you are using SQL Server, we recommend [SQL Always On Availability Groups](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true) for high availability. Create a single availability group that includes the SQL Server instances in both regions.

-   **VNET to VNET VPN Connection**. As VNET Peering is not yet available on Azure Stack Hub, use VNET to VNET VPN connection in order to connect the two VNETs. Please see [VNET to VNET in Azure Stack Hub](./azure-stack-network-howto-vnet-to-vnet.md) for more information.

## Recommendations

A multi-region architecture can provide higher availability than deploying to a single region. If a regional outage affects the primary region, you can use [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) to fail over to the secondary region. This architecture can also help if an individual subsystem of the application fails.

There are several general approaches to achieving high availability across regions:

-   **Active/passive with hot standby**. Traffic goes to one region, while the other waits on hot standby. Hot standby means the VMs in the secondary region are allocated and running at all times.

-   **Active/passive with cold standby**. Traffic goes to one region, while the other waits on cold standby. Cold standby means the VMs in the secondary region are not allocated until needed for failover. This approach costs less to run, but will generally take longer to come online during a failure.

-   **Active/active**. Both regions are active, and requests are load balanced between them. If one region becomes unavailable, it is taken out of rotation.

This reference architecture focuses on active/passive with hot standby, using Traffic Manager for failover. You could deploy a small number of VMs for hot standby and then scale out as needed.

### Traffic Manager configuration

Consider the following points when configuring Traffic Manager:

-   **Routing**. Traffic Manager supports several [routing algorithms](/azure/traffic-manager/traffic-manager-routing-methods). For the scenario described in this article, use *priority* routing (formerly called *failover* routing). With this setting, Traffic Manager sends all requests to the primary region, unless the primary region becomes unreachable. At that point, it automatically fails over to the secondary region. See [Configure Failover routing method](/azure/traffic-manager/traffic-manager-configure-failover-routing-method).

-   **Health probe**. Traffic Manager uses an HTTP (or HTTPS) [probe](/azure/traffic-manager/traffic-manager-monitoring) to monitor the availability of each region. The probe checks for an HTTP 200 response for a specified URL path. As a best practice, create an endpoint that reports the overall health of the application, and use this endpoint for the health probe. Otherwise, the probe might report a healthy endpoint when critical parts of the application are actually failing. For more information, see [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring).

When Traffic Manager fails over there is a period of time when clients cannot reach the application. The duration is affected by the following factors:

-   The health probe must detect that the primary region has become unreachable.

-   DNS servers must update the cached DNS records for the IP address, which depends on the DNS time-to-live (TTL). The default TTL is 300 seconds (5 minutes), but you can configure this value when you create the Traffic Manager profile.

For details, see [About Traffic Manager Monitoring](/azure/traffic-manager/traffic-manager-monitoring).

If Traffic Manager fails over, we recommend performing a manual failback rather than implementing an automatic failback. Otherwise, you can create a situation where the application flips back and forth between regions. Verify that all application subsystems are healthy before failing back.

Note that Traffic Manager automatically fails back by default. To prevent this, manually lower the priority of the primary region after a failover event. For example, suppose the primary region is priority 1 and the secondary is priority 2. After a failover, set the primary region to priority 3, to prevent automatic failback. When you are ready to switch,  back, update the priority to 1.

The following [Azure CLI](/cli/azure/) command updates the priority:

```azurecli  
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --priority 3
```

Another approach is to temporarily disable the endpoint until you are ready to fail back:

```azurecli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --endpoint-status Disabled
```

Depending on the cause of a failover, you might need to redeploy the resources within a region. Before failing back, perform an operational readiness test. The test should verify things like:

-   VMs are configured correctly. (All required software is installed, IIS is running, and so on.)

-   Application subsystems are healthy.

-   Functional testing. (For example, the database tier is reachable from the web tier.)

### Configure SQL Server Always On Availability Groups

Prior to Windows Server 2016, SQL Server Always On Availability Groups require a domain controller, and all nodes in the availability group must be in the same Active Directory (AD) domain.

To configure the availability group:

-   At a minimum, place two domain controllers in each region.

-   Give each domain controller a static IP address.

-   Create [VPN](./azure-stack-vpn-gateway-about-vpn-gateways.md) to enable communication between two virtual networks.

-   For each virtual network, add the IP addresses of the domain controllers (from both regions) to the DNS server list. You can use the following CLI command. For more information, see [Change DNS servers](/azure/virtual-network/manage-virtual-network#change-dns-servers).

    ```azurecli
    az network vnet update --resource-group <resource-group> --name <vnet-name> --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

-   Create a [Windows Server Failover Clustering](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-ver15&preserve-view=true) (WSFC) cluster that includes the SQL Server instances in both regions.

-   Create a SQL Server Always On Availability Group that includes the SQL Server instances in both the primary and secondary regions. See [Extending Always On Availability Group to Remote Azure Datacenter (PowerShell)](https://techcommunity.microsoft.com/t5/DataCAT/Extending-AlwaysOn-Availability-Group-to-Remote-Azure-Datacenter/ba-p/305217) for the steps.

    -   Put the primary replica in the primary region.

    -   Put one or more secondary replicas in the primary region. Configure these to use synchronous commit with automatic failover.

    -   Put one or more secondary replicas in the secondary region. Configure these to use *asynchronous* commit, for performance reasons. (Otherwise, all T-SQL transactions have to wait on a round trip over the network to the secondary region.)

> [!NOTE]  
> Asynchronous commit replicas don't support automatic failover.

## Availability considerations

With a complex N-tier app, you may not need to replicate the entire application in the secondary region. Instead, you might just replicate a critical subsystem that is needed to support business continuity.

Traffic Manager is a possible failure point in the system. If the Traffic Manager service fails, clients cannot access your application during the downtime. Review the [Traffic Manager SLA](https://azure.microsoft.com/support/legal/sla/traffic-manager), and determine whether using Traffic Manager alone meets your business requirements for high availability. If not, consider adding another traffic management solution as a failback. If the Azure Traffic Manager service fails, change your CNAME records in DNS to point to the other traffic management service. (This step must be performed manually, and your application will be unavailable until the DNS changes are propagated.)

For the SQL Server cluster, there are two failover scenarios to consider:

-   All of the SQL Server database replicas in the primary region fail. For example, this could happen during a regional outage. In that case, you must manually fail over the availability group, even though Traffic Manager automatically fails over on the front end. Follow the steps in [Perform a Forced Manual Failover of a SQL Server Availability Group](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-ver15&preserve-view=true), which describes how to perform a forced failover by using SQL Server Management Studio, Transact-SQL, or PowerShell in SQL Server 2016.

    > [!Warning]  
    > With forced failover, there is a risk of data loss. Once the primary region is back online, take a snapshot of the database and use [tablediff](/sql/tools/tablediff-utility?view=sql-server-ver15&preserve-view=true) to find the differences.

-   Traffic Manager fails over to the secondary region, but the primary SQL Server database replica is still available. For example, the front-end tier might fail, without affecting the SQL Server VMs. In that case, Internet traffic is routed to the secondary region, and that region can still connect to the primary replica. However, there will be increased latency, because the SQL Server connections are going across regions. In this situation, you should perform a manual failover as follows:

    1.  Temporarily switch a SQL Server database replica in the secondary region to *synchronous* commit. This ensures there won't be data loss during the failover.

    2.  Fail over to that replica.

    3.  When you fail back to the primary region, restore the asynchronous commit setting.

## Manageability considerations

When you update your deployment, update one region at a time to reduce the chance of a global failure from an incorrect configuration or an error in the application.

Test the resiliency of the system to failures. Here are some common failure scenarios to test:

-   Shut down VM instances.

-   Pressure resources such as CPU and memory.

-   Disconnect/delay network.

-   Crash processes.

-   Expire certificates.

-   Simulate hardware faults.

-   Shut down the DNS service on the domain controllers.

Measure the recovery times and verify they meet your business requirements. Test combinations of failure modes, as well.

## Next steps

- To learn more about Azure Cloud Patterns, see [Cloud Design Patterns](/azure/architecture/patterns).
