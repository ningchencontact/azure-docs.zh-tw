---
title: Tag support for resources
description: 顯示哪些 Azure 資源類型支援標記。 提供所有 Azure 服務的詳細資料。
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 5b3e26d914887496eedde609404eaf0b380dbcc0
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422132"
---
# <a name="tag-support-for-azure-resources"></a>Azure 資源的標記支援
本文將說明資源類型是否支援[標記](resource-group-using-tags.md)。 The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report. You can view costs by tags in the [Cost Management cost analysis](../cost-management/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) and the [Azure billing invoice and daily usage data](../billing/billing-download-azure-invoice-daily-usage-date.md).

若要以逗號區隔值檔案的形式取得相同資料，請下載 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | 是 | 是 |
> | DomainServices / oucontainer | 否 | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | 否 | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | 否 | 否 |
> | addsservices | 否 | 否 |
> | agents | 否 | 否 |
> | anonymousapiusers | 否 | 否 |
> | 組態 | 否 | 否 |
> | 記錄 | 否 | 否 |
> | reports | 否 | 否 |
> | servicehealthmetrics | 否 | 否 |
> | 服務 | 否 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 組態 | 否 | 否 |
> | generateRecommendations | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | Mahout | 否 | 否 |
> | suppressions | 否 | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | 是 | 是 |
> | alerts | 否 | 否 |
> | alertsList | 否 | 否 |
> | alertsMetaData | 否 | 否 |
> | alertsSummary | 否 | 否 |
> | alertsSummaryList | 否 | 否 |
> | feedback | 否 | 否 |
> | smartDetectorAlertRules | 是 | 是 |
> | smartDetectorRuntimeEnvironments | 否 | 否 |
> | smartGroups | 否 | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 伺服器 | 是 | 是 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | 否 | 否 |
> | service | 是 | 是 |
> | validateServiceName | 否 | 否 |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores / eventGridFilters | 否 | 否 |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | 是 | 是 |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | 否 | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | 否 | 否 |
> | dataAliases | 否 | 否 |
> | denyAssignments | 否 | 否 |
> | elevateAccess | 否 | 否 |
> | findOrphanRoleAssignments | 否 | 否 |
> | locks | 否 | 否 |
> | 權限 | 否 | 否 |
> | policyAssignments | 否 | 否 |
> | policyDefinitions | 否 | 否 |
> | policySetDefinitions | 否 | 否 |
> | providerOperations | 否 | 否 |
> | roleAssignments | 否 | 否 |
> | roleDefinitions | 否 | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | 是 | 是 |
> | automationAccounts / configurations | 是 | 是 |
> | automationAccounts / jobs | 否 | 否 |
> | automationAccounts / runbooks | 是 | 是 |
> | automationAccounts / softwareUpdateConfigurations | 否 | 否 |
> | automationAccounts / webhooks | 否 | 否 |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | 是 | 是 |
> | configurationStores / eventGridFilters | 否 | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | 否 | 否 |
> | environments / accounts | 否 | 否 |
> | environments / accounts / namespaces | 否 | 否 |
> | environments / accounts / namespaces / configurations | 否 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | 是 | 否 |
> | b2ctenants | 否 | 否 |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | 是 | 是 |
> | postgresInstances | 是 | 是 |
> | sqlBigDataClusters | 是 | 是 |
> | sqlInstances | 是 | 是 |
> | sqlServerRegistrations | 是 | 是 |
> | sqlServerRegistrations / sqlServers | 否 | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | 是 | 是 |
> | registrations / customerSubscriptions | 否 | 否 |
> | registrations / products | 否 | 否 |
> | verificationKeys | 否 | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | 是 | 是 |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | 否 | 否 |
> | billingAccounts / agreements | 否 | 否 |
> | billingAccounts / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles | 否 | 否 |
> | billingAccounts / billingProfiles / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingProfiles / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / billingProfiles / customers | 否 | 否 |
> | billingAccounts / billingProfiles / invoices | 否 | 否 |
> | billingAccounts / billingProfiles / invoices / pricesheet | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / transactions | 否 | 否 |
> | billingAccounts / billingProfiles / invoiceSections / transfers | 否 | 否 |
> | billingAccounts / BillingProfiles / patchOperations | 否 | 否 |
> | billingAccounts / billingProfiles / paymentMethods | 否 | 否 |
> | billingAccounts / billingProfiles / policies | 否 | 否 |
> | billingAccounts / billingProfiles / pricesheet | 否 | 否 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 否 | 否 |
> | billingAccounts / billingProfiles / products | 否 | 否 |
> | billingAccounts / billingProfiles / transactions | 否 | 否 |
> | billingAccounts / billingRoleAssignments | 否 | 否 |
> | billingAccounts / billingRoleDefinitions | 否 | 否 |
> | billingAccounts / billingSubscriptions | 否 | 否 |
> | billingAccounts / billingSubscriptions / invoices | 否 | 否 |
> | billingAccounts / createBillingRoleAssignment | 否 | 否 |
> | billingAccounts / createInvoiceSectionOperations | 否 | 否 |
> | billingAccounts / customers | 否 | 否 |
> | billingAccounts / customers / billingPermissions | 否 | 否 |
> | billingAccounts / customers / billingSubscriptions | 否 | 否 |
> | billingAccounts / customers / initiateTransfer | 否 | 否 |
> | billingAccounts / customers / policies | 否 | 否 |
> | billingAccounts / customers / products | 否 | 否 |
> | billingAccounts / customers / transactions | 否 | 否 |
> | billingAccounts / customers / transfers | 否 | 否 |
> | billingAccounts / departments | 否 | 否 |
> | billingAccounts / enrollmentAccounts | 否 | 否 |
> | billingAccounts / invoices | 否 | 否 |
> | billingAccounts / invoiceSections | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions | 否 | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | 否 | 否 |
> | billingAccounts / invoiceSections / elevate | 否 | 否 |
> | billingAccounts / invoiceSections / initiateTransfer | 否 | 否 |
> | billingAccounts / invoiceSections / patchOperations | 否 | 否 |
> | billingAccounts / invoiceSections / productMoveOperations | 否 | 否 |
> | billingAccounts / invoiceSections / products | 否 | 否 |
> | billingAccounts / invoiceSections / products / transfer | 否 | 否 |
> | billingAccounts / invoiceSections / products / updateAutoRenew | 否 | 否 |
> | billingAccounts / invoiceSections / transactions | 否 | 否 |
> | billingAccounts / invoiceSections / transfers | 否 | 否 |
> | billingAccounts / lineOfCredit | 否 | 否 |
> | billingAccounts / patchOperations | 否 | 否 |
> | billingAccounts / paymentMethods | 否 | 否 |
> | billingAccounts / products | 否 | 否 |
> | billingAccounts / transactions | 否 | 否 |
> | billingPeriods | 否 | 否 |
> | billingPermissions | 否 | 否 |
> | billingProperty | 否 | 否 |
> | billingRoleAssignments | 否 | 否 |
> | billingRoleDefinitions | 否 | 否 |
> | createBillingRoleAssignment | 否 | 否 |
> | departments | 否 | 否 |
> | enrollmentAccounts | 否 | 否 |
> | invoices | 否 | 否 |
> | transfers | 否 | 否 |
> | transfers / acceptTransfer | 否 | 否 |
> | transfers / declineTransfer | 否 | 否 |
> | transfers / operationStatus | 否 | 否 |
> | transfers / validateTransfer | 否 | 否 |
> | validateAddress | 否 | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | 是 | 是 |
> | cordaMembers | 是 | 是 |
> | 位監看員 | 是 | 是 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | 否 | 否 |
> | blueprintAssignments / assignmentOperations | 否 | 否 |
> | blueprintAssignments / operations | 否 | 否 |
> | blueprints | 否 | 否 |
> | blueprints / artifacts | 否 | 否 |
> | blueprints / versions | 否 | 否 |
> | blueprints / versions / artifacts | 否 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | 是 | 是 |
> | botServices / channels | 否 | 否 |
> | botServices / connections | 否 | 否 |
> | 語言 | 否 | 否 |
> | 範本 | 否 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | 是 | 是 |
> | RedisConfigDefinition | 否 | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | 否 | 否 |
> | calculateExchange | 否 | 否 |
> | calculatePrice | 否 | 否 |
> | calculatePurchasePrice | 否 | 否 |
> | catalogs | 否 | 否 |
> | commercialReservationOrders | 否 | 否 |
> | exchange | 否 | 否 |
> | placePurchaseOrder | 否 | 否 |
> | reservationOrders | 否 | 否 |
> | reservationOrders / calculateRefund | 否 | 否 |
> | reservationOrders / merge | 否 | 否 |
> | reservationOrders / reservations | 否 | 否 |
> | reservationOrders / reservations / revisions | 否 | 否 |
> | reservationOrders / return | 否 | 否 |
> | reservationOrders / split | 否 | 否 |
> | reservationOrders / swap | 否 | 否 |
> | reservations | 否 | 否 |
> | resources | 否 | 否 |
> | validateReservationOrder | 否 | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 | 否 |
> | CdnWebApplicationFirewallPolicies | 是 | 是 |
> | edgenodes | 否 | 否 |
> | 設定檔 | 是 | 是 |
> | profiles / endpoints | 是 | 是 |
> | profiles / endpoints / customdomains | 否 | 否 |
> | profiles / endpoints / origins | 否 | 否 |
> | validateProbe | 否 | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | 是 | 是 |
> | certificateOrders / certificates | 否 | 否 |
> | validateCertificateRegistrationInformation | 否 | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | domainNames | 否 | 否 |
> | domainNames / capabilities | 否 | 否 |
> | domainNames / internalLoadBalancers | 否 | 否 |
> | domainNames / serviceCertificates | 否 | 否 |
> | domainNames / slots | 否 | 否 |
> | domainNames / slots / roles | 否 | 否 |
> | domainNames / slots / roles / metricDefinitions | 否 | 否 |
> | domainNames / slots / roles / metrics | 否 | 否 |
> | moveSubscriptionResources | 否 | 否 |
> | operatingSystemFamilies | 否 | 否 |
> | operatingSystems | 否 | 否 |
> | quotas | 否 | 否 |
> | resourceTypes | 否 | 否 |
> | validateSubscriptionMoveAvailability | 否 | 否 |
> | virtualMachines | 否 | 否 |
> | virtualMachines / diagnosticSettings | 否 | 否 |
> | virtualMachines / metricDefinitions | 否 | 否 |
> | virtualMachines / metrics | 否 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | 否 | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | expressRouteCrossConnections | 否 | 否 |
> | expressRouteCrossConnections / peerings | 否 | 否 |
> | gatewaySupportedDevices | 否 | 否 |
> | networkSecurityGroups | 否 | 否 |
> | quotas | 否 | 否 |
> | reservedIps | 否 | 否 |
> | virtualNetworks | 否 | 否 |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | 否 | 否 |
> | virtualNetworks / virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | 否 | 否 |
> | 磁碟 | 否 | 否 |
> | images | 否 | 否 |
> | osImages | 否 | 否 |
> | osPlatformImages | 否 | 否 |
> | publicImages | 否 | 否 |
> | quotas | 否 | 否 |
> | storageAccounts | 否 | 否 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts / metricDefinitions | 否 | 否 |
> | storageAccounts / metrics | 否 | 否 |
> | storageAccounts / queueServices | 否 | 否 |
> | storageAccounts / services | 否 | 否 |
> | storageAccounts / services / diagnosticSettings | 否 | 否 |
> | storageAccounts / services / metricDefinitions | 否 | 否 |
> | storageAccounts / services / metrics | 否 | 否 |
> | storageAccounts / tableServices | 否 | 否 |
> | storageAccounts / vmImages | 否 | 否 |
> | vmImages | 否 | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | 否 | 否 |
> | UsageAggregates | 否 | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | 是 | 是 |
> | diskEncryptionSets | 是 | 是 |
> | 磁碟 | 是 | 是 |
> | galleries | 是 | 是 |
> | galleries / applications | 否 | 否 |
> | galleries / applications / versions | 否 | 否 |
> | galleries / images | 否 | 否 |
> | galleries / images / versions | 否 | 否 |
> | hostGroups | 是 | 是 |
> | hostGroups / hosts | 是 | 是 |
> | images | 是 | 是 |
> | proximityPlacementGroups | 是 | 是 |
> | restorePointCollections | 是 | 是 |
> | restorePointCollections / restorePoints | 否 | 否 |
> | sharedVMImages | 是 | 是 |
> | sharedVMImages / versions | 否 | 否 |
> | snapshots | 是 | 是 |
> | virtualMachines | 是 | 是 |
> | virtualMachines / extensions | 是 | 是 |
> | virtualMachines / metricDefinitions | 否 | 否 |
> | virtualMachineScaleSets | 是 | 是 |
> | virtualMachineScaleSets / extensions | 否 | 否 |
> | virtualMachineScaleSets / networkInterfaces | 否 | 否 |
> | virtualMachineScaleSets / publicIPAddresses | 否 | 否 |
> | virtualMachineScaleSets / virtualMachines | 否 | 否 |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | 否 | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | 否 | 否 |
> | 餘額 | 否 | 否 |
> | 預算 | 否 | 否 |
> | Charges | 否 | 否 |
> | CostTags | 否 | 否 |
> | credits | 否 | 否 |
> | 活動 | 否 | 否 |
> | 預測 | 否 | 否 |
> | lots | 否 | 否 |
> | Marketplace | 否 | 否 |
> | Pricesheets | 否 | 否 |
> | products | 否 | 否 |
> | ReservationDetails | 否 | 否 |
> | ReservationRecommendations | 否 | 否 |
> | ReservationSummaries | 否 | 否 |
> | ReservationTransactions | 否 | 否 |
> | Tags | 否 | 否 |
> | tenants | 否 | 否 |
> | 詞彙 | 否 | 否 |
> | UsageDetails | 否 | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | 是 | 是 |
> | serviceAssociationLinks | 否 | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | 是 | 是 |
> | registries / builds | 否 | 否 |
> | registries / builds / cancel | 否 | 否 |
> | registries / builds / getLogLink | 否 | 否 |
> | registries / buildTasks | 是 | 是 |
> | registries / buildTasks / steps | 否 | 否 |
> | registries / eventGridFilters | 否 | 否 |
> | registries / generateCredentials | 否 | 否 |
> | registries / getBuildSourceUploadUrl | 否 | 否 |
> | registries / GetCredentials | 否 | 否 |
> | registries / importImage | 否 | 否 |
> | registries / queueBuild | 否 | 否 |
> | registries / regenerateCredential | 否 | 否 |
> | registries / regenerateCredentials | 否 | 否 |
> | registries / replications | 是 | 是 |
> | registries / runs | 否 | 否 |
> | registries / runs / cancel | 否 | 否 |
> | registries / scheduleRun | 否 | 否 |
> | registries / scopeMaps | 否 | 否 |
> | registries / taskRuns | 是 | 是 |
> | registries / tasks | 是 | 是 |
> | registries / tokens | 否 | 否 |
> | registries / updatePolicies | 否 | 否 |
> | registries / webhooks | 是 | 是 |
> | registries / webhooks / getCallbackConfig | 否 | 否 |
> | registries / webhooks / ping | 否 | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | 是 | 是 |
> | managedClusters | 是 | 是 |
> | openShiftManagedClusters | 是 | 是 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 警示 | 否 | 否 |
> | BillingAccounts | 否 | 否 |
> | 預算 | 否 | 否 |
> | CloudConnectors | 否 | 否 |
> | 連接器 | 是 | 是 |
> | 部門 | 否 | 否 |
> | 維度 | 否 | 否 |
> | EnrollmentAccounts | 否 | 否 |
> | 匯出 | 否 | 否 |
> | ExternalBillingAccounts | 否 | 否 |
> | ExternalBillingAccounts / Alerts | 否 | 否 |
> | ExternalBillingAccounts / Dimensions | 否 | 否 |
> | ExternalBillingAccounts / Forecast | 否 | 否 |
> | ExternalBillingAccounts / Query | 否 | 否 |
> | ExternalSubscriptions | 否 | 否 |
> | ExternalSubscriptions / Alerts | 否 | 否 |
> | ExternalSubscriptions / Dimensions | 否 | 否 |
> | ExternalSubscriptions / Forecast | 否 | 否 |
> | ExternalSubscriptions / Query | 否 | 否 |
> | Forecast | 否 | 否 |
> | 查詢 | 否 | 否 |
> | 註冊 | 否 | 否 |
> | Reportconfigs | 否 | 否 |
> | 報告 | 否 | 否 |
> | 設定 | 否 | 否 |
> | showbackRules | 否 | 否 |
> | 檢視 | 否 | 否 |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | 否 | 否 |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | 否 | 否 |
> | resourceProviders | 是 | 是 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | 是 | 是 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 否 |
> | workspaces / virtualNetworkPeerings | 否 | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | 是 | 是 |
> | datacatalogs | 是 | 是 |
> | datacatalogs / datasources | 否 | 否 |
> | datacatalogs / datasources / scans | 否 | 否 |
> | datacatalogs / datasources / scans / datasets | 否 | 否 |
> | datacatalogs / datasources / scans / triggers | 否 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | 是 | 否 |
> | dataFactories / diagnosticSettings | 否 | 否 |
> | dataFactories / metricDefinitions | 否 | 否 |
> | dataFactorySchema | 否 | 否 |
> | factories | 是 | 否 |
> | factories / integrationRuntimes | 否 | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / dataLakeStoreAccounts | 否 | 否 |
> | accounts / storageAccounts | 否 | 否 |
> | accounts / storageAccounts / containers | 否 | 否 |
> | accounts / transferAnalyticsUnits | 否 | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / eventGridFilters | 否 | 否 |
> | accounts / firewallRules | 否 | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 服務 | 否 | 否 |
> | services / projects | 否 | 否 |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / shares | 否 | 否 |
> | accounts / shares / datasets | 否 | 否 |
> | accounts / shares / invitations | 否 | 否 |
> | accounts / shares / providersharesubscriptions | 否 | 否 |
> | accounts / shares / synchronizationSettings | 否 | 否 |
> | accounts / sharesubscriptions | 否 | 否 |
> | accounts / sharesubscriptions / consumerSourceDataSets | 否 | 否 |
> | accounts / sharesubscriptions / datasetmappings | 否 | 否 |
> | accounts / sharesubscriptions / triggers | 否 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 伺服器 | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 伺服器 | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | 是 | 是 |
> | 伺服器 | 是 | 是 |
> | servers / advisors | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / privateEndpointConnectionProxies | 否 | 否 |
> | servers / privateEndpointConnections | 否 | 否 |
> | servers / privateLinkResources | 否 | 否 |
> | servers / queryTexts | 否 | 否 |
> | servers / recoverableServers | 否 | 否 |
> | servers / topQueryStatistics | 否 | 否 |
> | servers / virtualNetworkRules | 否 | 否 |
> | servers / waitStatistics | 否 | 否 |
> | serversv2 | 是 | 是 |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | 是 | 是 |
> | rollouts | 是 | 是 |
> | serviceTopologies | 是 | 是 |
> | serviceTopologies / services | 是 | 是 |
> | serviceTopologies / services / serviceUnits | 是 | 是 |
> | steps | 是 | 是 |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | 是 | 是 |
> | applicationgroups / applications | 否 | 否 |
> | applicationgroups / desktops | 否 | 否 |
> | applicationgroups / startmenuitems | 否 | 否 |
> | hostpools | 是 | 是 |
> | hostpools / sessionhosts | 否 | 否 |
> | hostpools / sessionhosts / usersessions | 否 | 否 |
> | hostpools / usersessions | 否 | 否 |
> | workspaces | 是 | 是 |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | 是 | 是 |
> | ElasticPools / IotHubTenants | 是 | 是 |
> | IotHubs | 是 | 是 |
> | IotHubs / eventGridFilters | 否 | 否 |
> | ProvisioningServices | 是 | 是 |
> | usages | 否 | 否 |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | 是 | 是 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | 是 | 是 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | 是 | 是 |
> | labs | 是 | 是 |
> | labs / environments | 是 | 是 |
> | labs / serviceRunners | 是 | 是 |
> | labs / virtualMachines | 是 | 是 |
> | schedules | 是 | 是 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | 否 | 否 |
> | databaseAccounts | 是 | 是 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains / domainOwnershipIdentifiers | 否 | 否 |
> | generateSsoRequest | 否 | 否 |
> | topLevelDomains | 否 | 否 |
> | validateDomainRegistrationInformation | 否 | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | 否 | 否 |
> | lcsprojects / clouddeployments | 否 | 否 |
> | lcsprojects / connectors | 否 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 服務 | 是 | 是 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | 是 | 是 |
> | domains / topics | 否 | 否 |
> | eventSubscriptions | 否 | 否 |
> | extensionTopics | 否 | 否 |
> | topics | 是 | 是 |
> | topicTypes | 否 | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventhubs | 否 | 否 |
> | namespaces / eventhubs / authorizationrules | 否 | 否 |
> | namespaces / eventhubs / consumergroups | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 功能 | 否 | 否 |
> | 提供者 | 否 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | 否 | 否 |
> | galleryitems | 否 | 否 |
> | generateartifactaccessuri | 否 | 否 |
> | myareas | 否 | 否 |
> | myareas / areas | 否 | 否 |
> | myareas / areas / areas | 否 | 否 |
> | myareas / areas / areas / galleryitems | 否 | 否 |
> | myareas / areas / galleryitems | 否 | 否 |
> | myareas / galleryitems | 否 | 否 |
> | 註冊 | 否 | 否 |
> | resources | 否 | 否 |
> | retrieveresourcesbyid | 否 | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | 否 | 否 |
> | guestConfigurationAssignments | 否 | 否 |
> | software | 否 | 否 |
> | softwareUpdateProfile | 否 | 否 |
> | softwareUpdates | 否 | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | 是 | 是 |
> | sapMonitors | 是 | 是 |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | 是 | 是 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters / applications | 否 | 否 |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 服務 | 是 | 是 |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | 是 | 是 |
> | machines / extensions | 是 | 是 |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | 是 | 是 |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | 是 | 是 |
> | networkScopes | 是 | 是 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | 是 | 是 |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | 否 | 否 |
> | IoTApps | 是 | 是 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 圖表 | 是 | 是 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | 否 | 否 |
> | hsmPools | 是 | 是 |
> | vaults | 是 | 是 |
> | vaults / accessPolicies | 否 | 否 |
> | vaults / eventGridFilters | 否 | 否 |
> | vaults / secrets | 否 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | clusters / attacheddatabaseconfigurations | 否 | 否 |
> | clusters / databases | 否 | 否 |
> | clusters / databases / dataconnections | 否 | 否 |
> | clusters / databases / eventhubconnections | 否 | 否 |
> | clusters / sharedidentities | 否 | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | 是 | 是 |
> | 使用者人數 | 否 | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | 是 | 是 |
> | integrationAccounts | 是 | 是 |
> | integrationServiceEnvironments | 是 | 是 |
> | integrationServiceEnvironments / managedApis | 是 | 是 |
> | isolatedEnvironments | 是 | 是 |
> | workflows | 是 | 是 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | 是 | 是 |
> | webServices | 是 | 是 |
> | 工作區 | 是 | 是 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | 是 | 是 |
> | workspaces / computes | 否 | 否 |
> | workspaces / eventGridFilters | 否 | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | 否 | 否 |
> | userAssignedIdentities | 是 | 是 |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 | 否 |
> | registrationAssignments | 否 | 否 |
> | registrationDefinitions | 否 | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | 否 | 否 |
> | managementGroups | 否 | 否 |
> | resources | 否 | 否 |
> | startTenantBackfill | 否 | 否 |
> | tenantBackfillStatus | 否 | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 是 | 是 |
> | accounts / eventGridFilters | 否 | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | 否 | 否 |
> | offerTypes | 否 | 否 |
> | offerTypes / publishers | 否 | 否 |
> | offerTypes / publishers / offers | 否 | 否 |
> | offerTypes / publishers / offers / plans | 否 | 否 |
> | offerTypes / publishers / offers / plans / agreements | 否 | 否 |
> | offerTypes / publishers / offers / plans / configs | 否 | 否 |
> | offerTypes / publishers / offers / plans / configs / importImage | 否 | 否 |
> | privategalleryitems | 否 | 否 |
> | products | 否 | 否 |
> | publishers | 否 | 否 |
> | publishers / offers | 否 | 否 |
> | publishers / offers / amendments | 否 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | 是 | 是 |
> | updateCommunicationPreference | 否 | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | 否 | 否 |
> | offertypes | 否 | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | 是 | 是 |
> | mediaservices / accountFilters | 否 | 否 |
> | mediaservices / assets | 否 | 否 |
> | mediaservices / assets / assetFilters | 否 | 否 |
> | mediaservices / contentKeyPolicies | 否 | 否 |
> | mediaservices / eventGridFilters | 否 | 否 |
> | mediaservices / liveEventOperations | 否 | 否 |
> | mediaservices / liveEvents | 是 | 是 |
> | mediaservices / liveEvents / liveOutputs | 否 | 否 |
> | mediaservices / liveOutputOperations | 否 | 否 |
> | mediaservices / mediaGraphs | 否 | 否 |
> | mediaservices / streamingEndpointOperations | 否 | 否 |
> | mediaservices / streamingEndpoints | 是 | 是 |
> | mediaservices / streamingLocators | 否 | 否 |
> | mediaservices / streamingPolicies | 否 | 否 |
> | mediaservices / transforms | 否 | 否 |
> | mediaservices / transforms / jobs | 否 | 否 |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | 是 | 是 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | 是 | 是 |
> | migrateprojects | 是 | 是 |
> | projects | 是 | 是 |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | 是 | 是 |
> | objectUnderstandingAccounts | 是 | 是 |
> | remoteRenderingAccounts | 是 | 是 |
> | spatialAnchorsAccounts | 是 | 是 |
> | surfaceReconstructionAccounts | 是 | 是 |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | 是 | 否 |
> | netAppAccounts / capacityPools | 是 | 否 |
> | netAppAccounts / capacityPools / volumes | 是 | 否 |
> | netAppAccounts / capacityPools / volumes / mountTargets | 是 | 否 |
> | netAppAccounts / capacityPools / volumes / snapshots | 是 | 否 |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | 是 | 是 |
> | applicationGatewayWebApplicationFirewallPolicies | 是 | 是 |
> | applicationSecurityGroups | 是 | 是 |
> | azureFirewallFqdnTags | 否 | 否 |
> | azureFirewalls | 是 | 否 |
> | bastionHosts | 是 | 是 |
> | bgpServiceCommunities | 否 | 否 |
> | connections | 是 | 是 |
> | ddosCustomPolicies | 是 | 是 |
> | ddosProtectionPlans | 是 | 是 |
> | dnsOperationStatuses | 否 | 否 |
> | dnszones | 是 | 是 |
> | dnszones / A | 否 | 否 |
> | dnszones / AAAA | 否 | 否 |
> | dnszones / all | 否 | 否 |
> | dnszones / CAA | 否 | 否 |
> | dnszones / CNAME | 否 | 否 |
> | dnszones / MX | 否 | 否 |
> | dnszones / NS | 否 | 否 |
> | dnszones / PTR | 否 | 否 |
> | dnszones / recordsets | 否 | 否 |
> | dnszones / SOA | 否 | 否 |
> | dnszones / SRV | 否 | 否 |
> | dnszones / TXT | 否 | 否 |
> | expressRouteCircuits | 是 | 是 |
> | expressRouteCrossConnections | 是 | 是 |
> | expressRouteGateways | 是 | 是 |
> | expressRoutePorts | 是 | 是 |
> | expressRouteServiceProviders | 否 | 否 |
> | firewallPolicies | 是 | 是 |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | 是 |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | 否 |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | 是 |
> | getDnsResourceReference | 否 | 否 |
> | internalNotify | 否 | 否 |
> | loadBalancers | 是 | 否 |
> | localNetworkGateways | 是 | 是 |
> | natGateways | 是 | 是 |
> | networkIntentPolicies | 是 | 是 |
> | networkInterfaces | 是 | 是 |
> | networkProfiles | 是 | 是 |
> | networkSecurityGroups | 是 | 是 |
> | networkWatchers | 是 | 否 |
> | networkWatchers / connectionMonitors | 是 | 否 |
> | networkWatchers / lenses | 是 | 否 |
> | networkWatchers / pingMeshes | 是 | 否 |
> | p2sVpnGateways | 是 | 是 |
> | privateDnsOperationStatuses | 否 | 否 |
> | privateDnsZones | 是 | 是 |
> | privateDnsZones / A | 否 | 否 |
> | privateDnsZones / AAAA | 否 | 否 |
> | privateDnsZones / all | 否 | 否 |
> | privateDnsZones / CNAME | 否 | 否 |
> | privateDnsZones / MX | 否 | 否 |
> | privateDnsZones / PTR | 否 | 否 |
> | privateDnsZones / SOA | 否 | 否 |
> | privateDnsZones / SRV | 否 | 否 |
> | privateDnsZones / TXT | 否 | 否 |
> | privateDnsZones / virtualNetworkLinks | 是 | 是 |
> | privateEndpoints | 是 | 是 |
> | privateLinkServices | 是 | 是 |
> | publicIPAddresses | 是 | 是 |
> | publicIPPrefixes | 是 | 是 |
> | routeFilters | 是 | 是 |
> | routeTables | 是 | 是 |
> | serviceEndpointPolicies | 是 | 是 |
> | trafficManagerGeographicHierarchies | 否 | 否 |
> | trafficmanagerprofiles | 是 | 是 |
> | trafficmanagerprofiles/heatMaps | 否 | 否 |
> | trafficManagerUserMetricsKeys | 否 | 否 |
> | virtualHubs | 是 | 是 |
> | virtualNetworkGateways | 是 | 是 |
> | virtualNetworks | 是 | 是 |
> | virtualNetworkTaps | 是 | 是 |
> | virtualWans | 是 | 是 |
> | vpnGateways | 是 | 否 |
> | vpnSites | 是 | 是 |
> | webApplicationFirewallPolicies | 是 | 是 |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | namespaces / notificationHubs | 是 | 否 |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | 是 | 是 |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | 是 | 是 |
> | ImportSites | 是 | 是 |
> | ServerSites | 是 | 是 |
> | VMwareSites | 是 | 是 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | 是 | 是 |
> | devices | 否 | 否 |
> | linkTargets | 否 | 否 |
> | storageInsightConfigs | 否 | 否 |
> | workspaces | 是 | 是 |
> | workspaces / dataSources | 否 | 否 |
> | workspaces / linkedServices | 否 | 否 |
> | workspaces / query | 否 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | 否 | 否 |
> | managementconfigurations | 是 | 是 |
> | solutions | 是 | 是 |
> | 檢視 | 是 | 是 |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | 否 | 否 |
> | peerAsns | 否 | 否 |
> | peerings | 是 | 是 |
> | peeringServiceProviders | 否 | 否 |
> | peeringServices | 是 | 是 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | 否 | 否 |
> | policyMetadata | 否 | 否 |
> | policyStates | 否 | 否 |
> | policyTrackedResources | 否 | 否 |
> | remediations | 否 | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | 否 | 否 |
> | dashboards | 是 | 是 |
> | userSettings | 否 | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | 是 | 是 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | 是 | 是 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | 否 | 否 |
> | vaults | 是 | 是 |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 是 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / hybridconnections | 否 | 否 |
> | namespaces / hybridconnections / authorizationrules | 否 | 否 |
> | namespaces / wcfrelays | 否 | 否 |
> | namespaces / wcfrelays / authorizationrules | 否 | 否 |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | 否 | 否 |
> | collections | 是 | 是 |
> | collections / applications | 否 | 否 |
> | collections / securityprincipals | 否 | 否 |
> | templateImages | 否 | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 筆查詢 | 是 | 是 |
> | resourceChangeDetails | 否 | 否 |
> | resourceChanges | 否 | 否 |
> | resources | 否 | 否 |
> | resourcesHistory | 否 | 否 |
> | subscriptionsStatus | 否 | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | 否 | 否 |
> | childAvailabilityStatuses | 否 | 否 |
> | childResources | 否 | 否 |
> | 活動 | 否 | 否 |
> | impactedResources | 否 | 否 |
> | 中繼資料 | 否 | 否 |
> | 通知 | 否 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | 是 | 否 |
> | deployments / operations | 否 | 否 |
> | deploymentScripts | 是 | 是 |
> | deploymentScripts / logs | 否 | 否 |
> | 連結 | 否 | 否 |
> | notifyResourceJobs | 否 | 否 |
> | 提供者 | 否 | 否 |
> | resourceGroups | 是 | 否 |
> | subscriptions | 否 | 否 |
> | tenants | 否 | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 應用程式 | 是 | 是 |
> | saasresources | 否 | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | 是 | 是 |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | 否 | 否 |
> | searchServices | 是 | 是 |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | 否 | 否 |
> | advancedThreatProtectionSettings | 否 | 否 |
> | alerts | 否 | 否 |
> | allowedConnections | 否 | 否 |
> | applicationWhitelistings | 否 | 否 |
> | assessmentMetadata | 否 | 否 |
> | assessments | 否 | 否 |
> | autoDismissAlertsRules | 否 | 否 |
> | automations | 是 | 是 |
> | AutoProvisioningSettings | 否 | 否 |
> | Compliances | 否 | 否 |
> | dataCollectionAgents | 否 | 否 |
> | deviceSecurityGroups | 否 | 否 |
> | discoveredSecuritySolutions | 否 | 否 |
> | externalSecuritySolutions | 否 | 否 |
> | InformationProtectionPolicies | 否 | 否 |
> | iotSecuritySolutions | 是 | 是 |
> | iotSecuritySolutions / analyticsModels | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 | 否 |
> | jitNetworkAccessPolicies | 否 | 否 |
> | networkData | 否 | 否 |
> | 原則 | 否 | 否 |
> | pricings | 否 | 否 |
> | regulatoryComplianceStandards | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 | 否 |
> | securityContacts | 否 | 否 |
> | securitySolutions | 否 | 否 |
> | securitySolutionsReferenceData | 否 | 否 |
> | securityStatuses | 否 | 否 |
> | securityStatusesSummaries | 否 | 否 |
> | serverVulnerabilityAssessments | 否 | 否 |
> | settings | 否 | 否 |
> | subAssessments | 否 | 否 |
> | 工作 | 否 | 否 |
> | topologies | 否 | 否 |
> | workspaceSettings | 否 | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | 否 | 否 |
> | alertRules | 否 | 否 |
> | alertRuleTemplates | 否 | 否 |
> | bookmarks | 否 | 否 |
> | 案例 | 否 | 否 |
> | dataConnectors | 否 | 否 |
> | 實體 | 否 | 否 |
> | entityQueries | 否 | 否 |
> | officeConsents | 否 | 否 |
> | settings | 否 | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | 是 | 否 |
> | namespaces / authorizationrules | 否 | 否 |
> | namespaces / disasterrecoveryconfigs | 否 | 否 |
> | namespaces / eventgridfilters | 否 | 否 |
> | namespaces / networkrulesets | 否 | 否 |
> | namespaces / queues | 否 | 否 |
> | namespaces / queues / authorizationrules | 否 | 否 |
> | namespaces / topics | 否 | 否 |
> | namespaces / topics / authorizationrules | 否 | 否 |
> | namespaces / topics / subscriptions | 否 | 否 |
> | namespaces / topics / subscriptions / rules | 否 | 否 |
> | premiumMessagingRegions | 否 | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 應用程式 | 是 | 是 |
> | clusters | 是 | 是 |
> | clusters / applications | 否 | 否 |
> | containerGroups | 是 | 是 |
> | containerGroupSets | 是 | 是 |
> | edgeclusters | 是 | 是 |
> | edgeclusters / applications | 否 | 否 |
> | networks | 是 | 是 |
> | secretstores | 是 | 是 |
> | secretstores / certificates | 否 | 否 |
> | secretstores / secrets | 否 | 否 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | 應用程式 | 是 | 是 |
> | containerGroups | 是 | 是 |
> | gateways | 是 | 是 |
> | networks | 是 | 是 |
> | 密碼 | 是 | 是 |
> | 磁碟區 | 是 | 是 |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | 否 | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 | 否 |
> | rollouts | 是 | 是 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | 是 | 是 |
> | SignalR / eventGridFilters | 否 | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | 是 | 是 |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | 否 | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | 是 | 是 |
> | 應用程式 | 是 | 是 |
> | jitRequests | 是 | 是 |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | 是 | 是 |
> | managedInstances / databases | 否 | 否 |
> | managedInstances / databases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | 否 | 否 |
> | managedInstances / databases / vulnerabilityAssessments | 否 | 否 |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | 否 | 否 |
> | managedInstances / encryptionProtector | 否 | 否 |
> | managedInstances / keys | 否 | 否 |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | 否 | 否 |
> | managedInstances / vulnerabilityAssessments | 否 | 否 |
> | 伺服器 | 是 | 是 |
> | servers / administrators | 否 | 否 |
> | servers / communicationLinks | 否 | 否 |
> | servers / databases | Yes (see [note below](#sqlnote)) | 是 |
> | servers / encryptionProtector | 否 | 否 |
> | servers / firewallRules | 否 | 否 |
> | servers / keys | 否 | 否 |
> | servers / restorableDroppedDatabases | 否 | 否 |
> | servers / serviceobjectives | 否 | 否 |
> | servers / tdeCertificates | 否 | 否 |
> | virtualClusters | 否 | 否 |

<a id="sqlnote" />

> [!NOTE]
> Master 資料庫不支援標籤，但其他資料庫 (包括 Azure SQL 資料倉儲資料庫) 則可支援標籤。 Azure SQL 資料倉儲資料庫的狀態必須是 [作用中] \(不是 [暫停]\)。

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | 是 | 是 |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | 否 | 否 |
> | SqlVirtualMachines | 是 | 是 |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | 是 | 是 |
> | storageAccounts / blobServices | 否 | 否 |
> | storageAccounts / fileServices | 否 | 否 |
> | storageAccounts / queueServices | 否 | 否 |
> | storageAccounts / services | 否 | 否 |
> | storageAccounts / services / metricDefinitions | 否 | 否 |
> | storageAccounts / tableServices | 否 | 否 |
> | usages | 否 | 否 |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | 是 | 是 |
> | caches / storageTargets | 否 | 否 |
> | usageModels | 否 | 否 |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | 否 | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices / workflows | 否 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices / workflows | 否 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | 是 | 是 |
> | storageSyncServices / registeredServers | 否 | 否 |
> | storageSyncServices / syncGroups | 否 | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 | 否 |
> | storageSyncServices / workflows | 否 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | 是 | 是 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | 是 (請參閱下方注意事項) | 是 |

> [!NOTE]
> 您無法在 streamingjobs 執行時新增標記。 阻止資源新增標記。

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | 否 | 否 |
> | CreateSubscription | 否 | 否 |
> | enable | 否 | 否 |
> | rename | 否 | 否 |
> | SubscriptionDefinitions | 否 | 否 |
> | SubscriptionOperations | 否 | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | 是 | 否 |
> | environments / accessPolicies | 否 | 否 |
> | environments / eventsources | 是 | 否 |
> | environments / referenceDataSets | 是 | 否 |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | 是 | 是 |
> | dedicatedCloudServices | 是 | 是 |
> | virtualMachines | 是 | 是 |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | 否 | 否 |
> | apiManagementAccounts / apiAcls | 否 | 否 |
> | apiManagementAccounts / apis | 否 | 否 |
> | apiManagementAccounts / apis / apiAcls | 否 | 否 |
> | apiManagementAccounts / apis / connectionAcls | 否 | 否 |
> | apiManagementAccounts / apis / connections | 否 | 否 |
> | apiManagementAccounts / apis / connections / connectionAcls | 否 | 否 |
> | apiManagementAccounts / apis / localizedDefinitions | 否 | 否 |
> | apiManagementAccounts / connectionAcls | 否 | 否 |
> | apiManagementAccounts / connections | 否 | 否 |
> | billingMeters | 否 | 否 |
> | certificates | 是 | 是 |
> | connectionGateways | 是 | 是 |
> | connections | 是 | 是 |
> | customApis | 是 | 是 |
> | deletedSites | 否 | 否 |
> | functions | 否 | 否 |
> | hostingEnvironments | 是 | 是 |
> | hostingEnvironments / multiRolePools | 否 | 否 |
> | hostingEnvironments / workerPools | 否 | 否 |
> | publishingUsers | 否 | 否 |
> | Mahout | 否 | 否 |
> | resourceHealthMetadata | 否 | 否 |
> | runtimes | 否 | 否 |
> | serverFarms | 是 | 是 |
> | serverFarms / eventGridFilters | 否 | 否 |
> | sites | 是 | 是 |
> | sites / config  | 否 | 否 |
> | sites / eventGridFilters | 否 | 否 |
> | sites / hostNameBindings | 否 | 否 |
> | sites / networkConfig | 否 | 否 |
> | sites / premieraddons | 是 | 是 |
> | sites / slots | 是 | 是 |
> | sites / slots / eventGridFilters | 否 | 否 |
> | sites / slots / hostNameBindings | 否 | 否 |
> | sites / slots / networkConfig | 否 | 否 |
> | sourceControls | 否 | 否 |
> | validate | 否 | 否 |
> | verifyHostingEnvironmentVnet | 否 | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | 否 | 否 |
> | diagnosticSettingsCategories | 否 | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | 是 | 是 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 支援標記 | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | 否 | 否 |
> | componentsSummary | 否 | 否 |
> | monitorInstances | 否 | 否 |
> | monitorInstancesSummary | 否 | 否 |
> | monitors | 否 | 否 |
> | notificationSettings | 否 | 否 |

## <a name="next-steps"></a>後續步驟

若要了解如何將標記套用至資源，請參閱[使用標記來組織您的 Azure 資源](resource-group-using-tags.md)。
