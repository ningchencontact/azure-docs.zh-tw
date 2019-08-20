---
title: 依 Azure 資源類型區分的移動作業支援
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 73f4b6fe4714d21c12d2c7bd387cd30f6f711d5a
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624315"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援
此文章列出 Azure 資源類型是否支援移動作業。 它也會提供移動資源時要考慮之特殊條件的相關資訊。

跳至資源提供者命名空間:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| domainservices | 否 | 否 |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| tenants | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| actionrules | 是 | 是 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 伺服器 | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 服務 | 是 | 是 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| configurationstores | 是 | 是 |

## <a name="microsoftappservice"></a>Microsoft.AppService
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| apiapps | 否 | 否 |
| appidentities | 否 | 否 |
| gateways | 否 | 否 |

> [!IMPORTANT]
> 請參閱[App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| policyassignments | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| automationaccounts | 是 | 是 |
| automationaccounts/configurations | 是 | 是 |
| automationaccounts/runbooks | 是 | 是 |

> [!IMPORTANT]
> Runbook 必須存在於與自動化帳戶相同的資源群組中。

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| b2cdirectories | 是 | 是 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| registrations | 是 | 是 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| backupvault | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| batchaccounts | 是 | 是 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| clusters | 否 | 否 |
| fileservers | 否 | 否 |
| 工作 | 否 | 否 |
| 工作區 | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| mapapis | 否 | 否 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| biztalk | 是 | 是 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| blockchainmembers | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| blueprintassignments | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| botservices | 是 | 是 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| redis | 是 | 是 |

> [!IMPORTANT]
> 如果 Azure Cache for Redis 實例是設定為使用虛擬網路, 實例就無法移至不同的訂用帳戶。 請參閱[虛擬網路移動限制](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| profiles | 是 | 是 |
| profiles/endpoints | 是 | 是 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| certificateorders | 是 | 是 |

> [!IMPORTANT]
> 請參閱[App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| domainnames | 是 | 否 |
| virtualmachines | 是 | 否 |

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例特定的作業, 跨訂用帳戶移動。

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| networksecuritygroups | 否 | 否 |
| reservedips | 否 | 否 |
| virtualnetworks | 否 | 否 |

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例特定的作業, 跨訂用帳戶移動。

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| storageaccounts | 是 | 否 |

> [!IMPORTANT]
> 請參閱[傳統部署移動指引](./move-limitations/classic-model-move-limitations.md)。 傳統部署資源可以透過該案例特定的作業, 跨訂用帳戶移動。

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| availabilitysets | 是 | 是 |
| 磁碟 | 是 | 是 |
| galleries | 否 | 否 |
| galleries/images | 否 | 否 |
| galleries/images/versions | 否 | 否 |
| hostgroups | 否 | 否 |
| hostgroups/主機 | 否 | 否 |
| 映像 | 是 | 是 |
| proximityplacementgroups | 否 | 否 |
| restorepointcollections | 否 | 否 |
| sharedvmimages | 否 | 否 |
| sharedvmimages/versions | 否 | 否 |
| 快照集 | 是 | 是 |
| virtualmachines | 是 | 是 |
| virtualmachines/extensions | 是 | 是 |
| virtualmachinescalesets | 是 | 是 |

> [!IMPORTANT]
> 請參閱[虛擬機器移動指引](./move-limitations/virtual-machines-move-limitations.md)。

## <a name="microsoftcontainer"></a>Microsoft.Container
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| containergroups | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| registries | 是 | 是 |
| registries/buildtasks | 是 | 是 |
| registries/replications | 是 | 是 |
| registries/tasks | 是 | 是 |
| registries/webhooks | 是 | 是 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| containerservices | 否 | 否 |
| managedclusters | 否 | 否 |
| openshiftmanagedclusters | 否 | 否 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 應用程式 | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 否 | 否 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 連接器 | 是 | 是 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| hubs | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 工作 | 否 | 否 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| databoxedgedevices | 否 | 否 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 工作區 | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| catalogs | 是 | 是 |
| datacatalogs | 否 | 否 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| connectionmanagers | 否 | 否 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| packages | 否 | 否 |
| plans | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| datafactories | 是 | 是 |
| factories | 是 | 是 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| datalakeaccounts | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 服務 | 否 | 否 |
| services/projects | 否 | 否 |
| slots | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 伺服器 | 是 | 是 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 伺服器 | 是 | 是 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| servergroups | 否 | 否 |
| 伺服器 | 是 | 是 |
| serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| artifactsources | 是 | 是 |
| rollouts | 是 | 是 |
| servicetopologies | 是 | 是 |
| servicetopologies/services | 是 | 是 |
| servicetopologies/services/serviceunits | 是 | 是 |
| steps | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| elasticpools | 否 | 否 |
| elasticpools/iothubtenants | 否 | 否 |
| iothubs | 是 | 是 |
| provisioningservices | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| controllers | 否 | 否 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| labcenters | 否 | 否 |
| labs | 是 | 否 |
| 實驗室/環境 | 是 | 是 |
| labs/servicerunners | 是 | 是 |
| labs/virtualmachines | 是 | 否 |
| schedules | 是 | 是 |

## <a name="microsoftdns"></a>microsoft.dns
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
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
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| databaseaccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| domains | 是 | 是 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| domains | 是 | 是 |
| topics | 是 | 是 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |
| 命名空間 | 是 | 是 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| hanainstances | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |

> [!IMPORTANT]
> 您可以將 HDInsight 叢集移至新的訂用帳戶或資源群組。 不過，您無法跨訂用帳戶來移動連結至 HDInsight 叢集的網路資源 (例如虛擬網路、NIC 或負載平衡器)。 此外，您無法將已連結至叢集虛擬機器的 NIC 移至新的資源群組。
>
> 將 HDInsight 叢集移至新的訂用帳戶時，請先移動其他資源 (例如儲存體帳戶)。 然後，移動 HDInsight 叢集本身。

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 電腦 | 否 | 否 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| datamanagers | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 工作 | 是 | 是 |

## <a name="microsoftinsights"></a>microsoft.insights
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 否 | 否 |
| actiongroups | 是 | 是 |
| activitylogalerts | 否 | 否 |
| alertrules | 是 | 是 |
| autoscalesettings | 是 | 是 |
| components | 是 | 是 |
| guestdiagnosticsettings | 否 | 否 |
| metricalerts | 否 | 否 |
| notificationgroups | 否 | 否 |
| notificationrules | 否 | 否 |
| scheduledqueryrules | 是 | 是 |
| webtests | 是 | 是 |
| workbooks | 是 | 是 |

> [!IMPORTANT]
> 請確定移至新的訂用帳戶不會超過訂用帳戶[配額](../azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| iotapps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| checknameavailability | 是 | 是 |
| 圖表 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| hsmpools | 否 | 否 |
| vaults | 是 | 是 |

> [!IMPORTANT]
> 用於磁片加密的金鑰保存庫無法移至相同訂用帳戶中或跨訂用帳戶的資源群組。

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| clusters | 是 | 是 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| labaccounts | 否 | 否 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| hostingenvironments | 否 | 否 |
| integrationaccounts | 是 | 是 |
| integrationserviceenvironments | 否 | 否 |
| isolatedenvironments | 否 | 否 |
| workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| commitmentplans | 是 | 是 |
| webservices | 是 | 否 |
| 工作區 | 是 | 是 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| operationalizationclusters | 是 | 是 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 否 | 否 |
| accounts/workspaces | 否 | 否 |
| accounts/workspaces/projects | 否 | 否 |
| teamaccounts | 否 | 否 |
| teamaccounts/workspaces | 否 | 否 |
| teamaccounts/workspaces/projects | 否 | 否 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| hostingaccounts | 否 | 否 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 工作區 | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| userassignedidentities | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| classicdevservices | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| mediaservices | 是 | 是 |
| mediaservices/liveevents | 是 | 是 |
| mediaservices/streamingendpoints | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| assessmentprojects | 否 | 否 |
| migrateprojects | 否 | 否 |
| projects | 否 | 否 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| netappaccounts | 否 | 否 |
| netappaccounts/capacitypools | 否 | 否 |
| netappaccounts/capacitypools/volumes | 否 | 否 |
| netappaccounts/capacitypools/volumes/mounttargets | 否 | 否 |
| netappaccounts/capacitypools/volumes/snapshots | 否 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| applicationgateways | 否 | 否 |
| applicationgatewaywebapplicationfirewallpolicies | 否 | 否 |
| applicationsecuritygroups | 是 | 是 |
| azurefirewalls | 是 | 是 |
| bastionhosts | 否 | 否 |
| 連接 | 是 | 是 |
| ddoscustompolicies | 是 | 是 |
| ddosprotectionplans | 否 | 否 |
| dnszones | 是 | 是 |
| expressroutecircuits | 否 | 否 |
| expressroutecrossconnections | 否 | 否 |
| expressroutegateways | 否 | 否 |
| expressrouteports | 否 | 否 |
| 前門 | 否 | 否 |
| frontdoorwebapplicationfirewallpolicies | 否 | 否 |
| loadbalancers | 是-基本 SKU<br>無標準 SKU | 是-基本 SKU<br>無標準 SKU |
| localnetworkgateways | 是 | 是 |
| natgateways | 是 | 是 |
| networkintentpolicies | 是 | 是 |
| networkinterfaces | 是 | 是 |
| networkprofiles | 否 | 否 |
| networksecuritygroups | 是 | 是 |
| networkwatchers | 是 | 是 |
| networkwatchers/connectionmonitors | 是 | 是 |
| networkwatchers/lenses | 是 | 是 |
| networkwatchers/pingmeshes | 是 | 是 |
| p2svpngateways | 否 | 否 |
| privatednszones | 是 | 是 |
| privatednszones/virtualnetworklinks | 是 | 是 |
| privateendpoints | 否 | 否 |
| privatelinkservices | 否 | 否 |
| publicipaddresses | 是-基本 SKU<br>無標準 SKU | 是-基本 SKU<br>無標準 SKU |
| publicipprefixes | 是 | 是 |
| routefilters | 否 | 否 |
| routetables | 是 | 是 |
| securegateways | 是 | 是 |
| serviceendpointpolicies | 是 | 是 |
| trafficmanagerprofiles | 是 | 是 |
| virtualhubs | 否 | 否 |
| virtualnetworkgateways | 是 | 是 |
| virtualnetworks | 是 | 是 |
| virtualnetworktaps | 否 | 否 |
| virtualwans | 否 | 否 |
| vpngateways | 否 | 否 |
| vpnsites | 否 | 否 |
| webapplicationfirewallpolicies | 是 | 是 |

> [!IMPORTANT]
> 請參閱[網路移動指引](./move-limitations/networking-move-limitations.md)。

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 命名空間 | 是 | 是 |
| namespaces/notificationhubs | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 工作區 | 是 | 是 |

> [!IMPORTANT]
> 請確定移至新的訂用帳戶不會超過訂用帳戶[配額](../azure-subscription-service-limits.md#azure-monitor-limits)。

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| managementconfigurations | 是 | 是 |
| 解決方案 | 是 | 是 |
| 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 對等 | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| dashboards | 是 | 是 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| rootresources | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| workspacecollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| capacities | 是 | 是 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 否 | 否 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| vaults | 是 | 是 |

> [!IMPORTANT]
> 請參閱復原[服務移動指引](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftrelay"></a>Microsoft.Relay
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 命名空間 | 是 | 是 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 應用程式 | 是 | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 流量 | 是 | 是 |
| jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| searchservices | 是 | 是 |

> [!IMPORTANT]
> 您無法在單一作業中移動不同區域中的數個搜尋資源。 而是要在不同作業中移動它們。

## <a name="microsoftsecurity"></a>Microsoft.Security
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | 是 | 是 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| gateways | 否 | 否 |
| nodes | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 命名空間 | 是 | 是 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 應用程式 | 否 | 否 |
| clusters | 是 | 是 |
| containergroups | 否 | 否 |
| containergroupsets | 否 | 否 |
| edgeclusters | 否 | 否 |
| 網路 | 否 | 否 |
| secretstores | 否 | 否 |
| 磁碟區 | 否 | 否 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 應用程式 | 是 | 是 |
| containergroups | 否 | 否 |
| gateways | 是 | 是 |
| 網路 | 是 | 是 |
| 祕密 | 是 | 是 |
| 磁碟區 | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| signalr | 是 | 是 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| siterecoveryvault | 否 | 否 |

> [!IMPORTANT]
> 請參閱復原[服務移動指引](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)。

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| appliancedefinitions | 否 | 否 |
| appliances | 否 | 否 |
| applicationdefinitions | 否 | 否 |
| 應用程式 | 否 | 否 |
| jitrequests | 否 | 否 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| instancepools | 否 | 否 |
| managedinstances | 否 | 否 |
| managedinstances/databases | 否 | 否 |
| 伺服器 | 是 | 是 |
| servers/databases | 是 | 是 |
| servers/elasticpools | 是 | 是 |
| virtualclusters | 是 | 是 |

> [!IMPORTANT]
> 資料庫和伺服器必須位於相同的資源群組中。 當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。 此行為會套用至 Azure SQL Database 和 Azure SQL Data Warehouse 資料庫。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | 是 | 是 |
| sqlvirtualmachines | 是 | 是 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| dwvm | 否 | 否 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| storageaccounts | 是 | 是 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 緩衝區 | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 是 | 是 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| managers | 否 | 否 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| streamingjobs | 是 | 是 |

> [!IMPORTANT]
> 在執行中狀態時, 無法移動串流分析作業。

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| environments | 否 | 否 |
| environments/eventsources | 否 | 否 |
| 執行個體 | 否 | 否 |
| instances/environments | 否 | 否 |
| instances/environments/eventsources | 否 | 否 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| providerregistrations | 否 | 否 |
| 資源 | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| environments | 是 | 是 |
| environments/eventsources | 是 | 是 |
| environments/referencedatasets | 是 | 是 |

## <a name="microsofttoken"></a>Microsoft.Token
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 商店 | 否 | 否 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| imagetemplates | 否 | 否 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 帳戶 | 是 | 是 |
| account/extension | 是 | 是 |
| account/project | 是 | 是 |

> [!IMPORTANT]
> 若要變更 Azure DevOps 的訂用帳戶, 請參閱[變更用於計費的 Azure 訂用](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)帳戶。

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | 是 | 是 |
| dedicatedcloudservices | 是 | 是 |
| virtualmachines | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| 憑證 | 否 | 是 |
| connectiongateways | 是 | 是 |
| 連接 | 是 | 是 |
| customapis | 是 | 是 |
| hostingenvironments | 否 | 否 |
| serverfarms | 是 | 是 |
| 網站 | 是 | 是 |
| sites/premieraddons | 是 | 是 |
| sites/slots | 是 | 是 |

> [!IMPORTANT]
> 請參閱[App Service 移動指引](./move-limitations/app-service-move-limitations.md)。

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| deviceservices | 否 | 否 |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| 資源類型 | 資源群組 | 訂閱 |
| ------------- | ----------- | ---------- |
| applicationgroups | 否 | 否 |
| hostpools | 否 | 否 |
| 工作區 | 否 | 否 |

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟
如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。
