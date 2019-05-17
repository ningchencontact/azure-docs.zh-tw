---
title: 依 Azure 資源類型區分的移動作業支援
description: 列出可移至新資源群組或訂用帳戶的 Azure 資源類型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 5/16/2019
ms.author: tomfitz
ms.openlocfilehash: d662607eaaabb8ccfad89f625165c542af81b0fa
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794524"
---
# <a name="move-operation-support-for-resources"></a>資源的移動作業支援
此文章列出 Azure 資源類型是否支援移動作業。 雖然資源類型支援作業，但可能會有導致無法移動資源的情況。 如需有關影響移動作業之情況的詳細資料，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv)。

## <a name="microsoftaad"></a>Microsoft.AAD
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domainservices | 無 | 無 |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| tenants | 無 | 無 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| actionrules | 有 | 有 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 伺服器 | 有 | 有 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 有 | 有 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| configurationstores | 有 | 有 |

## <a name="microsoftappservice"></a>Microsoft.AppService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| apiapps | 無 | 無 |
| appidentities | 無 | 無 |
| gateways | 無 | 無 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| policyassignments | 無 | 無 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| automationaccounts | 有 | 有 |
| automationaccounts/configurations | 有 | 有 |
| automationaccounts/runbooks | 有 | 有 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| b2cdirectories | 有 | 有 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| registrations | 有 | 有 |

## <a name="microsoftbackup"></a>Microsoft.Backup
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| backupvault | 無 | 無 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| batchaccounts | 有 | 有 |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 無 | 無 |
| fileservers | 無 | 無 |
| 工作 | 無 | 無 |
| workspaces | 無 | 無 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| mapapis | 無 | 無 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| biztalk | 有 | 有 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| blockchainmembers | 有 | 有 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| blueprintassignments | 無 | 無 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| botservices | 有 | 有 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| redis | 有 | 有 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 設定檔 | 有 | 有 |
| profiles/endpoints | 有 | 有 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| certificateorders | 有 | 有 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domainnames | 有 | 無 |
| virtualmachines | 有 | 無 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| networksecuritygroups | 無 | 無 |
| reservedips | 無 | 無 |
| virtualnetworks | 無 | 無 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storageaccounts | 有 | 無 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| availabilitysets | 有 | 有 |
| 磁碟 | 有 | 有 |
| galleries | 無 | 無 |
| galleries/images | 無 | 無 |
| galleries/images/versions | 無 | 無 |
| hostgroups | 無 | 無 |
| hostgroups/主機 | 無 | 無 |
| 映像 | 有 | 有 |
| proximityplacementgroups | 無 | 無 |
| restorepointcollections | 無 | 無 |
| sharedvmimages | 無 | 無 |
| sharedvmimages/versions | 無 | 無 |
| 快照集 | 有 | 有 |
| virtualmachines | 有 | 有 |
| virtualmachines/extensions | 有 | 有 |
| virtualmachinescalesets | 有 | 有 |

## <a name="microsoftcontainer"></a>Microsoft.Container
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| containergroups | 無 | 無 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| containergroups | 無 | 無 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| registries | 有 | 有 |
| registries/buildtasks | 有 | 有 |
| registries/replications | 有 | 有 |
| registries/tasks | 有 | 有 |
| registries/webhooks | 有 | 有 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| containerservices | 無 | 無 |
| managedclusters | 無 | 無 |
| openshiftmanagedclusters | 無 | 無 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式 | 有 | 有 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 無 | 無 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 連接器 | 有 | 有 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hubs | 有 | 有 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 工作 | 無 | 無 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| databoxedgedevices | 無 | 無 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspaces | 無 | 無 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| catalogs | 有 | 有 |
| datacatalogs | 無 | 無 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| connectionmanagers | 無 | 無 |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| packages | 無 | 無 |
| plans | 無 | 無 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| datafactories | 有 | 有 |
| factories | 有 | 有 |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| datalakeaccounts | 無 | 無 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 無 | 無 |
| services/projects | 無 | 無 |
| slots | 無 | 無 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 伺服器 | 有 | 有 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 伺服器 | 有 | 有 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| servergroups | 無 | 無 |
| 伺服器 | 有 | 有 |
| serversv2 | 有 | 有 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| artifactsources | 有 | 有 |
| rollouts | 有 | 有 |
| servicetopologies | 有 | 有 |
| servicetopologies/services | 有 | 有 |
| servicetopologies/services/serviceunits | 有 | 有 |
| steps | 有 | 有 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| elasticpools | 無 | 無 |
| elasticpools/iothubtenants | 無 | 無 |
| iothubs | 有 | 有 |
| provisioningservices | 有 | 有 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| controllers | 無 | 無 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| labcenters | 無 | 無 |
| labs | 有 | 無 |
| 實驗室/環境 | 有 | 有 |
| labs/servicerunners | 有 | 有 |
| labs/virtualmachines | 有 | 無 |
| schedules | 有 | 有 |

## <a name="microsoftdns"></a>microsoft.dns
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dnszones | 無 | 無 |
| dnszones/a | 無 | 無 |
| dnszones/aaaa | 無 | 無 |
| dnszones/cname | 無 | 無 |
| dnszones/mx | 無 | 無 |
| dnszones/ptr | 無 | 無 |
| dnszones/srv | 無 | 無 |
| dnszones/txt | 無 | 無 |
| trafficmanagerprofiles | 無 | 無 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| databaseaccounts | 有 | 有 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domains | 有 | 有 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 有 | 有 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| domains | 有 | 有 |
| topics | 有 | 有 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 有 | 有 |
| 命名空間 | 有 | 有 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 無 | 無 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hanainstances | 有 | 有 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 有 | 有 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 服務 | 有 | 有 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 電腦 | 無 | 無 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| datamanagers | 有 | 有 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 工作 | 有 | 有 |

## <a name="microsoftinsights"></a>microsoft.insights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 無 | 無 |
| actiongroups | 有 | 有 |
| activitylogalerts | 無 | 無 |
| alertrules | 有 | 有 |
| autoscalesettings | 有 | 有 |
| components | 有 | 有 |
| guestdiagnosticsettings | 無 | 無 |
| metricalerts | 無 | 無 |
| notificationgroups | 無 | 無 |
| notificationrules | 無 | 無 |
| scheduledqueryrules | 無 | 無 |
| webtests | 有 | 有 |
| workbooks | 有 | 有 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| iotapps | 有 | 有 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| checknameavailability | 有 | 有 |
| 圖表 | 有 | 有 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hsmpools | 無 | 無 |
| vaults | 有 | 有 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| clusters | 有 | 有 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| labaccounts | 有 | 有 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 無 | 無 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hostingenvironments | 無 | 無 |
| integrationaccounts | 有 | 有 |
| integrationserviceenvironments | 無 | 無 |
| isolatedenvironments | 無 | 無 |
| workflows | 有 | 有 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| commitmentplans | 有 | 有 |
| webservices | 有 | 無 |
| workspaces | 有 | 有 |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| operationalizationclusters | 有 | 有 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 無 | 無 |
| accounts/workspaces | 無 | 無 |
| accounts/workspaces/projects | 無 | 無 |
| teamaccounts | 無 | 無 |
| teamaccounts/workspaces | 無 | 無 |
| teamaccounts/workspaces/projects | 無 | 無 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| hostingaccounts | 無 | 無 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspaces | 無 | 無 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| userassignedidentities | 無 | 無 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| classicdevservices | 無 | 無 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| mediaservices | 有 | 有 |
| mediaservices/liveevents | 有 | 有 |
| mediaservices/streamingendpoints | 有 | 有 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| assessmentprojects | 無 | 無 |
| migrateprojects | 無 | 無 |
| projects | 無 | 無 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| netappaccounts | 無 | 無 |
| netappaccounts/capacitypools | 無 | 無 |
| netappaccounts/capacitypools/volumes | 無 | 無 |
| netappaccounts/capacitypools/volumes/mounttargets | 無 | 無 |
| netappaccounts/capacitypools/volumes/snapshots | 無 | 無 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| applicationgateways | 無 | 無 |
| applicationgatewaywebapplicationfirewallpolicies | 無 | 無 |
| applicationsecuritygroups | 有 | 有 |
| azurefirewalls | 有 | 有 |
| bastionhosts | 無 | 無 |
| 連接 | 有 | 有 |
| ddoscustompolicies | 有 | 有 |
| ddosprotectionplans | 無 | 無 |
| dnszones | 有 | 有 |
| expressroutecircuits | 無 | 無 |
| expressroutecrossconnections | 無 | 無 |
| expressroutegateways | 無 | 無 |
| expressrouteports | 無 | 無 |
| 前門 | 有 | 有 |
| frontdoorwebapplicationfirewallpolicies | 有 | 有 |
| loadbalancers | 有 | 有 |
| localnetworkgateways | 有 | 有 |
| natgateways | 有 | 有 |
| networkintentpolicies | 有 | 有 |
| networkinterfaces | 有 | 有 |
| networkprofiles | 無 | 無 |
| networksecuritygroups | 有 | 有 |
| networkwatchers | 有 | 有 |
| networkwatchers/connectionmonitors | 有 | 有 |
| networkwatchers/lenses | 有 | 有 |
| networkwatchers/pingmeshes | 有 | 有 |
| p2svpngateways | 無 | 無 |
| privatednszones | 有 | 有 |
| privatednszones/virtualnetworklinks | 有 | 有 |
| privateendpoints | 無 | 無 |
| privatelinkservices | 無 | 無 |
| publicipaddresses | 有 | 有 |
| publicipprefixes | 有 | 有 |
| routefilters | 無 | 無 |
| routetables | 有 | 有 |
| securegateways | 有 | 有 |
| serviceendpointpolicies | 有 | 有 |
| trafficmanagerprofiles | 有 | 有 |
| virtualhubs | 無 | 無 |
| virtualnetworkgateways | 有 | 有 |
| virtualnetworks | 有 | 有 |
| virtualnetworktaps | 無 | 無 |
| virtualwans | 無 | 無 |
| vpngateways | 無 | 無 |
| vpnsites | 無 | 無 |
| webapplicationfirewallpolicies | 有 | 有 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 命名空間 | 有 | 有 |
| namespaces/notificationhubs | 有 | 有 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspaces | 有 | 有 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| managementconfigurations | 有 | 有 |
| solutions | 有 | 有 |
| 檢視 | 有 | 有 |

## <a name="microsoftpeering"></a>Microsoft.Peering
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 對等 | 無 | 無 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dashboards | 有 | 有 |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| rootresources | 無 | 無 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| workspacecollections | 有 | 有 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| capacities | 有 | 有 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 無 | 無 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| vaults | 有 | 有 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 命名空間 | 有 | 有 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式 | 有 | 無 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 流量 | 有 | 有 |
| jobcollections | 有 | 有 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| searchservices | 有 | 有 |

## <a name="microsoftsecurity"></a>Microsoft.Security
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | 有 | 有 |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| gateways | 無 | 無 |
| nodes | 無 | 無 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 命名空間 | 有 | 有 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式 | 無 | 無 |
| clusters | 有 | 有 |
| containergroups | 無 | 無 |
| containergroupsets | 無 | 無 |
| edgeclusters | 無 | 無 |
| 網路 | 無 | 無 |
| secretstores | 無 | 無 |
| 磁碟區 | 無 | 無 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 應用程式 | 有 | 有 |
| containergroups | 無 | 無 |
| gateways | 有 | 有 |
| 網路 | 有 | 有 |
| 祕密 | 有 | 有 |
| 磁碟區 | 有 | 有 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| signalr | 有 | 有 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| siterecoveryvault | 無 | 無 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| appliancedefinitions | 無 | 無 |
| appliances | 無 | 無 |
| applicationdefinitions | 無 | 無 |
| 應用程式 | 無 | 無 |
| jitrequests | 無 | 無 |

## <a name="microsoftsql"></a>Microsoft.Sql
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| instancepools | 有 | 有 |
| managedinstances | 有 | 有 |
| managedinstances/databases | 有 | 有 |
| 伺服器 | 有 | 有 |
| servers/databases | 有 | 有 |
| servers/elasticpools | 有 | 有 |
| virtualclusters | 有 | 有 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | 有 | 有 |
| sqlvirtualmachines | 有 | 有 |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dwvm | 無 | 無 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storageaccounts | 有 | 有 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 快取 | 無 | 無 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 有 | 有 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 無 | 無 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| storagesyncservices | 無 | 無 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| managers | 無 | 無 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| streamingjobs | 有 | 有 |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| environments | 無 | 無 |
| environments/eventsources | 無 | 無 |
| 執行個體 | 無 | 無 |
| instances/environments | 無 | 無 |
| instances/environments/eventsources | 無 | 無 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| providerregistrations | 無 | 無 |
| 資源 | 無 | 無 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| environments | 有 | 有 |
| environments/eventsources | 有 | 有 |
| environments/referencedatasets | 有 | 有 |

## <a name="microsofttoken"></a>Microsoft.Token
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 存放區 | 無 | 無 |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| imagetemplates | 無 | 無 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 帳戶 | 有 | 有 |
| account/extension | 有 | 有 |
| account/project | 有 | 有 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | 有 | 有 |
| dedicatedcloudservices | 有 | 有 |
| virtualmachines | 有 | 有 |

## <a name="microsoftweb"></a>Microsoft.Web
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| 憑證 | 無 | 有 |
| connectiongateways | 有 | 有 |
| 連接 | 有 | 有 |
| customapis | 有 | 有 |
| hostingenvironments | 無 | 無 |
| serverfarms | 有 | 有 |
| 網站 | 有 | 有 |
| sites/premieraddons | 有 | 有 |
| sites/slots | 有 | 有 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| deviceservices | 有 | 有 |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| 資源類型 | 資源群組 | 訂用帳戶 |
| ------------- | ----------- | ---------- |
| applicationgroups | 無 | 無 |
| hostpools | 無 | 無 |
| workspaces | 無 | 無 |

## <a name="third-party-services"></a>協力廠商服務

協力廠商服務目前不支援移動作業。

## <a name="next-steps"></a>後續步驟
如需用來移動資源的命令，請參閱[將資源移動到新的資源群組或訂用帳戶](resource-group-move-resources.md)。
