---
title: 完整模式刪除
description: 顯示資源類型如何處理 Azure Resource Manager 範本中的完整模式刪除。
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232679"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>完整模式部署的 Azure 資源刪除

此文章說明當以完整模式部署的範本中沒有資源類型時，資源類型會如何處理刪除。

當類型不在以完整模式部署的範本中時，會刪除標示為 **[是]** 的資源類型。

當不在範本中時，標記為 [**否**] 的資源類型不會自動刪除;不過，如果刪除父資源，則會刪除它們。 如需行為的完整描述，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。

如果您[在範本中部署到一個以上的資源群組](resource-manager-cross-resource-group-deployment.md)，部署作業中指定的資源群組中的資源就有資格刪除。 不會刪除次要資源群組中的資源。

跳至資源提供者命名空間：
> [!div class="op_single_selector"]
> - [Microsoft AAD](#microsoftaad)
> - [附加元件](#microsoftaddons)
> - [ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft Advisor](#microsoftadvisor)
> - [Microsoft.alertsmanagement](#microsoftalertsmanagement)
> - [Microsoft.analysisservices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [AppConfiguration](#microsoftappconfiguration)
> - [AppPlatform](#microsoftappplatform)
> - [Microsoft 證明](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Azconfig](#microsoftazconfig)
> - [Geneva](#microsoftazuregeneva)
> - [AzureActiveDirectory](#microsoftazureactivedirectory)
> - [AzureData](#microsoftazuredata)
> - [AzureStack](#microsoftazurestack)
> - [Microsoft。 Batch](#microsoftbatch)
> - [Microsoft 帳單](#microsoftbilling)
> - [BingMaps](#microsoftbingmaps)
> - [區塊鏈](#microsoftblockchain)
> - [Microsoft 藍圖](#microsoftblueprint)
> - [BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft。容量](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.classiccompute](#microsoftclassiccompute)
> - [ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.classicnetwork](#microsoftclassicnetwork)
> - [Microsoft.classicstorage](#microsoftclassicstorage)
> - [CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft Commerce](#microsoftcommerce)
> - [Microsoft。計算](#microsoftcompute)
> - [Microsoft。耗用量](#microsoftconsumption)
> - [Microsoft.containerinstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [CortanaAnalytics](#microsoftcortanaanalytics)
> - [CostManagement](#microsoftcostmanagement)
> - [CustomerLockbox](#microsoftcustomerlockbox)
> - [CustomProviders](#microsoftcustomproviders)
> - [DataBox](#microsoftdatabox)
> - [DataBoxEdge](#microsoftdataboxedge)
> - [Databricks](#microsoftdatabricks)
> - [Microsoft.datacatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.datamigration](#microsoftdatamigration)
> - [DataShare](#microsoftdatashare)
> - [DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.dbformysql](#microsoftdbformysql)
> - [DBforPostgreSQL](#microsoftdbforpostgresql)
> - [DeploymentManager](#microsoftdeploymentmanager)
> - [DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [DevOps](#microsoftdevops)
> - [DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [DomainRegistration](#microsoftdomainregistration)
> - [DynamicsLcs](#microsoftdynamicslcs)
> - [EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. 功能](#microsoftfeatures)
> - [Microsoft. 圖庫](#microsoftgallery)
> - [Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.hardwaresecuritymodules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [HybridData](#microsofthybriddata)
> - [Hydra 等](#microsofthydra)
> - [ImportExport](#microsoftimportexport)
> - [Microsoft Intune](#microsoftintune)
> - [IoTCentral](#microsoftiotcentral)
> - [Microsoft.iotspaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Kusto](#microsoftkusto)
> - [LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [MachineLearning](#microsoftmachinelearning)
> - [Microsoft.machinelearningservices](#microsoftmachinelearningservices)
> - [Microsoft.managedidentity](#microsoftmanagedidentity)
> - [ManagedServices](#microsoftmanagedservices)
> - [Microsoft 管理](#microsoftmanagement)
> - [Microsoft Maps](#microsoftmaps)
> - [Microsoft Marketplace](#microsoftmarketplace)
> - [MarketplaceApps](#microsoftmarketplaceapps)
> - [MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft。遷移](#microsoftmigrate)
> - [MixedReality](#microsoftmixedreality)
> - [Microsoft NetApp](#microsoftnetapp)
> - [Microsoft 網路](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Objectstore 會](#microsoftobjectstore)
> - [OffAzure](#microsoftoffazure)
> - [Microsoft.operationalinsights](#microsoftoperationalinsights)
> - [Microsoft.operationsmanagement](#microsoftoperationsmanagement)
> - [Microsoft 對等互連](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft 入口網站](#microsoftportal)
> - [Microsoft PowerBI](#microsoftpowerbi)
> - [PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft 轉送](#microsoftrelay)
> - [Microsoft RemoteApp](#microsoftremoteapp)
> - [Az.resourcegraph](#microsoftresourcegraph)
> - [ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [SecurityGraph](#microsoftsecuritygraph)
> - [SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft 服務](#microsoftservices)
> - [Microsoft.signalrservice](#microsoftsignalrservice)
> - [Azurerm.siterecovery](#microsoftsiterecovery)
> - [SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft 解決方案](#microsoftsolutions)
> - [Microsoft .SQL](#microsoftsql)
> - [Microsoft.sqlvirtualmachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [StorageCache](#microsoftstoragecache)
> - [StorageReplication](#microsoftstoragereplication)
> - [Microsoft.storagesync](#microsoftstoragesync)
> - [StorageSyncDev](#microsoftstoragesyncdev)
> - [StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft StorSimple](#microsoftstorsimple)
> - [Mslearn-streamanalytics](#microsoftstreamanalytics)
> - [Microsoft 訂用帳戶](#microsoftsubscription)
> - [Timeseriesinsights-environment-with-eventhub](#microsofttimeseriesinsights)
> - [VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [WindowsIoT](#microsoftwindowsiot)
> - [WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DomainServices | yes |
> | DomainServices/oucontainer | 否 |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | supportProviders | 否 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | aadsupportcases | 否 |
> | addsservices | 否 |
> | agents | 否 |
> | anonymousapiusers | 否 |
> | 組態 | 否 |
> | 記錄 | 否 |
> | reports | 否 |
> | servicehealthmetrics | 否 |
> | 服務 | 否 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 組態 | 否 |
> | generateRecommendations | 否 |
> | 中繼資料 | 否 |
> | Mahout | 否 |
> | suppressions | 否 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | actionRules | yes |
> | alerts | 否 |
> | alertsList | 否 |
> | alertsMetaData | 否 |
> | alertsSummary | 否 |
> | alertsSummaryList | 否 |
> | 反 | 否 |
> | smartDetectorAlertRules | yes |
> | smartDetectorRuntimeEnvironments | 否 |
> | smartGroups | 否 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | yes |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | reportFeedback | 否 |
> | 服務 | yes |
> | validateServiceName | 否 |

## <a name="microsoftappconfiguration"></a>AppConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | configurationStores | yes |
> | configurationStores / eventGridFilters | 否 |

## <a name="microsoftappplatform"></a>AppPlatform

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Spring | yes |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | attestationProviders | 否 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicAdministrators | 否 |
> | dataAliases | 否 |
> | denyAssignments | 否 |
> | elevateAccess | 否 |
> | findOrphanRoleAssignments | 否 |
> | locks | 否 |
> | 權限 | 否 |
> | policyAssignments | 否 |
> | policyDefinitions | 否 |
> | policySetDefinitions | 否 |
> | providerOperations | 否 |
> | roleAssignments | 否 |
> | roleDefinitions | 否 |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | automationAccounts | yes |
> | automationAccounts/設定 | yes |
> | automationAccounts/作業 | 否 |
> | automationAccounts/runbook | yes |
> | automationAccounts/softwareUpdateConfigurations | 否 |
> | automationAccounts/webhook | 否 |

## <a name="microsoftazconfig"></a>Azconfig

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | configurationStores | yes |
> | configurationStores / eventGridFilters | 否 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | 否 |
> | 環境/帳戶 | 否 |
> | 環境/帳戶/命名空間 | 否 |
> | 環境/帳戶/命名空間/設定 | 否 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | b2cDirectories | yes |
> | b2ctenants | 否 |

## <a name="microsoftazuredata"></a>AzureData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hybridDataManagers | yes |
> | postgresInstances | yes |
> | sqlBigDataClusters | yes |
> | sqlInstances | yes |
> | sqlServerRegistrations | yes |
> | sqlServerRegistrations/sqlServers | 否 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | registrations | yes |
> | 註冊/customerSubscriptions | 否 |
> | 註冊/產品 | 否 |
> | verificationKeys | 否 |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | batchAccounts | yes |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | billingAccounts | 否 |
> | billingAccounts/合約 | 否 |
> | billingAccounts / billingPermissions | 否 |
> | billingAccounts / billingProfiles | 否 |
> | billingAccounts / billingProfiles / billingPermissions | 否 |
> | billingAccounts / billingProfiles / billingRoleAssignments | 否 |
> | billingAccounts / billingProfiles / billingRoleDefinitions | 否 |
> | billingAccounts / billingProfiles / billingSubscriptions | 否 |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | 否 |
> | billingAccounts/billingProfiles/客戶 | 否 |
> | billingAccounts/billingProfiles/發票 | 否 |
> | billingAccounts/billingProfiles/發票/pricesheet | 否 |
> | billingAccounts / billingProfiles / invoiceSections | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | 否 |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | 否 |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | 否 |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | 否 |
> | billingAccounts/billingProfiles/invoiceSections/筆交易 | 否 |
> | billingAccounts/billingProfiles/invoiceSections/transfer | 否 |
> | billingAccounts / BillingProfiles / patchOperations | 否 |
> | billingAccounts / billingProfiles / paymentMethods | 否 |
> | billingAccounts/billingProfiles/原則 | 否 |
> | billingAccounts/billingProfiles/pricesheet | 否 |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | 否 |
> | billingAccounts/billingProfiles/products | 否 |
> | billingAccounts/billingProfiles/筆交易 | 否 |
> | billingAccounts / billingRoleAssignments | 否 |
> | billingAccounts / billingRoleDefinitions | 否 |
> | billingAccounts / billingSubscriptions | 否 |
> | billingAccounts/billingSubscriptions/發票 | 否 |
> | billingAccounts / createBillingRoleAssignment | 否 |
> | billingAccounts / createInvoiceSectionOperations | 否 |
> | billingAccounts/客戶 | 否 |
> | billingAccounts/customers/billingPermissions | 否 |
> | billingAccounts/customers/billingSubscriptions | 否 |
> | billingAccounts/customers/initiateTransfer | 否 |
> | billingAccounts/customers/原則 | 否 |
> | billingAccounts/customers/products | 否 |
> | billingAccounts/customers/交易所 | 否 |
> | billingAccounts/客戶/傳輸 | 否 |
> | billingAccounts/部門 | 否 |
> | billingAccounts / enrollmentAccounts | 否 |
> | billingAccounts/發票 | 否 |
> | billingAccounts / invoiceSections | 否 |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | 否 |
> | billingAccounts / invoiceSections / billingSubscriptions | 否 |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | 否 |
> | billingAccounts/invoiceSections/提升許可權 | 否 |
> | billingAccounts / invoiceSections / initiateTransfer | 否 |
> | billingAccounts / invoiceSections / patchOperations | 否 |
> | billingAccounts / invoiceSections / productMoveOperations | 否 |
> | billingAccounts/invoiceSections/products | 否 |
> | billingAccounts/invoiceSections/products/transfer | 否 |
> | billingAccounts/invoiceSections/products/updateAutoRenew | 否 |
> | billingAccounts/invoiceSections/筆交易 | 否 |
> | billingAccounts/invoiceSections/transfer | 否 |
> | billingAccounts / lineOfCredit | 否 |
> | billingAccounts / patchOperations | 否 |
> | billingAccounts / paymentMethods | 否 |
> | billingAccounts/products | 否 |
> | billingAccounts/筆交易 | 否 |
> | billingPeriods | 否 |
> | billingPermissions | 否 |
> | billingProperty | 否 |
> | billingRoleAssignments | 否 |
> | billingRoleDefinitions | 否 |
> | createBillingRoleAssignment | 否 |
> | departments | 否 |
> | enrollmentAccounts | 否 |
> | invoices | 否 |
> | transfers | 否 |
> | 傳輸/acceptTransfer | 否 |
> | 傳輸/declineTransfer | 否 |
> | 傳輸/operationStatus | 否 |
> | 傳輸/validateTransfer | 否 |
> | validateAddress | 否 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | mapApis | yes |
> | updateCommunicationPreference | 否 |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | blockchainMembers | yes |
> | cordaMembers | yes |
> | 位監看員 | yes |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | blueprintAssignments | 否 |
> | blueprintAssignments / assignmentOperations | 否 |
> | blueprintAssignments/作業 | 否 |
> | blueprints | 否 |
> | 藍圖/成品 | 否 |
> | 藍圖/版本 | 否 |
> | 藍圖/版本/構件 | 否 |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | botServices | yes |
> | botServices/通道 | 否 |
> | botServices/connections | 否 |
> | 語言 | 否 |
> | 範本 | 否 |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Redis | yes |
> | RedisConfigDefinition | 否 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appliedReservations | 否 |
> | calculateExchange | 否 |
> | calculatePrice | 否 |
> | calculatePurchasePrice | 否 |
> | catalogs | 否 |
> | commercialReservationOrders | 否 |
> | 兌換 | 否 |
> | placePurchaseOrder | 否 |
> | reservationOrders | 否 |
> | reservationOrders / calculateRefund | 否 |
> | reservationOrders/merge | 否 |
> | reservationOrders/保留 | 否 |
> | reservationOrders/保留/修訂 | 否 |
> | reservationOrders/return | 否 |
> | reservationOrders/split | 否 |
> | reservationOrders/swap | 否 |
> | reservations | 否 |
> | 資源 | 否 |
> | validateReservationOrder | 否 |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | 否 |
> | CdnWebApplicationFirewallPolicies | yes |
> | edgenodes | 否 |
> | 設定檔 | yes |
> | 設定檔/端點 | yes |
> | 設定檔/端點/customdomains | 否 |
> | 設定檔/端點/來源 | 否 |
> | validateProbe | 否 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | certificateOrders | yes |
> | certificateOrders/憑證 | 否 |
> | validateCertificateRegistrationInformation | 否 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | domainNames | yes |
> | domainNames/功能 | 否 |
> | domainNames/internalLoadBalancers | 否 |
> | domainNames/serviceCertificates | 否 |
> | domainNames/插槽 | 否 |
> | domainNames/位置/角色 | 否 |
> | domainNames/位置/角色/Metricdefinitions.listasync | 否 |
> | domainNames/位置/角色/計量 | 否 |
> | moveSubscriptionResources | 否 |
> | operatingSystemFamilies | 否 |
> | operatingSystems | 否 |
> | quotas | 否 |
> | resourceTypes | 否 |
> | validateSubscriptionMoveAvailability | 否 |
> | virtualMachines | yes |
> | virtualMachines/diagnosticSettings | 否 |
> | virtualMachines/Metricdefinitions.listasync | 否 |
> | virtualMachines/計量 | 否 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicInfrastructureResources | 否 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | expressRouteCrossConnections | 否 |
> | expressRouteCrossConnections/對等互連 | 否 |
> | gatewaySupportedDevices | 否 |
> | networkSecurityGroups | yes |
> | quotas | 否 |
> | reservedIps | yes |
> | virtualNetworks | yes |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | 否 |
> | virtualNetworks/virtualNetworkPeerings | 否 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capabilities | 否 |
> | disks | 否 |
> | images | 否 |
> | osImages | 否 |
> | osPlatformImages | 否 |
> | publicImages | 否 |
> | quotas | 否 |
> | storageAccounts | yes |
> | storageAccounts/blobServices | 否 |
> | storageAccounts/fileServices | 否 |
> | storageAccounts/Metricdefinitions.listasync | 否 |
> | storageAccounts/計量 | 否 |
> | storageAccounts/queueServices | 否 |
> | storageAccounts/服務 | 否 |
> | storageAccounts/services/diagnosticSettings | 否 |
> | storageAccounts/services/Metricdefinitions.listasync | 否 |
> | storageAccounts/服務/計量 | 否 |
> | storageAccounts/tableServices | 否 |
> | storageAccounts/vmImages | 否 |
> | vmImages | 否 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | RateCard | 否 |
> | UsageAggregates | 否 |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilitySets | yes |
> | diskEncryptionSets | yes |
> | disks | yes |
> | galleries | yes |
> | 資源庫/應用程式 | 否 |
> | 資源庫/應用程式/版本 | 否 |
> | 資源庫/影像 | 否 |
> | 資源庫/影像/版本 | 否 |
> | hostGroups | yes |
> | hostGroups/主機 | yes |
> | images | yes |
> | proximityPlacementGroups | yes |
> | restorePointCollections | yes |
> | restorePointCollections / restorePoints | 否 |
> | sharedVMImages | yes |
> | sharedVMImages/版本 | 否 |
> | snapshots | yes |
> | virtualMachines | yes |
> | virtualMachines/extensions | yes |
> | virtualMachines/Metricdefinitions.listasync | 否 |
> | virtualMachineScaleSets | yes |
> | virtualMachineScaleSets/extensions | 否 |
> | virtualMachineScaleSets/networkInterfaces | 否 |
> | virtualMachineScaleSets/publicIPAddresses | 否 |
> | virtualMachineScaleSets/virtualMachines | 否 |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | 否 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | AggregatedCost | 否 |
> | 餘額 | 否 |
> | 預算 | 否 |
> | Charges | 否 |
> | CostTags | 否 |
> | credits | 否 |
> | events | 否 |
> | 預測 | 否 |
> | lots | 否 |
> | Marketplace | 否 |
> | Pricesheets | 否 |
> | products | 否 |
> | ReservationDetails | 否 |
> | ReservationRecommendations | 否 |
> | ReservationSummaries | 否 |
> | ReservationTransactions | 否 |
> | 標籤 | 否 |
> | tenants | 否 |
> | 詞彙 | 否 |
> | UsageDetails | 否 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerGroups | yes |
> | serviceAssociationLinks | 否 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | registries | yes |
> | 登錄/組建 | 否 |
> | 登錄/組建/取消 | 否 |
> | 登錄/組建/getLogLink | 否 |
> | 登錄/buildTasks | yes |
> | 登錄/buildTasks/步驟 | 否 |
> | 登錄/eventGridFilters | 否 |
> | 登錄/generateCredentials | 否 |
> | 登錄/getBuildSourceUploadUrl | 否 |
> | 登錄/GetCredentials | 否 |
> | 登錄/importImage | 否 |
> | 登錄/queueBuild | 否 |
> | 登錄/regenerateCredential | 否 |
> | 登錄/regenerateCredentials | 否 |
> | 登錄/複寫 | yes |
> | 登錄/執行 | 否 |
> | 登錄/執行/取消 | 否 |
> | 登錄/scheduleRun | 否 |
> | 登錄/scopeMaps | 否 |
> | 登錄/taskRuns | yes |
> | 登錄/工作 | yes |
> | 登錄/權杖 | 否 |
> | 登錄/updatePolicies | 否 |
> | 登錄/webhook | yes |
> | 登錄/webhook/getCallbackConfig | 否 |
> | 登錄/webhook/ping | 否 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | containerServices | yes |
> | managedClusters | yes |
> | openShiftManagedClusters | yes |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Alerts | 否 |
> | BillingAccounts | 否 |
> | 預算 | 否 |
> | CloudConnectors | 否 |
> | 連接器 | yes |
> | 部門 | 否 |
> | 維度 | 否 |
> | EnrollmentAccounts | 否 |
> | 匯出 | 否 |
> | ExternalBillingAccounts | 否 |
> | ExternalBillingAccounts/警示 | 否 |
> | ExternalBillingAccounts/維度 | 否 |
> | ExternalBillingAccounts/預測 | 否 |
> | ExternalBillingAccounts/查詢 | 否 |
> | ExternalSubscriptions | 否 |
> | ExternalSubscriptions/警示 | 否 |
> | ExternalSubscriptions/維度 | 否 |
> | ExternalSubscriptions/預測 | 否 |
> | ExternalSubscriptions/查詢 | 否 |
> | 預見性 | 否 |
> | 查詢 | 否 |
> | 註冊 | 否 |
> | Reportconfigs | 否 |
> | 報告 | 否 |
> | 設定 | 否 |
> | showbackRules | 否 |
> | Views | 否 |

## <a name="microsoftcustomerlockbox"></a>CustomerLockbox

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | requests | 否 |

## <a name="microsoftcustomproviders"></a>CustomProviders

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 關聯 | 否 |
> | resourceProviders | yes |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | jobs | yes |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | yes |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaces | yes |
> | 工作區/virtualNetworkPeerings | 否 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | catalogs | yes |
> | datacatalogs | yes |
> | datacatalogs/資料來源 | 否 |
> | datacatalogs/資料來源/掃描 | 否 |
> | datacatalogs/資料來源/掃描/資料集 | 否 |
> | datacatalogs/資料來源/掃描/觸發程式 | 否 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataFactories | yes |
> | Microsoft.azure.management.datafactories/diagnosticSettings | 否 |
> | Microsoft.azure.management.datafactories/Metricdefinitions.listasync | 否 |
> | dataFactorySchema | 否 |
> | factories | yes |
> | factory/integrationRuntimes | 否 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |
> | 帳戶/dataLakeStoreAccounts | 否 |
> | 帳戶/storageAccounts | 否 |
> | 帳戶/storageAccounts/容器 | 否 |
> | 帳戶/transferAnalyticsUnits | 否 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |
> | 帳戶/eventGridFilters | 否 |
> | 帳戶/firewallRules | 否 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | yes |
> | 服務/專案 | yes |

## <a name="microsoftdatashare"></a>DataShare

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |
> | 帳戶/共用 | 否 |
> | 帳戶/共用/資料集 | 否 |
> | 帳戶/共用/邀請 | 否 |
> | 帳戶/共用/providersharesubscriptions | 否 |
> | 帳戶/共用/synchronizationSettings | 否 |
> | 帳戶/sharesubscriptions | 否 |
> | accounts/sharesubscriptions/consumerSourceDataSets | 否 |
> | accounts/sharesubscriptions/datasetmappings | 否 |
> | 帳戶/sharesubscriptions/觸發程式 | 否 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | yes |
> | 伺服器/顧問 | 否 |
> | 伺服器/privateEndpointConnectionProxies | 否 |
> | 伺服器/privateEndpointConnections | 否 |
> | 伺服器/privateLinkResources | 否 |
> | 伺服器/queryTexts | 否 |
> | 伺服器/recoverableServers | 否 |
> | 伺服器/topQueryStatistics | 否 |
> | 伺服器/virtualNetworkRules | 否 |
> | 伺服器/waitStatistics | 否 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | servers | yes |
> | 伺服器/顧問 | 否 |
> | 伺服器/privateEndpointConnectionProxies | 否 |
> | 伺服器/privateEndpointConnections | 否 |
> | 伺服器/privateLinkResources | 否 |
> | 伺服器/queryTexts | 否 |
> | 伺服器/recoverableServers | 否 |
> | 伺服器/topQueryStatistics | 否 |
> | 伺服器/virtualNetworkRules | 否 |
> | 伺服器/waitStatistics | 否 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | serverGroups | yes |
> | servers | yes |
> | 伺服器/顧問 | 否 |
> | 伺服器/金鑰 | 否 |
> | 伺服器/privateEndpointConnectionProxies | 否 |
> | 伺服器/privateEndpointConnections | 否 |
> | 伺服器/privateLinkResources | 否 |
> | 伺服器/queryTexts | 否 |
> | 伺服器/recoverableServers | 否 |
> | 伺服器/topQueryStatistics | 否 |
> | 伺服器/virtualNetworkRules | 否 |
> | 伺服器/waitStatistics | 否 |
> | serversv2 | yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | artifactSources | yes |
> | rollouts | yes |
> | serviceTopologies | yes |
> | serviceTopologies/服務 | yes |
> | serviceTopologies/services/serviceUnits | yes |
> | steps | yes |

## <a name="microsoftdesktopvirtualization"></a>DesktopVirtualization

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationgroups | yes |
> | applicationgroups/應用程式 | 否 |
> | applicationgroups/桌上型電腦 | 否 |
> | applicationgroups / startmenuitems | 否 |
> | hostpools | yes |
> | hostpools / sessionhosts | 否 |
> | hostpools / sessionhosts / usersessions | 否 |
> | hostpools / usersessions | 否 |
> | workspaces | yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | ElasticPools | yes |
> | ElasticPools / IotHubTenants | yes |
> | IotHubs | yes |
> | IotHubs/eventGridFilters | 否 |
> | ProvisioningServices | yes |
> | usages | 否 |

## <a name="microsoftdevops"></a>DevOps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 段 | yes |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | controllers | yes |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | labcenters | yes |
> | labs | yes |
> | 實驗室/環境 | yes |
> | 實驗室/serviceRunners | yes |
> | 實驗室/virtualMachines | yes |
> | schedules | yes |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | databaseAccountNames | 否 |
> | databaseAccounts | yes |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | yes |
> | 網域/domainOwnershipIdentifiers | 否 |
> | generateSsoRequest | 否 |
> | topLevelDomains | 否 |
> | validateDomainRegistrationInformation | 否 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | lcsprojects | 否 |
> | lcsprojects / clouddeployments | 否 |
> | lcsprojects/連接器 | 否 |

## <a name="microsoftenterpriseknowledgegraph"></a>EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | yes |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | domains | yes |
> | 網域/主題 | 否 |
> | eventSubscriptions | 否 |
> | extensionTopics | 否 |
> | topics | yes |
> | topicTypes | 否 |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | yes |
> | namespaces | yes |
> | 命名空間/authorizationrules | 否 |
> | 命名空間/disasterrecoveryconfigs | 否 |
> | 命名空間/eventhubs | 否 |
> | 命名空間/eventhubs/authorizationrules | 否 |
> | 命名空間/eventhubs/consumergroups | 否 |
> | 命名空間/networkrulesets | 否 |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 特性 | 否 |
> | 提供者 | 否 |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | enroll | 否 |
> | galleryitems | 否 |
> | generateartifactaccessuri | 否 |
> | myareas | 否 |
> | myareas/區域 | 否 |
> | myareas/區域/區域 | 否 |
> | myareas/區域/區域/galleryitem | 否 |
> | myareas/areas/galleryitem | 否 |
> | myareas/galleryitem | 否 |
> | 註冊 | 否 |
> | 資源 | 否 |
> | retrieveresourcesbyid | 否 |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | configurationProfileAssignments | 否 |
> | guestConfigurationAssignments | 否 |
> | software | 否 |
> | softwareUpdateProfile | 否 |
> | softwareUpdates | 否 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hanaInstances | yes |
> | sapMonitors | yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dedicatedHSMs | yes |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | yes |
> | 叢集/應用程式 | 否 |

## <a name="microsofthealthcareapis"></a>HealthcareApis

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 服務 | yes |

## <a name="microsofthybridcompute"></a>HybridCompute

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 機時 | yes |
> | 機器/擴充功能 | yes |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dataManagers | yes |

## <a name="microsofthydra"></a>Hydra 等

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | yes |
> | networkScopes | yes |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | jobs | yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appTemplates | 否 |
> | IoTApps | yes |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 圖形 | yes |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | deletedVaults | 否 |
> | hsmPools | yes |
> | vaults | yes |
> | 保存庫/accessPolicies | 否 |
> | 保存庫/eventGridFilters | 否 |
> | 保存庫/秘密 | 否 |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | yes |
> | 叢集/attacheddatabaseconfigurations | 否 |
> | 叢集/資料庫 | 否 |
> | 叢集/資料庫/dataconnections | 否 |
> | 叢集/資料庫/eventhubconnections | 否 |
> | 叢集/sharedidentities | 否 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | labaccounts | yes |
> | users | 否 |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hostingEnvironments | yes |
> | integrationAccounts | yes |
> | integrationServiceEnvironments | yes |
> | integrationServiceEnvironments/managedApis | yes |
> | isolatedEnvironments | yes |
> | workflows | yes |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | commitmentPlans | yes |
> | webServices | yes |
> | 工作區 | yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaces | yes |
> | 工作區/計算 | 否 |
> | 工作區/eventGridFilters | 否 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | Identities | 否 |
> | userAssignedIdentities | yes |

## <a name="microsoftmanagedservices"></a>ManagedServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | 否 |
> | registrationAssignments | 否 |
> | registrationDefinitions | 否 |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | getEntities | 否 |
> | managementGroups | 否 |
> | 資源 | 否 |
> | startTenantBackfill | 否 |
> | tenantBackfillStatus | 否 |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | yes |
> | 帳戶/eventGridFilters | 否 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | offers | 否 |
> | offerTypes | 否 |
> | offerTypes/發行者 | 否 |
> | offerTypes/發行者/優惠 | 否 |
> | offerTypes/發行者/優惠/方案 | 否 |
> | offerTypes/發行者/優惠/方案/合約 | 否 |
> | offerTypes/發行者/供應專案/方案/專案 | 否 |
> | offerTypes/發行者/優惠/方案/importImage | 否 |
> | privategalleryitems | 否 |
> | products | 否 |
> | 發行者 | 否 |
> | 發行者/優惠 | 否 |
> | 發行者/供應專案/修訂 | 否 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | classicDevServices | yes |
> | updateCommunicationPreference | 否 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | agreements | 否 |
> | offertypes | 否 |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | mediaservices | yes |
> | windowsazure.mediaservices.extensions/accountFilters | 否 |
> | windowsazure.mediaservices.extensions/資產 | 否 |
> | windowsazure.mediaservices.extensions/資產/Assetfilter | 否 |
> | windowsazure.mediaservices.extensions/contentKeyPolicies | 否 |
> | windowsazure.mediaservices.extensions/eventGridFilters | 否 |
> | windowsazure.mediaservices.extensions/liveEventOperations | 否 |
> | windowsazure.mediaservices.extensions/Liveevent | yes |
> | windowsazure.mediaservices.extensions/Liveevent/Liveoutput | 否 |
> | windowsazure.mediaservices.extensions/liveOutputOperations | 否 |
> | windowsazure.mediaservices.extensions/mediaGraphs | 否 |
> | windowsazure.mediaservices.extensions/streamingEndpointOperations | 否 |
> | windowsazure.mediaservices.extensions/Streamingendpoint | yes |
> | windowsazure.mediaservices.extensions/Streaminglocator | 否 |
> | windowsazure.mediaservices.extensions/streamingPolicies | 否 |
> | windowsazure.mediaservices.extensions/轉換 | 否 |
> | windowsazure.mediaservices.extensions/轉換/作業 | 否 |

## <a name="microsoftmicroservices4spring"></a>Microservices4Spring

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | appClusters | yes |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | assessmentProjects | yes |
> | migrateprojects | yes |
> | projects | yes |

## <a name="microsoftmixedreality"></a>MixedReality

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | yes |
> | objectUnderstandingAccounts | yes |
> | remoteRenderingAccounts | yes |
> | spatialAnchorsAccounts | yes |
> | surfaceReconstructionAccounts | yes |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | netAppAccounts | yes |
> | netAppAccounts / capacityPools | yes |
> | netAppAccounts/capacityPools/磁片區 | yes |
> | netAppAccounts/capacityPools/磁片區/mountTargets | yes |
> | netAppAccounts/capacityPools/磁片區/快照集 | yes |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationGateways | yes |
> | applicationGatewayWebApplicationFirewallPolicies | yes |
> | applicationSecurityGroups | yes |
> | azureFirewallFqdnTags | 否 |
> | azureFirewalls | yes |
> | bastionHosts | yes |
> | bgpServiceCommunities | 否 |
> | connections | yes |
> | ddosCustomPolicies | yes |
> | ddosProtectionPlans | yes |
> | dnsOperationStatuses | 否 |
> | dnszones | yes |
> | dnszones/A | 否 |
> | dnszones/AAAA | 否 |
> | dnszones/全部 | 否 |
> | dnszones/CAA | 否 |
> | dnszones/CNAME | 否 |
> | dnszones/MX | 否 |
> | dnszones/NS | 否 |
> | dnszones/PTR | 否 |
> | dnszones/記錄集 | 否 |
> | dnszones/SOA | 否 |
> | dnszones/SRV | 否 |
> | dnszones/TXT | 否 |
> | expressRouteCircuits | yes |
> | expressRouteCrossConnections | yes |
> | expressRouteGateways | yes |
> | expressRoutePorts | yes |
> | expressRouteServiceProviders | 否 |
> | firewallPolicies | yes |
> | frontdoors | yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | 否 |
> | frontdoorWebApplicationFirewallPolicies | yes |
> | getDnsResourceReference | 否 |
> | internalNotify | 否 |
> | loadBalancers | yes |
> | localNetworkGateways | yes |
> | natGateways | yes |
> | networkIntentPolicies | yes |
> | networkInterfaces | yes |
> | networkProfiles | yes |
> | networkSecurityGroups | yes |
> | networkWatchers | yes |
> | networkWatchers / connectionMonitors | yes |
> | networkWatchers/鏡頭 | yes |
> | networkWatchers / pingMeshes | yes |
> | p2sVpnGateways | yes |
> | privateDnsOperationStatuses | 否 |
> | privateDnsZones | yes |
> | privateDnsZones/A | 否 |
> | privateDnsZones/AAAA | 否 |
> | privateDnsZones/全部 | 否 |
> | privateDnsZones/CNAME | 否 |
> | privateDnsZones/MX | 否 |
> | privateDnsZones/PTR | 否 |
> | privateDnsZones/SOA | 否 |
> | privateDnsZones/SRV | 否 |
> | privateDnsZones/TXT | 否 |
> | privateDnsZones / virtualNetworkLinks | yes |
> | privateEndpoints | yes |
> | privateLinkServices | yes |
> | publicIPAddresses | yes |
> | publicIPPrefixes | yes |
> | routeFilters | yes |
> | routeTables | yes |
> | serviceEndpointPolicies | yes |
> | trafficManagerGeographicHierarchies | 否 |
> | trafficmanagerprofiles | yes |
> | trafficmanagerprofiles/熱度圖 | 否 |
> | trafficManagerUserMetricsKeys | 否 |
> | virtualHubs | yes |
> | virtualNetworkGateways | yes |
> | virtualNetworks | yes |
> | virtualNetworkTaps | yes |
> | virtualWans | yes |
> | vpnGateways | yes |
> | vpnSites | yes |
> | webApplicationFirewallPolicies | yes |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | namespaces | yes |
> | 命名空間/notificationHubs | yes |

## <a name="microsoftobjectstore"></a>Objectstore 會

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | osNamespaces | yes |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | HyperVSites | yes |
> | ImportSites | yes |
> | ServerSites | yes |
> | VMwareSites | yes |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | clusters | yes |
> | devices | 否 |
> | linkTargets | 否 |
> | storageInsightConfigs | 否 |
> | workspaces | yes |
> | 工作區/資料來源 | 否 |
> | 工作區/Linkedservices.json 和 datasets.json | 否 |
> | 工作區/查詢 | 否 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managementassociations | 否 |
> | managementconfigurations | yes |
> | solutions | yes |
> | 檢視 | yes |

## <a name="microsoftpeering"></a>Microsoft 對等互連

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | legacyPeerings | 否 |
> | peerAsns | 否 |
> | 對等互連 | yes |
> | peeringServiceProviders | 否 |
> | peeringServices | yes |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | policyEvents | 否 |
> | policyMetadata | 否 |
> | policyStates | 否 |
> | policyTrackedResources | 否 |
> | remediations | 否 |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | consoles | 否 |
> | dashboards | yes |
> | userSettings | 否 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | workspaceCollections | yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | capacities | yes |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | backupProtectedItems | 否 |
> | vaults | yes |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | namespaces | yes |
> | 命名空間/authorizationrules | 否 |
> | 命名空間/hybridconnections | 否 |
> | 命名空間/hybridconnections/authorizationrules | 否 |
> | 命名空間/wcfrelays | 否 |
> | 命名空間/wcfrelays/authorizationrules | 否 |

## <a name="microsoftremoteapp"></a>Microsoft RemoteApp

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | accounts | 否 |
> | 收藏 | yes |
> | 集合/應用程式 | 否 |
> | 集合/securityprincipalsgetresponse | 否 |
> | templateImages | 否 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 查詢 | yes |
> | resourceChangeDetails | 否 |
> | resourceChanges | 否 |
> | 資源 | 否 |
> | resourcesHistory | 否 |
> | subscriptionsStatus | 否 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | availabilityStatuses | 否 |
> | childAvailabilityStatuses | 否 |
> | childResources | 否 |
> | events | 否 |
> | impactedResources | 否 |
> | 中繼資料 | 否 |
> | 通知 | 否 |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | deployments | 否 |
> | 部署/作業 | 否 |
> | deploymentScripts | yes |
> | deploymentScripts/記錄 | 否 |
> | 連結 | 否 |
> | notifyResourceJobs | 否 |
> | 提供者 | 否 |
> | resourceGroups | 否 |
> | 資源 | 否 |
> | subscriptions | 否 |
> | 訂閱/提供者 | 否 |
> | 訂用帳戶/資源 | 否 |
> | 訂閱/tagnames | 否 |
> | 訂閱/tagNames/tagValues | 否 |
> | tenants | 否 |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | yes |
> | saasresources | 否 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | jobcollections | yes |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | resourceHealthMetadata | 否 |
> | searchServices | yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | 否 |
> | advancedThreatProtectionSettings | 否 |
> | alerts | 否 |
> | allowedConnections | 否 |
> | applicationWhitelistings | 否 |
> | assessmentMetadata | 否 |
> | 評估 | 否 |
> | autoDismissAlertsRules | 否 |
> | 自動化 | yes |
> | AutoProvisioningSettings | 否 |
> | Compliances | 否 |
> | dataCollectionAgents | 否 |
> | deviceSecurityGroups | 否 |
> | discoveredSecuritySolutions | 否 |
> | externalSecuritySolutions | 否 |
> | InformationProtectionPolicies | 否 |
> | iotSecuritySolutions | yes |
> | iotSecuritySolutions / analyticsModels | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | 否 |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | 否 |
> | jitNetworkAccessPolicies | 否 |
> | Networkdata.xml | 否 |
> | 原則 | 否 |
> | pricings | 否 |
> | regulatoryComplianceStandards | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls | 否 |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | 否 |
> | securityContacts | 否 |
> | securitySolutions | 否 |
> | securitySolutionsReferenceData | 否 |
> | securityStatuses | 否 |
> | securityStatusesSummaries | 否 |
> | serverVulnerabilityAssessments | 否 |
> | settings | 否 |
> | subAssessments | 否 |
> | 工作 | 否 |
> | topologies | 否 |
> | workspaceSettings | 否 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftsecurityinsights"></a>SecurityInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 聚合 | 否 |
> | alertRules | 否 |
> | alertRuleTemplates | 否 |
> | 標籤 | 否 |
> | 案例 | 否 |
> | dataConnectors | 否 |
> | 實體 | 否 |
> | entityQueries | 否 |
> | officeConsents | 否 |
> | settings | 否 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | namespaces | yes |
> | 命名空間/authorizationrules | 否 |
> | 命名空間/disasterrecoveryconfigs | 否 |
> | 命名空間/eventgridfilters | 否 |
> | 命名空間/networkrulesets | 否 |
> | 命名空間/佇列 | 否 |
> | 命名空間/佇列/authorizationrules | 否 |
> | 命名空間/主題 | 否 |
> | 命名空間/主題/authorizationrules | 否 |
> | 命名空間/主題/訂用帳戶 | 否 |
> | 命名空間/主題/訂用帳戶/規則 | 否 |
> | premiumMessagingRegions | 否 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | yes |
> | clusters | yes |
> | 叢集/應用程式 | 否 |
> | containerGroups | yes |
> | containerGroupSets | yes |
> | edgeclusters | yes |
> | edgeclusters/應用程式 | 否 |
> | networks | yes |
> | secretstores | yes |
> | secretstores/憑證 | 否 |
> | secretstores/秘密 | 否 |
> | 磁碟區 | yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 應用程式所需 | yes |
> | containerGroups | yes |
> | gateways | yes |
> | networks | yes |
> | 密碼 | yes |
> | 磁碟區 | yes |

## <a name="microsoftservices"></a>Microsoft 服務

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | providerRegistrations | 否 |
> | providerRegistrations / resourceTypeRegistrations | 否 |
> | rollouts | yes |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SignalR | yes |
> | SignalR/eventGridFilters | 否 |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SiteRecoveryVault | yes |

## <a name="microsoftsoftwareplan"></a>SoftwarePlan

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | hybridUseBenefits | 否 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | applicationDefinitions | yes |
> | 應用程式所需 | yes |
> | jitRequests | yes |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managedInstances | yes |
> | managedInstances/資料庫 | yes |
> | managedInstances/資料庫/backupShortTermRetentionPolicies | 否 |
> | managedInstances/資料庫/架構/資料表/資料行/sensitivityLabels | 否 |
> | managedInstances/資料庫/vulnerabilityAssessments | 否 |
> | managedInstances/資料庫/vulnerabilityAssessments/規則/基準 | 否 |
> | managedInstances/encryptionProtector | 否 |
> | managedInstances/金鑰 | 否 |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 否 |
> | managedInstances/vulnerabilityAssessments | 否 |
> | servers | yes |
> | 伺服器/系統管理員 | 否 |
> | 伺服器/communicationLinks | 否 |
> | 伺服器/資料庫 | yes |
> | 伺服器/encryptionProtector | 否 |
> | 伺服器/firewallRules | 否 |
> | 伺服器/金鑰 | 否 |
> | 伺服器/restorableDroppedDatabases | 否 |
> | 伺服器/serviceobjectives | 否 |
> | 伺服器/tdeCertificates | 否 |
> | virtualClusters | 否 |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | yes |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | 否 |
> | SqlVirtualMachines | yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageAccounts | yes |
> | storageAccounts/blobServices | 否 |
> | storageAccounts/fileServices | 否 |
> | storageAccounts/queueServices | 否 |
> | storageAccounts/服務 | 否 |
> | storageAccounts/services/Metricdefinitions.listasync | 否 |
> | storageAccounts/tableServices | 否 |
> | usages | 否 |

## <a name="microsoftstoragecache"></a>StorageCache

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 緩衝區 | yes |
> | 快取/storageTargets | 否 |
> | usageModels | 否 |

## <a name="microsoftstoragereplication"></a>StorageReplication

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | replicationGroups | 否 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices / syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices/工作流程 | 否 |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices / syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices/工作流程 | 否 |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | storageSyncServices | yes |
> | storageSyncServices / registeredServers | 否 |
> | storageSyncServices / syncGroups | 否 |
> | storageSyncServices / syncGroups / cloudEndpoints | 否 |
> | storageSyncServices / syncGroups / serverEndpoints | 否 |
> | storageSyncServices/工作流程 | 否 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | managers | yes |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | streamingjobs | yes |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | 取消 | 否 |
> | CreateSubscription | 否 |
> | enable | 否 |
> | rename | 否 |
> | SubscriptionDefinitions | 否 |
> | SubscriptionOperations | 否 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | environments | yes |
> | 環境/accessPolicies | 否 |
> | 環境/eventsources | yes |
> | 環境/referenceDataSets | yes |

## <a name="microsoftvmwarecloudsimple"></a>VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | dedicatedCloudNodes | yes |
> | dedicatedCloudServices | yes |
> | virtualMachines | yes |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | apiManagementAccounts | 否 |
> | apiManagementAccounts/Apiacl | 否 |
> | apiManagementAccounts/api | 否 |
> | apiManagementAccounts/api/Apiacl | 否 |
> | apiManagementAccounts/api/Connectionacl | 否 |
> | apiManagementAccounts/api/連線 | 否 |
> | apiManagementAccounts/api/connections/Connectionacl | 否 |
> | apiManagementAccounts/api/localizedDefinitions | 否 |
> | apiManagementAccounts/Connectionacl | 否 |
> | apiManagementAccounts/connections | 否 |
> | billingMeters | 否 |
> | certificates | yes |
> | connectionGateways | yes |
> | connections | yes |
> | customApis | yes |
> | deletedSites | 否 |
> | functions | 否 |
> | hostingEnvironments | yes |
> | hostingEnvironments/multiRolePools | 否 |
> | hostingEnvironments/workerPools | 否 |
> | publishingUsers | 否 |
> | Mahout | 否 |
> | resourceHealthMetadata | 否 |
> | runtimes | 否 |
> | serverFarms | yes |
> | serverFarms/eventGridFilters | 否 |
> | sites | yes |
> | sites/config  | 否 |
> | sites/eventGridFilters | 否 |
> | sites/hostNameBindings | 否 |
> | sites/Networkconfig.netcfg | 否 |
> | sites/premieraddons | yes |
> | 網站/位置 | yes |
> | sites/位置/eventGridFilters | 否 |
> | sites/位置/hostNameBindings | 否 |
> | sites/位置/Networkconfig.netcfg | 否 |
> | sourceControls | 否 |
> | validate | 否 |
> | verifyHostingEnvironmentVnet | 否 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | diagnosticSettings | 否 |
> | diagnosticSettingsCategories | 否 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | DeviceServices | yes |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | 資源類型 | 完整模式刪除 |
> | ------------- | ----------- |
> | components | 否 |
> | componentsSummary | 否 |
> | monitorInstances | 否 |
> | monitorInstancesSummary | 否 |
> | monitors | 否 |
> | notificationSettings | 否 |

## <a name="next-steps"></a>後續步驟

若要以逗點分隔值檔案的形式取得相同資料，請下載 [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)。
