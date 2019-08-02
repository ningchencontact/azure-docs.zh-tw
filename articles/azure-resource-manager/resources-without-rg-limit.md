---
title: 沒有800計數限制的 Azure 資源
description: 列出資源群組中可有超過800個實例的 Azure 資源類型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: a796896450a5b786e3b78aeddd81e6d66b02eb94
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700453"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>每個資源群組的資源不限於800實例

根據預設, 您可以在每個資源群組中部署最多800個資源類型的實例。 不過, 某些資源類型會豁免800實例的限制。 本文列出在資源群組中可以有超過800個實例的 Azure 資源類型。 所有其他資源類型的限制為800實例。

針對某些資源類型, 您必須聯絡支援人員以移除800實例的限制。 這篇文章中注明了這些資源類型。


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrations/customerSubscriptions
* registrations/products

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices-聯絡支援以擴充限制。

## <a name="microsoftcompute"></a>Microsoft.Compute

* 磁碟
* 映像
* 快照集
* virtualMachines

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registries/buildTasks
* 登錄/buildTasks/listSourceRepositoryProperties
* registries/buildTasks/steps
* 登錄/buildTasks/步驟/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* 伺服器

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* 伺服器

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* 伺服器
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* 服務

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/全部
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses-聯絡支援以擴充限制。
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections-聯絡支援以擴充限制。

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* 網站

## <a name="next-steps"></a>後續步驟

如需配額和限制的完整清單, 請參閱[Azure 訂用帳戶和服務限制、配額和條件約束](../azure-subscription-service-limits.md)。
