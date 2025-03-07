---
title: Virtual network (VNet) data gateway FAQs
description: Frequently asked questions about virtual network (VNet) data gateway.
author: arthiriyer
ms.reviewer: kvivek
ms.prod: on-premises-data-gateway
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: arthii
---

# Virtual network data gateway FAQs 

This article contains a list of frequent asked questions (FAQs) about the virtual network (VNet) data gateways.

### How can I secure connectivity from my network to Power BI?

Use Private links to secure this connectivity. More information: [Power BI Private Links documentation](/power-bi/admin/service-security-private-links)  

### Where is my VNet data gateway?

The VNet data gateway is physically in the same region as your Azure VNet. However, the metadata (name, details, data sources, encrypted credentials, and so on) for all your VNet data gateways are stored in your tenant’s default region. You can manage all VNet data gateways when you select your tenant’s home region in [Power platform admin center](manage-data-gateways.md).

### What data sources are supported on the VNet data gateway?

Complete list of supported data services is available here: [Supported Azure data services](use-data-gateways-sources-power-bi.md#supported-azure-data-services)

### Some of my data sources are connected to my VNet using service endpoint and some using private endpoint. Can I connect to all of them from Power BI using VNet data gateways?

Yes

### Why am I not able to create a service endpoint for my data source in my VNet?

Review [Azure VNet documentation](/azure/virtual-network/virtual-networks-overview) for restrictions (for example, region related) on VNets, endpoints, and associated Azure resources.

### How do I create a private endpoint for my data sources and associate it to a VNet?

Review the corresponding Azure data service product documentation to check if private endpoints are supported and on how to enable them.  

### Can I use this feature if my data source is in East US and my Power BI home region is in East US2?

Yes, there's no dependency on the Power BI home region for this feature. If this feature is enabled in the region where the VNet exists, you'll be able to create a new VNet data gateway.

### Why can’t I connect to the data source?

Few areas to check:
- Make sure your data source is up and running.
- Make sure that the data source can be accessed from within the VNet specifically from the subnet delegated while creating the VNet data gateway. For instance, you could deploy a VM in the subnet and check if you can connect to the data source.
- The following Azure Network Security Groups (NSGs) may be required depending on your scenario:
  - Allow outbound traffic to the AAD endpoint while using OAuth authentication to connect to a data source.
  - Allow outbound traffic to CA (Certificate Authority) while using HTTPS to connect to a data source.

### How is the connectivity between the VNet service and your VNet secured?

The connectivity between the new VNet service and your VNet is via HTTPS and TLS 1.2.

### Are there any known connectivity issues for SQL serverless with auto-pause?

For SQL serverless with auto-pause, the first request might fail if SQL is in a paused state, but the next ones will succeed.

### Is there a delay when the VNet gateway is used for the first time or after a period of inactivity?

When used for the first time the VNet gateway takes about a minute to get set up. Similarly, if the VNet data gateway isn't used for 2 hours, you could experience a delay of about a minute the next time you use it.

### Is this feature supported in sovereign clouds?

No, only commercial clouds will be supported for the public preview release.

### What is the hardware configuration for a VNet data gateway?

Every VNet data gateway has one CPU core 6 GB.

### Can I create multiple VNet data gateways for the same Azure data service?  

Yes

### I'm not able to delete the subnet or the VNet that delegated to Power Platform

Check if there are other gateways using the same VNet and subnet. To be able to delete, it would take up to 48-72 hours after the last gateway using this VNet and subnet was removed. 

### How large does the delegated subnet need to be?

Beyond the reserved IPs, our recommendation is to have approximately 5-7 IPs so you can add more VNet gateways to the same VNet and Subnet.  

### I'm a subscription owner but get an error when I try to create a subscription

Make sure you're explicitly in a role with the Microsoft.Network/virtualNetworks/subnets/join/action permission on the VNet like the Azure Network Contributor role. This permission is required for creating a VNet data gateway.

### Any other known issues?

You can't delegate a subnet called **gatewaysubnet** to the Power Platform admin center. This is because it's a reserved word for Azure Gateway Subnet feature.
