---
title: 資源的 Azure Resource Manager 標記支援
description: 顯示哪些 Azure 資源類型支援標記。 提供所有 Azure 服務的詳細資料。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 05/10/2019
ms.author: tomfitz
ms.openlocfilehash: 7ef37323fb8150e3a6b52800bfafa2585ae328c2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523227"
---
# <a name="tag-support-for-azure-resources"></a>Azure 資源的標記支援
本文將說明資源類型是否支援[標記](resource-group-using-tags.md)。 資料行均標示**支援標記**指出資源類型是否有標記的屬性。 資料行均標示**成本報表中的標記**指出該資源類型是否通過 「 成本 」 報告來標記。

若要以逗號區隔值檔案的形式取得相同資料，請下載 [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv)。

## <a name="microsoftaad"></a>Microsoft.AAD
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| DomainServices | 有 | 有 |
| DomainServices/oucontainer | 無 | 無 |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 無 |  無 |
| diagnosticSettingsCategories | 無 |  無 |

## <a name="microsoftaddons"></a>Microsoft.Addons
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| supportProviders | 無 |  無 |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| aadsupportcases | 無 |  無 |
| addsservices | 無 |  無 |
| agents | 無 |  無 |
| anonymousapiusers | 無 |  無 |
| 組態 | 無 |  無 |
| 記錄 | 無 |  無 |
| 報告 | 無 |  無 |
| 服務 | 無 |  無 |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 組態 | 無 |  無 |
| generateRecommendations | 無 |  無 |
| 建議 | 無 |  無 |
| suppressions | 無 |  無 |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| actionRules | 無 |  無 |
| 警示 | 無 |  無 |
| alertsList | 無 |  無 |
| alertsSummary | 無 |  無 |
| alertsSummaryList | 無 |  無 |
| smartDetectorAlertRules | 無 |  無 |
| smartDetectorRuntimeEnvironments | 無 |  無 |
| smartGroups | 無 |  無 |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 伺服器 | 有 | 有 |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| reportFeedback | 無 |  無 |
| 服務 | 有 | 有 |
| validateServiceName | 無 |  無 |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| attestationProviders | 無 |  無 |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| classicAdministrators | 無 |  無 |
| denyAssignments | 無 |  無 |
| elevateAccess | 無 |  無 |
| locks | 無 |  無 |
| 權限 | 無 |  無 |
| policyAssignments | 無 |  無 |
| policyDefinitions | 無 |  無 |
| policySetDefinitions | 無 |  無 |
| providerOperations | 無 |  無 |
| roleAssignments | 無 |  無 |
| roleDefinitions | 無 |  無 |

## <a name="microsoftautomation"></a>Microsoft.Automation
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| automationAccounts | 有 | 有 |
| automationAccounts/configurations | 有 | 有 |
| automationAccounts/jobs | 無 |  無 |
| automationAccounts/runbooks | 有 | 有 |
| automationAccounts/softwareUpdateConfigurations | 無 | 無 |
| automationAccounts/webhooks | 無 |  無 |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| environments | 無 |  無 |
| environments/accounts | 無 |  無 |
| environments/accounts/namespaces | 無 |  無 |
| environments/accounts/namespaces/configurations | 無 |  無 |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| b2cDirectories | 有 | 無 |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| registrations | 有 | 有 |
| registrations/customerSubscriptions | 無 |  無 |
| registrations/products | 無 |  無 |

## <a name="microsoftbatch"></a>Microsoft.Batch
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| batchAccounts | 有 | 有 |

## <a name="microsoftbilling"></a>Microsoft.Billing
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| billingAccounts | 無 |  無 |
| billingAccounts/billingProfiles | 無 |  無 |
| billingAccounts/billingProfiles/billingSubscriptions | 無 |  無 |
| billingAccounts/billingProfiles/invoices | 無 |  無 |
| billingAccounts/billingProfiles/invoices/pricesheet | 無 |  無 |
| billingAccounts/billingProfiles/operationStatus | 無 |  無 |
| billingAccounts/billingProfiles/paymentMethods | 無 |  無 |
| billingAccounts/billingProfiles/policies | 無 |  無 |
| billingAccounts/billingProfiles/pricesheet | 無 |  無 |
| billingAccounts/billingProfiles/products | 無 |  無 |
| billingAccounts/billingProfiles/transactions | 無 |  無 |
| billingAccounts/billingSubscriptions | 無 |  無 |
| billingAccounts/departments | 無 |  無 |
| billingAccounts/eligibleOffers | 無 |  無 |
| billingAccounts/enrollmentAccounts | 無 |  無 |
| billingAccounts/invoices | 無 |  無 |
| billingAccounts/invoiceSections | 無 |  無 |
| billingAccounts/invoiceSections/billingSubscriptions | 無 |  無 |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | 無 |  無 |
| billingAccounts/invoiceSections/importRequests | 無 |  無 |
| billingAccounts/invoiceSections/initiateImportRequest | 無 |  無 |
| billingAccounts/invoiceSections/initiateTransfer | 無 |  無 |
| billingAccounts/invoiceSections/operationStatus | 無 |  無 |
| billingAccounts/invoiceSections/products | 無 |  無 |
| billingAccounts/invoiceSections/transfers | 無 |  無 |
| billingAccounts/products | 無 |  無 |
| billingAccounts/projects | 無 |  無 |
| billingAccounts/projects/billingSubscriptions | 無 |  無 |
| billingAccounts/projects/importRequests | 無 |  無 |
| billingAccounts/projects/initiateImportRequest | 無 |  無 |
| billingAccounts/projects/operationStatus | 無 |  無 |
| billingAccounts/projects/products | 無 |  無 |
| billingAccounts/transactions | 無 |  無 |
| billingPeriods | 無 |  無 |
| BillingPermissions | 無 |  無 |
| billingProperty | 無 |  無 |
| BillingRoleAssignments | 無 |  無 |
| BillingRoleDefinitions | 無 |  無 |
| CreateBillingRoleAssignment | 無 |  無 |
| departments | 無 |  無 |
| enrollmentAccounts | 無 |  無 |
| importRequests | 無 |  無 |
| importRequests/acceptImportRequest | 無 |  無 |
| importRequests/declineImportRequest | 無 |  無 |
| invoices | 無 |  無 |
| transfers | 無 |  無 |
| transfers/acceptTransfer | 無 |  無 |
| transfers/declineTransfer | 無 |  無 |
| transfers/operationStatus | 無 |  無 |
| usagePlans | 無 |  無 |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| mapApis | 有 | 有 |
| updateCommunicationPreference | 無 |  無 |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| BizTalk | 有 | 有 |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| blueprintAssignments | 無 |  無 |
| blueprintAssignments/assignmentOperations | 無 |  無 |
| blueprintAssignments/operations | 無 |  無 |
| blueprints | 無 |  無 |
| blueprints/artifacts | 無 |  無 |
| blueprints/versions | 無 |  無 |
| blueprints/versions/artifacts | 無 |  無 |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| botServices | 有 | 有 |
| botServices/channels | 無 |  無 |
| botServices/connections | 無 |  無 |

## <a name="microsoftcache"></a>Microsoft.Cache
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| Redis | 有 | 有 |
| RedisConfigDefinition | 無 |  無 |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| appliedReservations | 無 |  無 |
| calculatePrice | 無 |  無 |
| catalogs | 無 |  無 |
| commercialReservationOrders | 無 |  無 |
| reservationOrders | 無 |  無 |
| reservationOrders/calculateRefund | 無 |  無 |
| reservationOrders/merge | 無 |  無 |
| reservationOrders/reservations | 無 |  無 |
| reservationOrders/reservations/revisions | 無 |  無 |
| reservationOrders/return | 無 |  無 |
| reservationOrders/split | 無 |  無 |
| reservationOrders/swap | 無 |  無 |
| reservations | 無 |  無 |
| 資源 | 無 |  無 |
| validateReservationOrder | 無 |  無 |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| edgenodes | 無 |  無 |
| 設定檔 | 有 | 有 |
| profiles/endpoints | 有 | 有 |
| profiles/endpoints/customdomains | 無 |  無 |
| profiles/endpoints/origins | 無 |  無 |
| validateProbe | 無 |  無 |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| certificateOrders | 有 | 有 |
| certificateOrders/certificates | 無 |  無 |
| validateCertificateRegistrationInformation | 無 |  無 |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| capabilities | 無 |  無 |
| domainNames | 無 |  無 |
| domainNames/capabilities | 無 |  無 |
| domainNames/internalLoadBalancers | 無 |  無 |
| domainNames/serviceCertificates | 無 |  無 |
| domainNames/slots | 無 |  無 |
| domainNames/slots/roles | 無 |  無 |
| moveSubscriptionResources | 無 |  無 |
| operatingSystemFamilies | 無 |  無 |
| operatingSystems | 無 |  無 |
| quotas | 無 |  無 |
| resourceTypes | 無 |  無 |
| validateSubscriptionMoveAvailability | 無 |  無 |
| virtualMachines | 無 |  無 |
| virtualMachines/diagnosticSettings | 無 |  無 |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | 無 |  無 |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| capabilities | 無 |  無 |
| expressRouteCrossConnections | 無 |  無 |
| expressRouteCrossConnections/peerings | 無 |  無 |
| gatewaySupportedDevices | 無 |  無 |
| networkSecurityGroups | 無 |  無 |
| quotas | 無 |  無 |
| reservedIps | 無 |  無 |
| virtualNetworks | 無 |  無 |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | 無 |  無 |
| virtualNetworks/virtualNetworkPeerings | 無 |  無 |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| capabilities | 無 |  無 |
| 磁碟 | 無 |  無 |
| 映像 | 無 |  無 |
| osImages | 無 |  無 |
| osPlatformImages | 無 |  無 |
| publicImages | 無 |  無 |
| quotas | 無 |  無 |
| storageAccounts | 無 |  無 |
| storageAccounts/services | 無 |  無 |
| storageAccounts/services/diagnosticSettings | 無 |  無 |
| storageAccounts/vmImages | 無 |  無 |
| vmImages | 無 |  無 |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| RateCard | 無 |  無 |
| UsageAggregates | 無 |  無 |

## <a name="microsoftcompute"></a>Microsoft.Compute
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| availabilitySets | 有 | 有 |
| 磁碟 | 有 | 有 |
| 映像 | 有 | 有 |
| restorePointCollections | 有 | 有 |
| restorePointCollections/restorePoints | 無 |  無 |
| sharedVMImages | 有 | 有 |
| sharedVMImages/versions | 有 | 有 |
| 快照集 | 有 | 有 |
| virtualMachines | 有 | 有 |
| virtualMachines/diagnosticSettings | 無 |  無 |
| virtualMachines/extensions | 有 | 有 |
| virtualMachineScaleSets | 有 | 有 |
| virtualMachineScaleSets/extensions | 無 |  無 |
| virtualMachineScaleSets/networkInterfaces | 無 |  無 |
| virtualMachineScaleSets/publicIPAddresses | 無 |  無 |
| virtualMachineScaleSets/virtualMachines | 無 |  無 |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | 無 |  無 |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| AggregatedCost | 無 |  無 |
| 餘額 | 無 |  無 |
| 預算 | 無 |  無 |
| Charges | 無 |  無 |
| CostTags | 無 |  無 |
| credits | 無 |  無 |
| 活動 | 無 |  無 |
| 預測 | 無 |  無 |
| lots | 無 |  無 |
| Marketplace | 無 |  無 |
| Pricesheets | 無 |  無 |
| products | 無 |  無 |
| ReservationDetails | 無 |  無 |
| ReservationRecommendations | 無 |  無 |
| ReservationSummaries | 無 |  無 |
| ReservationTransactions | 無 |  無 |
| Tags | 無 |  無 |
| 使用規定 | 無 |  無 |
| UsageDetails | 無 |  無 |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| containerGroups | 有 | 有 |
| serviceAssociationLinks | 無 |  無 |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| registries | 有 | 有 |
| registries/builds | 無 |  無 |
| registries/builds/cancel | 無 |  無 |
| registries/builds/getLogLink | 無 |  無 |
| registries/buildTasks | 有 | 有 |
| registries/buildTasks/steps | 無 |  無 |
| registries/eventGridFilters | 無 |  無 |
| registries/getBuildSourceUploadUrl | 無 |  無 |
| registries/GetCredentials | 無 |  無 |
| registries/importImage | 無 |  無 |
| registries/queueBuild | 無 |  無 |
| registries/regenerateCredential | 無 |  無 |
| registries/regenerateCredentials | 無 |  無 |
| registries/replications | 有 | 有 |
| registries/runs | 無 |  無 |
| registries/runs/cancel | 無 |  無 |
| registries/scheduleRun | 無 |  無 |
| registries/tasks | 有 | 有 |
| registries/updatePolicies | 無 |  無 |
| registries/webhooks | 有 | 有 |
| registries/webhooks/getCallbackConfig | 無 |  無 |
| registries/webhooks/ping | 無 |  無 |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| containerServices | 有 | 有 |
| managedClusters | 有 | 有 |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 應用程式 | 有 | 有 |
| updateCommunicationPreference | 無 |  無 |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 警示 | 無 |  無 |
| BillingAccounts | 無 |  無 |
| 連接器 | 有 | 有 |
| 部門 | 無 |  無 |
| 維度 | 無 |  無 |
| EnrollmentAccounts | 無 |  無 |
| 查詢 | 無 |  無 |
| 註冊 | 無 |  無 |
| Reportconfigs | 無 |  無 |
| 報告 | 無 |  無 |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| hubs | 有 | 有 |
| hubs/authorizationPolicies | 無 |  無 |
| hubs/connectors | 無 |  無 |
| hubs/connectors/mappings | 無 |  無 |
| hubs/interactions | 無 |  無 |
| hubs/kpi | 無 |  無 |
| hubs/links | 無 |  無 |
| hubs/profiles | 無 |  無 |
| hubs/roleAssignments | 無 |  無 |
| hubs/roles | 無 |  無 |
| hubs/suggestTypeSchema | 無 |  無 |
| hubs/views | 無 |  無 |
| hubs/widgetTypes | 無 |  無 |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 工作 | 有 | 有 |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | 有 | 有 |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| workspaces | 有 | 無 |
| workspaces/virtualNetworkPeerings | 無 |  無 |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| catalogs | 有 | 有 |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| connectionManagers | 有 | 有 |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| dataFactories | 有 | 無 |
| dataFactories/diagnosticSettings | 無 |  無 |
| dataFactorySchema | 無 |  無 |
| factories | 有 | 無 |
| factories/integrationRuntimes | 無 |  無 |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |
| accounts/dataLakeStoreAccounts | 無 |  無 |
| accounts/storageAccounts | 無 |  無 |
| accounts/storageAccounts/containers | 無 |  無 |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |
| accounts/eventGridFilters | 無 |  無 |
| accounts/firewallRules | 無 |  無 |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 服務 | 有 | 有 |
| services/projects | 有 | 有 |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 伺服器 | 有 | 有 |
| servers/recoverableServers | 無 |  無 |
| servers/virtualNetworkRules | 無 |  無 |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 伺服器 | 有 | 有 |
| servers/recoverableServers | 無 |  無 |
| servers/virtualNetworkRules | 無 |  無 |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 伺服器 | 有 | 有 |
| servers/advisors | 無 |  無 |
| servers/queryTexts | 無 |  無 |
| servers/recoverableServers | 無 |  無 |
| servers/topQueryStatistics | 無 |  無 |
| servers/virtualNetworkRules | 無 |  無 |
| servers/waitStatistics | 無 |  無 |

## <a name="microsoftdevices"></a>Microsoft.Devices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| IotHubs | 有 | 有 |
| IotHubs/eventGridFilters | 無 |  無 |
| ProvisioningServices | 有 | 有 |
| usages | 無 |  無 |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| controllers | 有 | 有 |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| labs | 有 | 有 |
| labs/serviceRunners | 有 | 有 |
| labs/virtualMachines | 有 | 有 |
| schedules | 有 | 有 |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| databaseAccountNames | 無 |  無 |
| databaseAccounts | 有 | 有 |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| domains | 有 | 有 |
| domains/domainOwnershipIdentifiers | 無 |  無 |
| generateSsoRequest | 無 |  無 |
| topLevelDomains | 無 |  無 |
| validateDomainRegistrationInformation | 無 |  無 |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| lcsprojects | 無 |  無 |
| lcsprojects/clouddeployments | 無 |  無 |
| lcsprojects/connectors | 無 |  無 |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| domains | 有 | 無 |
| domains/topics | 無 |  無 |
| eventSubscriptions | 無 |  無 |
| extensionTopics | 無 |  無 |
| topics | 有 | 無 |
| topicTypes | 無 |  無 |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| clusters | 有 | 無 |
| 命名空間 | 有 | 無 |
| namespaces/authorizationrules | 無 |  無 |
| namespaces/disasterrecoveryconfigs | 無 |  無 |
| namespaces/eventhubs | 無 |  無 |
| namespaces/eventhubs/authorizationrules | 無 |  無 |
| namespaces/eventhubs/consumergroups | 無 |  無 |

## <a name="microsoftfeatures"></a>Microsoft.Features
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 特性 | 無 |  無 |
| 提供者 | 無 |  無 |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| enroll | 無 |  無 |
| galleryitems | 無 |  無 |
| generateartifactaccessuri | 無 |  無 |
| myareas | 無 |  無 |
| myareas/areas | 無 |  無 |
| myareas/areas/areas | 無 |  無 |
| myareas/areas/areas/galleryitems | 無 |  無 |
| myareas/areas/galleryitems | 無 |  無 |
| myareas/galleryitems | 無 |  無 |
| 註冊 | 無 |  無 |
| 資源 | 無 |  無 |
| retrieveresourcesbyid | 無 |  無 |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | 無 |  無 |
| software | 無 |  無 |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| hanaInstances | 有 |  有 |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| clusters | 有 | 有 |
| clusters/applications | 無 |  無 |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 工作 | 有 | 有 |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| labelGroups | 無 |  無 |
| labelGroups/labels | 無 |  無 |
| labelGroups/labels/conditions | 無 |  無 |
| labelGroups/labels/subLabels | 無 |  無 |
| labelGroups/labels/subLabels/conditions | 無 |  無 |

## <a name="microsoftinsights"></a>microsoft.insights
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| actiongroups | 有 | 有 |
| activityLogAlerts | 有 | 有 |
| alertrules | 有 | 有 |
| automatedExportSettings | 無 |  無 |
| autoscalesettings | 有 | 有 |
| baseline | 無 |  無 |
| calculatebaseline | 無 |  無 |
| components | 有 | 有 |
| components/events | 無 |  無 |
| components/pricingPlans | 無 |  無 |
| components/query | 無 |  無 |
| diagnosticSettings | 無 |  無 |
| diagnosticSettingsCategories | 無 |  無 |
| eventCategories | 無 |  無 |
| eventtypes | 無 |  無 |
| extendedDiagnosticSettings | 無 |  無 |
| logDefinitions | 無 |  無 |
| logprofiles | 無 |  無 |
| 記錄 | 無 |  無 |
| metricAlerts | 有 | 有 |
| migrateToNewPricingModel | 無 |  無 |
| myWorkbooks | 無 |  無 |
| 查詢 | 無 |  無 |
| rollbackToLegacyPricingModel | 無 |  無 |
| scheduledqueryrules | 有 | 有 |
| vmInsightsOnboardingStatuses | 無 |  無 |
| webtests | 有 | 有 |
| workbooks | 有 | 有 |

## <a name="microsoftintune"></a>Microsoft.Intune
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 無 |  無 |
| diagnosticSettingsCategories | 無 |  無 |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| IoTApps | 有 | 有 |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 圖形 | 有 | 有 |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| deletedVaults | 無 |  無 |
| vaults | 有 | 有 |
| vaults/accessPolicies | 無 |  無 |
| vaults/secrets | 無 |  無 |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| clusters | 有 | 有 |
| clusters/databases | 無 |  無 |
| clusters/databases/dataconnections | 無 |  無 |
| clusters/databases/eventhubconnections | 無 |  無 |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| labaccounts | 有 | 有 |
| 使用者 | 無 |  無 |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 記錄 | 無 |  無 |

## <a name="microsoftlogic"></a>Microsoft.Logic
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| integrationAccounts | 有 | 有 |
| workflows | 有 | 有 |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| commitmentPlans | 有 | 有 |
| webServices | 有 | 有 |
| 工作區 | 有 | 有 |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |
| accounts/workspaces | 有 | 有 |
| accounts/workspaces/projects | 有 | 有 |
| teamAccounts | 有 | 有 |
| teamAccounts/workspaces | 有 | 有 |
| teamAccounts/workspaces/projects | 有 | 有 |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| workspaces | 有 | 有 |
| workspaces/computes | 無 |  無 |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 識別身分 | 無 |  無 |
| userAssignedIdentities | 有 | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| getEntities | 無 |  無 |
| managementGroups | 無 |  無 |
| 資源 | 無 |  無 |
| startTenantBackfill | 無 |  無 |
| tenantBackfillStatus | 無 |  無 |

## <a name="microsoftmaps"></a>Microsoft.Maps
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |
| accounts/eventGridFilters | 無 |  無 |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| offers | 無 |  無 |
| offerTypes | 無 |  無 |
| offerTypes/publishers | 無 |  無 |
| offerTypes/publishers/offers | 無 |  無 |
| offerTypes/publishers/offers/plans | 無 |  無 |
| offerTypes/publishers/offers/plans/agreements | 無 |  無 |
| offerTypes/publishers/offers/plans/configs | 無 |  無 |
| offerTypes/publishers/offers/plans/configs/importImage | 無 |  無 |
| privategalleryitems | 無 |  無 |
| products | 無 |  無 |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| classicDevServices | 有 | 有 |
| updateCommunicationPreference | 無 |  無 |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| agreements | 無 |  無 |
| offertypes | 無 |  無 |

## <a name="microsoftmedia"></a>Microsoft.Media
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| mediaservices | 有 | 有 |
| mediaservices/accountFilters | 無 |  無 |
| mediaservices/assets | 無 |  無 |
| mediaservices/assets/assetFilters | 無 |  無 |
| mediaservices/contentKeyPolicies | 無 |  無 |
| mediaservices/eventGridFilters | 無 |  無 |
| mediaservices/liveEventOperations | 無 |  無 |
| mediaservices/liveEvents | 有 | 有 |
| mediaservices/liveEvents/liveOutputs | 無 |  無 |
| mediaservices/liveOutputOperations | 無 |  無 |
| mediaservices/streamingEndpointOperations | 無 |  無 |
| mediaservices/streamingEndpoints | 有 | 有 |
| mediaservices/streamingLocators | 無 |  無 |
| mediaservices/streamingPolicies | 無 |  無 |
| mediaservices/transforms | 無 |  無 |
| mediaservices/transforms/jobs | 無 |  無 |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| projects | 有 | 有 |

## <a name="microsoftnetwork"></a>Microsoft.Network
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| applicationGateways | 有 | 無 |
| applicationSecurityGroups | 有 | 有 |
| azureFirewallFqdnTags | 無 |  無 |
| azureFirewalls | 有 | 無 |
| bgpServiceCommunities | 無 |  無 |
| 連接 | 有 | 有 |
| ddosCustomPolicies | 有 | 有 |
| ddosProtectionPlans | 有 | 有 |
| dnsOperationStatuses | 無 |  無 |
| dnszones | 有 | 有 |
| dnszones/A | 無 |  無 |
| dnszones/AAAA | 無 |  無 |
| dnszones/all | 無 |  無 |
| dnszones/CAA | 無 |  無 |
| dnszones/CNAME | 無 |  無 |
| dnszones/MX | 無 |  無 |
| dnszones/NS | 無 |  無 |
| dnszones/PTR | 無 |  無 |
| dnszones/recordsets | 無 |  無 |
| dnszones/SOA | 無 |  無 |
| dnszones/SRV | 無 |  無 |
| dnszones/TXT | 無 |  無 |
| expressRouteCircuits | 有  | 無 |
| expressRouteServiceProviders | 無 |  無 |
| 前門 | 有 | 有 |
| frontdoorWebApplicationFirewallPolicies | 有 | 有 |
| getDnsResourceReference | 無 |  無 |
| interfaceEndpoints | 有 | 有 |
| internalNotify | 無 |  無 |
| loadBalancers | 有 | 無 |
| localNetworkGateways | 有 | 有 |
| natGateways | 有 | 有 |
| networkIntentPolicies | 有 | 有 |
| networkInterfaces | 有 | 有 |
| networkProfiles | 有 | 有 |
| networkSecurityGroups | 有 | 有 |
| networkWatchers | 有 | 無 |
| networkWatchers/connectionMonitors | 有 | 無 |
| networkWatchers/lenses | 有 | 無 |
| networkWatchers/pingMeshes | 有 | 無 |
| privateLinkServices | 有 | 有 |
| publicIPAddresses | 有 | 有 |
| publicIPPrefixes | 有 | 有 |
| routeFilters | 有 | 有 |
| routeTables | 有 | 有 |
| serviceEndpointPolicies | 有 | 有 |
| trafficManagerGeographicHierarchies | 無 |  無 |
| trafficmanagerprofiles | 有 | 有 |
| trafficmanagerprofiles/heatMaps | 無 |  無 |
| virtualHubs | 有 | 有 |
| virtualNetworkGateways | 有 | 無 |
| virtualNetworks | 有 | 有 |
| virtualNetworks/subnets | 無 |  無 |
| virtualNetworkTaps | 有 | 有 |
| virtualWans | 有 | 有 |
| vpnGateways | 有 | 無 |
| vpnSites | 有 | 有 |
| webApplicationFirewallPolicies | 有 | 有 |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 命名空間 | 有 | 無 |
| namespaces/notificationHubs | 有 | 無 |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 裝置 | 無 |  無 |
| linkTargets | 無 |  無 |
| storageInsightConfigs | 無 |  無 |
| workspaces | 有 | 有 |
| workspaces/dataSources | 無 |  無 |
| workspaces/linkedServices | 無 |  無 |
| workspaces/query | 無 |  無 |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| managementassociations | 無 |  無 |
| managementconfigurations | 有 | 有 |
| solutions | 有 | 有 |
| 檢視 | 有 | 有 |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| policyEvents | 無 |  無 |
| policyStates | 無 |  無 |
| policyTrackedResources | 無 |  無 |
| remediations | 無 |  無 |

## <a name="microsoftportal"></a>Microsoft.Portal
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| consoles | 無 |  無 |
| dashboards | 有 | 有 |
| userSettings | 無 |  無 |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| workspaceCollections | 有 | 有 |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| capacities | 有 | 有 |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| backupProtectedItems | 無 |  無 |
| vaults | 有 | 有 |

## <a name="microsoftrelay"></a>Microsoft.Relay
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 命名空間 | 有 | 有 |
| namespaces/authorizationrules | 無 |  無 |
| namespaces/hybridconnections | 無 |  無 |
| namespaces/hybridconnections/authorizationrules | 無 |  無 |
| namespaces/wcfrelays | 無 |  無 |
| namespaces/wcfrelays/authorizationrules | 無 |  無 |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 資源 | 無 |  無 |
| subscriptionsStatus | 無 |  無 |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| availabilityStatuses | 無 |  無 |
| childAvailabilityStatuses | 無 |  無 |
| childResources | 無 |  無 |
| 活動 | 無 |  無 |
| impactedResources | 無 |  無 |
| 通知 | 無 |  無 |

## <a name="microsoftresources"></a>Microsoft.Resources
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 部署 | 無 |  無 |
| deployments/operations | 無 |  無 |
| 連結 | 無 |  無 |
| notifyResourceJobs | 無 |  無 |
| 提供者 | 無 |  無 |
| resourceGroups | 無 |  無 |
| 資源 | 無 |  無 |
| 訂用帳戶 | 無 |  無 |
| subscriptions/providers | 無 |  無 |
| subscriptions/resourceGroups | 無 |  無 |
| subscriptions/resourcegroups/resources | 無 |  無 |
| subscriptions/resources | 無 |  無 |
| subscriptions/tagnames | 無 |  無 |
| subscriptions/tagNames/tagValues | 無 |  無 |
| tenants | 無 |  無 |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 應用程式 | 有 | 有 |
| saasresources | 無 |  無 |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 流量 | 有 | 有 |
| jobcollections | 有 | 有 |

## <a name="microsoftsearch"></a>Microsoft.Search
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | 無 |  無 |
| searchServices | 有 | 有 |

## <a name="microsoftsecurity"></a>Microsoft.Security
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | 無 |  無 |
| 警示 | 無 |  無 |
| allowedConnections | 無 |  無 |
| appliances | 無 |  無 |
| applicationWhitelistings | 無 |  無 |
| AutoProvisioningSettings | 無 |  無 |
| Compliances | 無 |  無 |
| dataCollectionAgents | 無 |  無 |
| discoveredSecuritySolutions | 無 |  無 |
| externalSecuritySolutions | 無 |  無 |
| InformationProtectionPolicies | 無 |  無 |
| jitNetworkAccessPolicies | 無 |  無 |
| monitoring | 無 |  無 |
| monitoring/antimalware | 無 |  無 |
| monitoring/baseline | 無 |  無 |
| monitoring/patch | 無 |  無 |
| 原則 | 無 |  無 |
| pricings | 無 |  無 |
| securityContacts | 無 |  無 |
| securitySolutions | 無 |  無 |
| securitySolutionsReferenceData | 無 |  無 |
| securityStatus | 無 |  無 |
| securityStatus/endpoints | 無 |  無 |
| securityStatus/subnets | 無 |  無 |
| securityStatus/virtualMachines | 無 |  無 |
| securityStatuses | 無 |  無 |
| securityStatusesSummaries | 無 |  無 |
| 設定 | 無 |  無 |
| 工作 | 無 |  無 |
| topologies | 無 |  無 |
| workspaceSettings | 無 |  無 |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 無 |  無 |
| diagnosticSettingsCategories | 無 |  無 |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 命名空間 | 有 | 無 |
| namespaces/authorizationrules | 無 |  無 |
| namespaces/disasterrecoveryconfigs | 無 |  無 |
| namespaces/eventgridfilters | 無 |  無 |
| namespaces/queues | 無 |  無 |
| namespaces/queues/authorizationrules | 無 |  無 |
| namespaces/topics | 無 |  無 |
| namespaces/topics/authorizationrules | 無 |  無 |
| namespaces/topics/subscriptions | 無 |  無 |
| namespaces/topics/subscriptions/rules | 無 |  無 |
| premiumMessagingRegions | 無 |  無 |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| clusters | 有 | 有 |
| clusters/applications | 無 |  無 |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 應用程式 | 有 | 有 |
| gateways | 有 | 有 |
| 網路 | 有 | 有 |
| 祕密 | 有 | 有 |
| 磁碟區 | 有 | 有 |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| SignalR | 有 | 有 |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| applianceDefinitions | 有 | 有 |
| appliances | 有 | 有 |
| applicationDefinitions | 有 | 有 |
| 應用程式 | 有 | 有 |
| jitRequests | 有 | 有 |

## <a name="microsoftsql"></a>Microsoft.SQL
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| managedInstances | 有 | 有 |
| managedInstances/databases | 是 (請參閱下方注意事項) | 有 |
| managedInstances/databases/backupShortTermRetentionPolicies | 無 | 無 |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | 無 | 無 |
| managedInstances/databases/vulnerabilityAssessments | 無 | 無 |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | 無 | 無 |
| managedInstances/encryptionProtector | 無 | 無 |
| managedInstances/keys | 無 | 無 |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | 無 | 無 |
| managedInstances/vulnerabilityAssessments | 無 | 無 |
| 伺服器 | 有 | 有 |
| servers/administrators | 無 |  無 |
| servers/communicationLinks | 無 |  無 |
| servers/databases | 是 (請參閱下方注意事項) | 有 |
| servers/encryptionProtector | 無 |  無 |
| servers/firewallRules | 無 |  無 |
| servers/keys | 無 |  無 |
| servers/restorableDroppedDatabases | 無 |  無 |
| servers/serviceobjectives | 無 |  無 |
| servers/tdeCertificates | 無 |  無 |

> [!NOTE]
> Master 資料庫不支援標籤，但其他資料庫 (包括 Azure SQL 資料倉儲資料庫) 則可支援標籤。 Azure SQL 資料倉儲資料庫的狀態必須是 [作用中] \(不是 [暫停]\)。


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | 有 | 有 |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | 無 |  無 |
| SqlVirtualMachines | 有 | 有 |

## <a name="microsoftstorage"></a>Microsoft.Storage
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| storageAccounts | 有 | 有 |
| storageAccounts/blobServices | 無 |  無 |
| storageAccounts/fileServices | 無 |  無 |
| storageAccounts/queueServices | 無 |  無 |
| storageAccounts/services | 無 |  無 |
| storageAccounts/tableServices | 無 |  無 |
| usages | 無 |  無 |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| storageSyncServices | 有 | 有 |
| storageSyncServices/registeredServers | 無 |  無 |
| storageSyncServices/syncGroups | 無 |  無 |
| storageSyncServices/syncGroups/cloudEndpoints | 無 |  無 |
| storageSyncServices/syncGroups/serverEndpoints | 無 |  無 |
| storageSyncServices/workflows | 無 |  無 |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| managers | 有 | 有 |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| streamingjobs | 是 (請參閱下方注意事項) | 有 |
| streamingjobs/diagnosticSettings | 無 |  無 |

> [!NOTE]
> 您無法在 streamingjobs 執行時新增標記。 阻止資源新增標記。

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| CreateSubscription | 無 |  無 |
| SubscriptionDefinitions | 無 |  無 |
| SubscriptionOperations | 無 |  無 |

## <a name="microsoftsupport"></a>microsoft.support
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| supporttickets | 無 |  無 |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| providerRegistrations | 有 | 有 |
| 資源 | 有 | 有 |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| environments | 有 | 無 |
| environments/accessPolicies | 無 |  無 |
| environments/eventsources | 有 | 無 |
| environments/referenceDataSets | 有 | 無 |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| 帳戶 | 有 | 有 |
| account/extension | 有 | 有 |
| account/project | 有 | 有 |

## <a name="microsoftweb"></a>Microsoft.Web
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | 無 |  無 |
| apiManagementAccounts/apiAcls | 無 |  無 |
| apiManagementAccounts/apis | 無 |  無 |
| apiManagementAccounts/apis/apiAcls | 無 |  無 |
| apiManagementAccounts/apis/connectionAcls | 無 |  無 |
| apiManagementAccounts/apis/connections | 無 |  無 |
| apiManagementAccounts/apis/connections/connectionAcls | 無 |  無 |
| apiManagementAccounts/apis/localizedDefinitions | 無 |  無 |
| apiManagementAccounts/connectionAcls | 無 |  無 |
| apiManagementAccounts/connections | 無 |  無 |
| billingMeters | 無 |  無 |
| 憑證 | 有 | 有 |
| connectionGateways | 有 | 有 |
| 連接 | 有 | 有 |
| customApis | 有 | 有 |
| deletedSites | 無 |  無 |
| 函數 | 無 |  無 |
| hostingEnvironments | 有 | 無 |
| hostingEnvironments/multiRolePools | 無 |  無 |
| hostingEnvironments/multiRolePools/instances | 無 |  無 |
| hostingEnvironments/workerPools | 無 |  無 |
| hostingEnvironments/workerPools/instances | 無 |  無 |
| publishingUsers | 無 |  無 |
| 建議 | 無 |  無 |
| resourceHealthMetadata | 無 |  無 |
| runtimes | 無 |  無 |
| serverFarms | 有 | 有 |
| serverFarms/workers | 無 |  無 |
| 網站 | 有 | 有 |
| sites/domainOwnershipIdentifiers | 無 |  無 |
| sites/hostNameBindings | 無 |  無 |
| sites/instances | 無 |  無 |
| sites/instances/extensions | 無 |  無 |
| sites/premieraddons | 有 | 有 |
| sites/recommendations | 無 |  無 |
| sites/resourceHealthMetadata | 無 |  無 |
| sites/slots | 有 | 有 |
| sites/slots/hostNameBindings | 無 |  無 |
| sites/slots/instances | 無 |  無 |
| sites/slots/instances/extensions | 無 |  無 |
| sourceControls | 無 |  無 |
| validate | 無 |  無 |
| verifyHostingEnvironmentVnet | 無 |  無 |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| diagnosticSettings | 無 |  無 |
| diagnosticSettingsCategories | 無 |  無 |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| DeviceServices | 有 | 有 |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| 資源類型 | 支援標記 | 在成本報表中標記 |
| ------------- | ----------- | ----------- |
| components | 無 |  無 |
| componentsSummary | 無 |  無 |
| monitorInstances | 無 |  無 |
| monitorInstancesSummary | 無 |  無 |
| monitors | 無 |  無 |
| notificationSettings | 無 |  無 |

## <a name="next-steps"></a>後續步驟
若要了解如何將標記套用至資源，請參閱[使用標記來組織您的 Azure 資源](resource-group-using-tags.md)。
