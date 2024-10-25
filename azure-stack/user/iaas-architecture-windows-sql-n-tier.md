---
title:  Windows N-tier application on Azure Stack Hub with SQL Server 
description: Learn how to run a Windows N-tier application on Azure Stack Hub with SQL Server.
author: sethmanheim

ms.topic: how-to
ms.date: 2/1/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019

# Intent: Notdone: As a < type of user >, I want < what? > so that < why? >
# Keyword: Notdone: keyword noun phrase

---


# Windows N-tier application on Azure Stack Hub with SQL Server

This reference architecture shows how to deploy virtual machines (VMs) and a virtual network configured for an [N-tier](/azure/architecture/guide/architecture-styles/n-tier) application, using SQL Server on Windows for the data tier. 

## Architecture

The architecture has the following components.

![The diagram shows a virtual network comprising six subnets: Application Gateway, Management, Web tier, Business tier, Data tier, and Active Directory. The Data tier subnet uses Cloud Witness. There are three load balancers.](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## General

-   **Resource group**. [Resource groups](/azure/azure-resource-manager/resource-group-overview) are used to group Azure resources so they can be managed by lifetime, owner, or other criteria.

-   **Availability Set.** Availability set is a datacenter configuration to provide VM redundancy and availability. This configuration within an Azure Stack Hub stamp ensures that during either a planned or unplanned maintenance event, at least one virtual machine is available. VMs are placed in an availability set that spreads them across multiple fault domains (Azure Stack Hub hosts)

## Networking and load balancing

-   **Virtual network and subnets**. Every Azure VM is deployed into a virtual network that can be segmented into subnets. Create a separate subnet for each tier.

-   **Layer 7 Load Balancer.** As Application Gateway is not yet available on Azure Stack Hub, there are alternatives available on [Azure Stack Hub Market place](../operator/azure-stack-marketplace-azure-items.md) such as: [KEMP LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) or [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Load balancers**. Use [Azure Load Balancer ](/azure/load-balancer/load-balancer-overview)to distribute network traffic from the web tier to the business tier, and from the business tier to SQL Server.

-   **Network security groups** (NSGs). Use NSGs to restrict network traffic within the virtual network. For example, in the three-tier architecture shown here, the database tier does not accept traffic from the web front end, only from the business tier and the management subnet.

-   **DNS**. Azure Stack Hub does not provide its own DNS hosting service, so please use the DNS server in your ADDS.

**Virtual machines**

-   **SQL Server Always On Availability Group**. Provides high availability at the data tier, by enabling replication and failover. It uses Windows Server Failover Cluster (WSFC) technology for failover.

-   **Active Directory Domain Services (AD DS) Servers**. The computer objects for the failover cluster and its associated clustered roles are created in Active Directory Domain Services (AD DS). Set up AD DS servers in VMs in the same virtual network are preferred method to join other VMs to AD DS. You can also join the VMs to existing Enterprise AD DS by connecting virtual network to Enterprise network with VPN connection. With both approaches, you need to change the virtual network DNS to your AD DS DNS server (in virtual network or existing Enterprise network) to resolve the AD DS domain FQDN.

-   **Cloud Witness**. A failover cluster requires more than half of its nodes to be running, which is known as having quorum. If the cluster has just two nodes, a network partition could cause each node to think it's the control plane node. In that case, you need a *witness* to break ties and establish quorum. A witness is a resource such as a shared disk that can act as a tie breaker to establish quorum. Cloud Witness is a type of witness that uses Azure Blob Storage. To learn more about the concept of quorum, see [Understanding cluster and pool quorum](/windows-server/storage/storage-spaces/understand-quorum). For more information about Cloud Witness, see [Deploy a Cloud Witness for a Failover Cluster](/windows-server/failover-clustering/deploy-cloud-witness). In Azure Stack Hub, the Cloud Witness endpoint is different from global Azure. 

It may look like:

- For global Azure:  
  `https://mywitness.blob.core.windows.net/`

- For Azure Stack Hub:  
  `https://mywitness.blob.<region>.<FQDN>`

-   **Jumpbox**. Also called a [bastion host](https://en.wikipedia.org/wiki/Bastion_host). A secure VM on the network that administrators use to connect to the other VMs. The jumpbox has an NSG that allows remote traffic only from public IP addresses on a safe list. The NSG should permit remote desktop (RDP) traffic.

## Recommendations

Your requirements might differ from the architecture described here. Use these recommendations as a starting point.

### Virtual machines

For recommendations on configuring the VMs, see [Run a Windows VM on Azure Stack Hub](iaas-architecture-vm-windows.md).

### Virtual network

When you create the virtual network, determine how many IP addresses your resources in each subnet require. Specify a subnet mask and a network address range large enough for the required IP addresses, using [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) notation. Use an address space that falls within the standard [private IP address blocks](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces), which are 10.0.0.0/8, 172.16.0.0/12, and 192.168.0.0/16.

Choose an address range that does not overlap with your on-premises network, in case you need to set up a gateway between the virtual network and your on-premises network later. Once you create the virtual network, you can't change the address range.

Design subnets with functionality and security requirements in mind. All VMs within the same tier or role should go into the same subnet, which can be a security boundary. For more information about designing virtual networks and subnets, see [Plan and design Azure Virtual Networks](/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### Load balancers

Don't expose the VMs directly to the Internet, but instead give each VM a private IP address. Clients connect using the public IP address associated with the Layer 7 Load Balancer.

Define load balancer rules to direct network traffic to the VMs. For example, to enable HTTP traffic, map port 80 from the front-end configuration to port 80 on the back-end address pool. When a client sends an HTTP request to port 80, the load balancer selects a back-end IP address by using a [hashing algorithm](/azure/load-balancer/concepts#limitations) that includes the source IP address. Client requests are distributed across all the VMs in the back-end address pool.

### Network security groups

Use NSG rules to restrict traffic between tiers. In the three-tier architecture shown above, the web tier does not communicate directly with the database tier. To enforce this rule, the database tier should block incoming traffic from the web tier subnet.

1.  Deny all inbound traffic from the virtual network. (Use the VIRTUAL_NETWORK tag in the rule.)

2.  Allow inbound traffic from the business tier subnet.

3.  Allow inbound traffic from the database tier subnet itself. This rule allows communication between the database VMs, which is needed for database replication and failover.

4.  Allow RDP traffic (port 3389) from the jumpbox subnet. This rule lets administrators connect to the database tier from the jumpbox.

Create rules 2 - 4 with higher priority than the first rule, so they override it.

## SQL Server Always On Availability Groups

We recommend [Always On Availability Groups](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true) for SQL Server high availability. Prior to Windows Server 2016, Always On Availability Groups require a domain controller, and all nodes in the availability group must be in the same AD domain.

For VM layer high availability, all SQL VMs should be in an Availability Set.

Other tiers connect to the database through an [availability group listener](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true). The listener enables a SQL client to connect without knowing the name of the physical instance of SQL Server. VMs that access the database must be joined to the domain. The client (in this case, another tier) uses DNS to resolve the listener's virtual network name into IP addresses.

Configure the SQL Server Always On Availability Group as follows:

1.  Create a Windows Server Failover Clustering (WSFC) cluster, a SQL Server Always On Availability Group, and a primary replica. For more information, see [Getting Started with Always On Availability Groups](/sql/database-engine/availability-groups/windows/getting-started-with-always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true).

2.  Create an internal load balancer with a static private IP address.

3.  Create an availability group listener, and map the listener's DNS name to the IP address of an internal load balancer.

4.  Create a load balancer rule for the SQL Server listening port (TCP port 1433 by default). The load balancer rule must enable *floating IP*, also called Direct Server Return. This causes the VM to reply directly to the client, which enables a direct connection to the primary replica.

> [!NOTE]
> When floating IP is enabled, the front-end port number must be the same as the back-end port number in the load balancer rule.

When a SQL client tries to connect, the load balancer routes the connection request to the primary replica. If there is a failover to another replica, the load balancer automatically routes new requests to a new primary replica. For more information, see [Configure an ILB listener for SQL Server Always On Availability Groups](/azure/azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure).

During a failover, existing client connections are closed. After the failover completes, new connections will be routed to the new primary replica.

If your application makes more reads than writes, you can offload some of the read-only queries to a secondary replica. See [Using a Listener to Connect to a Read-Only Secondary Replica (Read-Only Routing)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true#ConnectToSecondary).

Test your deployment by [forcing a manual failover](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-ver15&preserve-view=true) of the availability group.

For SQL performance optimization, you can also refer the article [SQL server best practices to optimize performance in Azure Stack Hub](./azure-stack-sql-server-vm-considerations.md).

**Jumpbox**

Don't allow RDP access from the public Internet to the VMs that run the application workload. Instead, all RDP access to these VMs should go through the jumpbox. An administrator logs into the jumpbox, and then logs into the other VM from the jumpbox. The jumpbox allows RDP traffic from the Internet, but only from known, safe IP addresses.

The jumpbox has minimal performance requirements, so select a small VM size. Create a [public IP address](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) for the jumpbox. Place the jumpbox in the same virtual network as the other VMs, but in a separate management subnet.

To secure the jumpbox, add an NSG rule that allows RDP connections only from a safe set of public IP addresses. Configure the NSGs for the other subnets to allow RDP traffic from the management subnet.

## Scalability considerations

### Scale sets

For the web and business tiers, consider using [virtual machine scale sets](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview) instead of deploying separate VMs. A scale set makes it easy to deploy and manage a set of identical VMs. Consider scale sets if you need to quickly scale out VMs.

There are two basic ways to configure VMs deployed in a scale set:

-   Use extensions to configure the VM after it's deployed. With this approach, new VM instances may take longer to start up than a VM with no extensions.

-   Deploy a [managed disk](./azure-stack-managed-disk-considerations.md) with a custom disk image. This option may be quicker to deploy. However, it requires you to keep the image up-to-date.

For more information, see [Design considerations for scale sets](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). This design consideration is mostly true for Azure Stack Hub, however there are some caveats:

-   Virtual machine scale sets on Azure Stack Hub do not support overprovisioning or rolling upgrades.

-   You cannot autoscale virtual machine scale sets on Azure Stack Hub.

-   We strongly recommend using Managed disks on Azure Stack Hub instead of unmanaged disks for virtual machine scale set

-   Currently, there is a 700 VM limit on Azure Stack Hub, which accounts for all Azure Stack Hub infrastructure VMs, individual VMs, and scale set instances.

## Subscription limits

Each Azure Stack Hub tenant subscription has default limits in place, including a maximum number of VMs per region configured by the Azure Stack Hub operator. For more information, see [Azure Stack Hub services, plans, offers, subscriptions overview](../operator/service-plan-offer-subscription-overview.md). Also refer to [Quota types in Azure Stack Hub](../operator/azure-stack-quota-types.md).

## Security considerations

Virtual networks are a traffic isolation boundary in Azure. By default, VMs in one virtual network can't communicate directly with VMs in a different virtual network.

**NSGs**. Use [network security groups](/azure/virtual-network/virtual-networks-nsg) (NSGs) to restrict traffic to and from the internet. For more information, see [Microsoft cloud services and network security](/azure/best-practices-network-security).

**DMZ**. Consider adding a network virtual appliance (NVA) to create a DMZ between the Internet and the Azure virtual network. NVA is a generic term for a virtual appliance that can perform network-related tasks, such as firewall, packet inspection, auditing, and custom routing.

**Encryption**. Encrypt sensitive data at rest and use [Key Vault in Azure Stack Hub](./azure-stack-key-vault-manage-portal.md) to manage the database encryption keys. For more information, see [Configure Azure Key Vault Integration for SQL Server on Azure VMs](/azure/azure-sql/virtual-machines/windows/azure-key-vault-integration-configure). It's also recommended to store application secrets, such as database connection strings, in Key Vault.

## Next steps

- To learn more about Azure Cloud Patterns, see [Cloud Design Patterns](/azure/architecture/patterns).
