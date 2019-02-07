---
title: 依 Azure 資源類型區分的移動作業支援 | Microsoft Docs
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: dfed8ce93a97b31380b44f0ac7057c0cff60901e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497276"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援

此文章列出 Azure 資源類型是否支援移動作業。 雖然資源類型支援作業，但可能會有導致無法移動資源的情況。 如需有關影響移動作業之情況的詳細資料，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="find-resource-provider-and-resource-type"></a>尋找資源提供者和資源類型

若要判斷是否可以移動某個資源，您必須找出其資源提供者和資源類型。

對於 PowerShell，請使用：

```azurepowershell-interactive
Get-AzResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

對於 Azure CLI，請使用：

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

傳回資源類型時，會以 `<resource-provider>/<resource-type-name>` 格式傳回。 因此，`Microsoft.OperationalInsights/workspaces` 值的資源提供者為 **Microsoft.OperationalInsights**，而資源類型名稱則為 **workspaces**。

找出資源提供者和資源類型之後，請使用此文章中的表格來判斷該資源類型是否支援移動作業。

## <a name="microsoftaad"></a>Microsoft.AAD

| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | --------------- | ----------- |
| domainservices | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| servers | yes | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | --------------- | ----------- |
| service | yes | yes |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | --------------- | ----------- |
| policyassignments | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| automationaccounts | yes | yes |
| automationaccounts/configurations | yes | yes |
| automationaccounts/runbooks | yes | yes |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| b2cdirectories | yes | yes |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| registrations | yes | yes |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| backupvault | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| batchaccounts | yes | yes |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| mapapis | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| biztalk | yes | yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| blueprintassignments | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| botservices | yes | yes |

## <a name="microsoftcache"></a>Microsoft.Cache
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| redis | yes | yes |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 設定檔 | yes | yes |
| profiles/endpoints | yes | yes |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| certificateorders | yes | yes |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| domainnames | yes | 否 |
| virtualmachines | yes | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| networksecuritygroups | 否 | 否 |
| reservedips | 否 | 否 |
| virtualnetworks | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| storageaccounts | yes | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| availabilitysets | yes | yes |
| disks | yes | yes |
| galleries | 否 | 否 |
| galleries/images | 否 | 否 |
| galleries/images/versions | 否 | 否 |
| images | yes | yes |
| restorepointcollections | 否 | 否 |
| sharedvmimages | 否 | 否 |
| sharedvmimages/versions | 否 | 否 |
| snapshots | yes | yes |
| virtualmachines | yes | yes |
| virtualmachines/extensions | yes | yes |
| virtualmachinescalesets | yes | yes |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| registries | yes | yes |
| registries/buildtasks | yes | yes |
| registries/replications | 否 | 否 |
| registries/tasks | yes | yes |
| registries/webhooks | yes | yes |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| containerservices | 否 | 否 |
| managedclusters | 否 | 否 |
| openshiftmanagedclusters | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 應用程式所需 | yes | yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 連接器 | yes | yes |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| hubs | yes | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| jobs | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| databoxedgedevices | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| workspaces | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| catalogs | yes | yes |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| datafactories | yes | yes |
| factories | yes | yes |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 服務 | 否 | 否 |
| services/projects | 否 | 否 |
| slots | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| servers | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| servers | yes | yes |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| servergroups | 否 | 否 |
| servers | yes | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| artifactsources | 否 | 否 |
| rollouts | 否 | 否 |
| servicetopologies | 否 | 否 |
| servicetopologies/services | 否 | 否 |
| servicetopologies/services/serviceunits | 否 | 否 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| iothubs | yes | yes |
| provisioningservices | yes | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| labcenters | 否 | 否 |
| labs | yes | 否 |
| labs/servicerunners | yes | yes |
| labs/virtualmachines | yes | 否 |
| schedules | 否 | 否 |

## <a name="microsoftdns"></a>microsoft.dns
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| dnszones | 否 | 否 |
| dnszones/a | 否 | 否 |
| dnszones/aaaa | 否 | 否 |
| dnszones/cname | 否 | 否 |
| dnszones/mx | 否 | 否 |
| dnszones/ptr | 否 | 否 |
| dnszones/srv | 否 | 否 |
| dnszones/txt | 否 | 否 |
| trafficmanagerprofiles | 否 | 否 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| databaseaccounts | yes | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| domains | yes | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| topics | yes | yes |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |
| namespaces | yes | yes |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| hanainstances | yes | yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| dedicatedhsms | 否 | 否 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| jobs | yes | yes |

## <a name="microsoftinsights"></a>microsoft.insights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| actiongroups | yes | yes |
| activitylogalerts | 否 | 否 |
| alertrules | yes | yes |
| autoscalesettings | yes | yes |
| components | yes | yes |
| metricalerts | 否 | 否 |
| scheduledqueryrules | yes | yes |
| webtests | yes | yes |
| workbooks | yes | yes |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| iotapps | yes | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| vaults | yes | yes |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| labaccounts | yes | yes |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| integrationaccounts | yes | yes |
| workflows | yes | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| commitmentplans | yes | yes |
| webservices | yes | 否 |
| workspaces | yes | yes |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| operationalizationclusters | yes | yes |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |
| accounts/workspaces | yes | yes |
| accounts/workspaces/projects | yes | yes |
| teamaccounts | yes | yes |
| teamaccounts/workspaces | yes | yes |
| teamaccounts/workspaces/projects | yes | yes |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| workspaces | yes | yes |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| userassignedidentities | yes | yes |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| accounts | yes | yes |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| classicdevservices | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| mediaservices | yes | yes |
| mediaservices/liveevents | yes | yes |
| mediaservices/streamingendpoints | yes | yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| projects | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| applicationgateways | 否 | 否 |
| applicationsecuritygroups | yes | yes |
| azurefirewalls | 否 | 否 |
| connections | yes | yes |
| ddosprotectionplans | 否 | 否 |
| dnszones | yes | yes |
| expressroutecircuits | 否 | 否 |
| expressroutecrossconnections | 否 | 否 |
| expressroutegateways | 否 | 否 |
| expressrouteports | 否 | 否 |
| frontdoors | yes | yes |
| frontdoorwebapplicationfirewallpolicies | yes | yes |
| interfaceendpoints | 否 | 否 |
| loadbalancers | yes | yes |
| localnetworkgateways | yes | yes |
| networkintentpolicies | yes | yes |
| networkinterfaces | yes | yes |
| networkprofiles | 否 | 否 |
| networksecuritygroups | yes | yes |
| networkwatchers | yes | yes |
| networkwatchers/connectionmonitors | yes | yes |
| networkwatchers/lenses | yes | yes |
| networkwatchers/pingmeshes | yes | yes |
| publicipaddresses | yes | yes |
| publicipprefixes | yes | yes |
| routefilters | 否 | 否 |
| routetables | yes | yes |
| serviceendpointpolicies | yes | yes |
| trafficmanagerprofiles | yes | yes |
| virtualhubs | yes | yes |
| virtualnetworkgateways | yes | yes |
| virtualnetworks | yes | yes |
| virtualnetworktaps | 否 | 否 |
| virtualwans | yes | yes |
| vpngateways | yes | yes |
| vpnsites | yes | yes |
| webapplicationfirewallpolicies | yes | yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| namespaces | yes | yes |
| namespaces/notificationhubs | yes | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| workspaces | yes | yes |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| managementconfigurations | yes | yes |
| solutions | yes | yes |
| 檢視 | yes | yes |

## <a name="microsoftportal"></a>Microsoft.Portal
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| dashboards | yes | yes |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| workspacecollections | yes | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| capacities | yes | yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| vaults | yes | yes |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| namespaces | yes | yes |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 應用程式所需 | yes | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| flows | yes | yes |
| jobcollections | yes | yes |

## <a name="microsoftsearch"></a>Microsoft.Search
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| searchservices | yes | yes |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| namespaces | yes | yes |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| clusters | yes | yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 應用程式所需 | yes | yes |
| networks | yes | yes |
| 磁碟區 | yes | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| signalr | yes | yes |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| siterecoveryvault | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| appliancedefinitions | 否 | 否 |
| appliances | 否 | 否 |
| applicationdefinitions | 否 | 否 |
| 應用程式所需 | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| managedinstances | yes | yes |
| managedinstances/databases | yes | yes |
| servers | yes | yes |
| servers/databases | yes | yes |
| servers/elasticpools | yes | yes |
| virtualclusters | yes | yes |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| storageaccounts | yes | yes |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| storagesyncservices | yes | yes |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| managers | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| streamingjobs | yes | yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| environments | yes | yes |
| environments/eventsources | yes | yes |
| environments/referencedatasets | yes | yes |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| 帳戶 | yes | yes |
| account/extension | yes | yes |
| account/project | yes | yes |

## <a name="microsoftweb"></a>Microsoft.Web
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| certificates | 否 | yes |
| classicmobileservices | 否 | 否 |
| connectiongateways | yes | yes |
| connections | yes | yes |
| customapis | yes | yes |
| hostingenvironments | 否 | 否 |
| serverfarms | yes | yes |
| sites | yes | yes |
| sites/premieraddons | yes | yes |
| sites/slots | yes | yes |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | -------------- | ------------ |
| deviceservices | yes | yes |


## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。 這些資源提供者包括：

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>後續步驟

* 如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。
