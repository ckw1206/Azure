---
title: Monitor data from virtual machines with Azure Monitor agent
description: Describes how to collect events and performance data from virtual machines using the Azure Monitor agent.
ms.topic: conceptual
ms.date: 06/23/2022
author: guywild
ms.author: guywild
ms.reviewer: shseth

---

# Collect data from virtual machines with the Azure Monitor agent

This article describes how to collect events and performance counters from virtual machines using the Azure Monitor agent. 

To collect data from virtual machines using the Azure Monitor agent, you'll:

1. Create [data collection rules (DCR)](../essentials/data-collection-rule-overview.md) that define which data Azure Monitor agent sends to which destinations.
1. Associate the data collection rule to specific virtual machines.

    You can associate virtual machines to multiple data collection rules. This allows you to define each data collection rule to address a particular requirement, and associate the data collection rules to virtual machines based on the specific data you want to collect from each machine.

## Create data collection rule and association

To send data to Log Analytics, create the data collection rule in the **same region** as your Log Analytics workspace. You can still associate the rule to machines in other supported regions.

### [Portal](https://portal.azure.com/)

1. From the **Monitor** menu, select **Data Collection Rules**. 
1. Select **Create** to create a new Data Collection Rule and associations.  
1. Provide a **Rule name** and specify a **Subscription**, **Resource Group**, **Region**, and **Platform Type**. 

    **Region** specifies where the DCR will be created. The virtual machines and their associations can be in any subscription or resource group in the tenant.

    **Platform Type** specifies the type of resources this rule can apply to. Custom allows for both Windows and Linux types. 

1. On the **Resources** tab, add the resources (virtual machines, virtual machine scale sets, Arc for servers) to which to associate the data collection rule. The portal will install Azure Monitor Agent on resources that don't already have it installed, and will also enable Azure Managed Identity.

    > [!IMPORTANT]
    > The portal enables System-Assigned managed identity on the target resources, in addition to existing User-Assigned Identities (if any). For existing applications, unless you specify the User-Assigned identity in the request, the machine will default to using System-Assigned Identity instead.  

    If you need network isolation using private links, select existing endpoints from the same region for the respective resources, or [create a new endpoint](https://docs.microsoft.com/en-us/azure/azure-monitor/essentials/data-collection-endpoint-overview?tabs=portal).

1. On the **Collect and deliver** tab, select **Add data source** to add a data source and set a destination.
1. Select a **Data source type**.
1. Select which data you want to collect. For performance counters, you can select from a predefined set of objects and their sampling rate. For events, you can select from a set of logs and severity levels. 

1. Select **Custom** to collect logs and performance counters that are not [currently supported data sources](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/azure-monitor-agent-overview#data-sources-and-destinations) or to [filter events using XPath queries](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/data-collection-rule-azure-monitor-agent?tabs=portal#filter-events-using-xpath-queries). You can then specify an [XPath](https://www.w3schools.com/xml/xpath_syntax.asp) to collect any specific values. See [Sample DCR](https://docs.microsoft.com/en-us/azure/azure-monitor/agents/data-collection-rule-sample-agent) for an example.

1. On the **Destination** tab, add one or more destinations for the data source. You can select multiple destinations of the same or different types - for instance multiple Log Analytics workspaces (known as "multi-homing"). 

    You can send Windows event and Syslog data sources can to Azure Monitor Logs only. You can send performance counters to both Azure Monitor Metrics and Azure Monitor Logs.

1. Select **Add Data Source** and then **Review + create** to review the details of the data collection rule and association with the set of virtual machines. 
1. Select **Create** to create the data collection rule.

> [!NOTE]
> It might take up to 5 minutes for data to be sent to the destinations after you create the data collection rule and associations.