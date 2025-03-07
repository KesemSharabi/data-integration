---
title: Adjust communication settings for the on-premises data gateway
description: Discusses how to fix blocked outbound connections, how to configure certain ports for the on-premises data gateway to create an outbound connection to Azure Service Bus, how to force the gateway to communicate with Azure Service Bus by using HTTPS instead of direct TCP, and how to ensure your gateway machine is using TLS 1.2 to communicate with the Microsoft Power BI service.
author: arthiriyer
ms.author: arthii
manager: kvivek
ms.reviewer: kvivek
ms.prod: on-premises-data-gateway
ms.technology:
ms.topic: conceptual
LocalizationGroup: Gateways
ms.date: 07/15/2019
---

# Adjust communication settings for the on-premises data gateway

This article describes several communication settings associated with the on-premises data gateway. It also describes how to adjust those settings.

## Enable outbound Azure connections

The gateway relies on Azure Service Bus for cloud connectivity. The gateway correspondingly establishes outbound connections to its associated Azure region.

If you registered for either a Power BI tenant or an Office 365 tenant, your Azure region defaults to the region of that service. Otherwise, your Azure region might be the one closest to you.

If a firewall blocks outbound connections, configure the firewall to allow outbound connections from the gateway to its associated Azure region.

## Ports

The gateway communicates on the following outbound ports: TCP 443, 5671, 5672, and from 9350 through 9354. The gateway doesn't require inbound ports.

We recommend that you allow the IP addresses for your data region in your firewall. Please use the JSON files listed below which are updated weekly.
>* [Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519)
>* [US Gov](https://www.microsoft.com/en-us/download/details.aspx?id=57063)
>* [Germany](https://www.microsoft.com/en-us/download/details.aspx?id=57064)
>* [China](https://www.microsoft.com/en-us/download/details.aspx?id=57062)(https://www.microsoft.com/download/details.aspx?id=41653)

Or, you can get the list of required ports by performing the [network ports test](#network-ports-test) periodically in the gateway app.

The gateway communicates with Service Bus by using an IP address along with a fully qualified domain name (FQDN). If you force the gateway to communicate via HTTPS, it will strictly use FQDNs only and won't communicate by using IP addresses.

> [!NOTE]
> The Azure datacenter IP list shows IP addresses in Classless Inter-Domain Routing (CIDR) notation. An example of this notation is 10.0.0.0/24, which doesn't mean from 10.0.0.0 through 10.0.0.24. Learn more about [CIDR notation](https://whatismyipaddress.com/cidr).

The following list describes FQDNs used by the gateway.

| Public Cloud Domain names | Outbound ports | Description |
| --- | --- | --- |
| *.download.microsoft.com |80 |Used to download the installer. The gateway app also uses this domain to check the version and gateway region. |
| *.powerbi.com |443 |Used to identify the relevant Power BI cluster. |
| *.analysis.windows.net |443 |Used to identify the relevant Power BI cluster. |
| *.login.windows.net, login.live.com, and aadcdn.msauth.net |443 |Used to authenticate the gateway app for Azure Active Directory (Azure AD) and OAuth2. |
| *.servicebus.windows.net |5671-5672 |Used for Advanced Message Queuing Protocol (AMQP). |
| *.servicebus.windows.net |443 and 9350-9354 |Listens on Service Bus Relay over TCP. Port 443 is required to get Azure Access Control tokens. |
| *.frontend.clouddatahub.net |443 |Deprecated and not required. This domain will be removed from the public documentation as well. |
| *.core.windows.net |443 |Used by dataflows to write data to Azure Data Lake. |
| login.microsoftonline.com |443 |Used to authenticate the gateway app for Azure AD and OAuth2. |
| *.msftncsi.com |443 |Used to test internet connectivity if the Power BI service can't reach the gateway. |
| *.microsoftonline-p.com |443 |Used to authenticate the gateway app for Azure AD and OAuth2. |
| *.dc.services.visualstudio.com |443 |Used by AppInsights to collect telemetry. |

For GCCC, GCC high and DoD, the following FQDNs are used by the gateway.

| Ports | GCC | GCC High | DoD |
| --- | --- | --- | --- |
| 80 | *.download.microsoft.com |*.download.microsoft.com |*.download.microsoft.com |
| 443 | *.powerbigov.us, *.powerbi.com  |*.high.powerbigov.us |*.mil.powerbigov.us|
| 443 | *.analysis.usgovcloudapi.net |*.high.analysis.usgovcloudapi.net |*.mil.analysis.usgovcloudapi.net |
| 443 | *.login.windows.net, *.login.live.com, *.aadcdn.msauth.net |[See documentation](/microsoft-365/enterprise/microsoft-365-u-s-government-gcc-high-endpoints?preserve-view=true&view=o365-worldwide#microsoft-365-common-and-office-online)|[See documentation](/microsoft-365/enterprise/microsoft-365-u-s-government-gcc-high-endpoints?preserve-view=true&view=o365-worldwide#microsoft-365-common-and-office-online) |
|5671-5672| *.servicebus.usgovcloudapi.net |*.servicebus.usgovcloudapi.net|*.servicebus.usgovcloudapi.net |
|443 and 9350-9354| *.servicebus.usgovcloudapi.net |*.servicebus.usgovcloudapi.net |*.servicebus.usgovcloudapi.net |
|443| *.core.usgovcloudapi.net|*.core.usgovcloudapi.net|*.core.usgovcloudapi.net |
|443| *.login.microsoftonline.com |*.login.microsoftonline.us |*.login.microsoftonline.us |
|443| *.msftncsi.com |*.msftncsi.com |*.msftncsi.com |
|443| *.microsoftonline-p.com |*.microsoftonline-p.com |*.microsoftonline-p.com |
|443| *.dc.applicationinsights.us |*.dc.applicationinsights.us |*.dc.applicationinsights.us |

> [!NOTE]
> After the gateway is installed and registered, the only required ports and IP addresses are those needed by Service Bus, as described for servicebus.windows.net in the preceding table. You can get the list of required ports by performing the [Network ports test](#network-ports-test) periodically in the gateway app. You can also force the gateway to [communicate using HTTPS](#force-https-communication-with-azure-service-bus).



## Network ports test

To test if the gateway has access to all required ports:

1. On the machine that is running the gateway, enter "gateway" in Windows search, and then select the **On-premises data gateway** app.

1. Select **Diagnostics**. Under **Network ports test**, select **Start new test**.

   ![How to start a new network ports test](media/service-gateway-communication/gateway-start-new-test.png)

When your gateway runs the network ports test, it retrieves a list of ports and servers from Service Bus and then attempts to connect to all of them. When the **Start new test** link reappears, the network ports test has finished.

The summary result of the test is either "Completed (Succeeded)" or "Completed (Failed, see last test results)". If the test succeeded, your gateway connected to all the required ports. If the test failed, your network environment might have blocked the required ports and servers.

To view the results of the last completed test, select the **Open last completed test results** link. The test results open in your default text editor.

The test results list all the servers, ports, and IP addresses that your gateway requires. If the test results display "Closed" for any ports as shown in the following screenshot, ensure that your network environment didn't block those connections. You might need to contact your network admin to open the required ports.

![Test results shown in Notepad](media/service-gateway-communication/gateway-onprem-porttest-result-file.png)

## Force HTTPS communication with Azure Service Bus

You can force the gateway to communicate with Service Bus by using HTTPS instead of direct TCP.

> [!NOTE]
> Starting with the June 2019 gateway release and based on recommendations from Service Bus, new installations default to HTTPS instead of TCP. This default behavior doesn't apply to updated installations.

You can use the [gateway app](service-gateway-app.md) to force the gateway to adopt this behavior. In the gateway app, select **Network**, and then turn on **HTTPS mode**.

![Setting the HTTPS mode](./media/service-gateway-communication/forcing-https.png)

After you make this change and then select **Apply**, the gateway Windows service restarts automatically so that the change can take effect. The **Apply** button appears only when you make a change.

To restart the gateway Windows service from the gateway app, see [Restart a gateway](service-gateway-restart.md).

> [!NOTE]
>If the gateway can't communicate by using TCP, it automatically uses HTTPS. The selection in the gateway app always reflects the current protocol value.

## TLS 1.2 for gateway traffic

By default, the gateway uses Transport Layer Security (TLS) 1.2 to communicate with the Power BI service. To ensure all gateway traffic uses TLS 1.2, you might need to add or modify the following registry keys on the machine that runs the gateway service.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\.NETFramework\v4.0.30319]"SchUseStrongCrypto"=dword:00000001
```

> [!NOTE]
> Adding or modifying these registry keys applies the change to all .NET applications. For information about registry changes that affect TLS for other applications, see [Transport Layer Security (TLS) registry settings](/windows-server/security/tls/tls-registry-settings).

## Next steps

* [Configure the gateway log file](service-gateway-log-files.md)


[!INCLUDE[footer-include](../includes/footer-banner.md)]
