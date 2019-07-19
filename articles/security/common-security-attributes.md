---
title: Azure 服務的安全性屬性
description: 用來評估 Azure Service Fabric 的常見安全性屬性檢查清單
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: d45e28175412b574432adb59cf700568c9a7fb39
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304258"
---
# <a name="security-attributes-for-azure-services"></a>Azure 服務的安全性屬性

本文會收集所選 Azure 服務的常見安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="api-managementapi-managementapi-management-security-attributesmd"></a>[API 管理](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 機密資料 (例如憑證、金鑰和密碼名稱值) 會使用服務管理的每個服務實例金鑰進行加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | [Express Route](../expressroute/index.yml)和 VNet 加密是由[Azure 網路](../virtual-network/index.yml)提供。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 | 所有加密金鑰都是針對每個服務實例, 而且受服務管理。 |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 管理平面呼叫是透過 TLS 的[Azure Resource Manager](../azure-resource-manager/index.yml)來進行。 需要有效的 JSON Web 權杖 (JWT)。  您可以使用 TLS 和支援的其中一種驗證機制 (例如用戶端憑證或 JWT) 來保護資料平面呼叫。
 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 是 | 分別使用網路安全性群組 (NSG) 和 Azure 應用程式閘道 (或其他軟體應用裝置)。 |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | |
| Authorization| 是 | |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | [Azure 監視器活動記錄](../azure-monitor/platform/activity-logs-overview.md) |
| 資料平面記錄和審核| 是 | [Azure 監視器診斷記錄](../azure-monitor/platform/diagnostic-logs-overview.md)和 (選擇性) [Azure 應用程式深入](../azure-monitor/app/app-insights-overview.md)解析。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 使用[AZURE API 管理 DevOps 資源套件](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>弱點掃描誤報

本節記載常見的弱點, 這不會影響 Azure API 管理。

| 弱點               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed 是在某些 F5 產品中找到的 TLS SessionTicket 擴充功能的弱點。 它允許從未初始化的記憶體中, 最多31個位元組的資料洩漏 (「不規則」)。 這是由 TLS 堆疊填補從用戶端傳遞的會話識別碼所造成, 其資料會使其32位長。 |


## <a name="app-serviceapp-serviceapp-service-security-attributesmd"></a>[App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 網站檔案內容會儲存在 Azure 儲存體中, 這會自動將待用內容加密。 請參閱待用[資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。<br><br>客戶提供的秘密會在待用時加密。 秘密會在待用時加密, 並儲存在 App Service 設定資料庫中。<br><br>網站 (D:\local 和% TMP%) 可以選擇性地使用本機連接的磁片做為暫時儲存。 本機連接的磁片不會在待用時加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 客戶可以將網站設定為要求並使用 HTTPS 來輸入流量。 請參閱 blog 文章[如何僅讓 AZURE APP SERVICE HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶可以選擇將應用程式秘密儲存在 Key Vault 中, 並在執行時間取得它們。 請參閱[使用 App Service 和 Azure Functions 的 Key Vault 參考 (預覽)](../app-service/app-service-key-vault-references.md)。|
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS [Azure Resource Manager](../azure-resource-manager/index.yml)呼叫來設定 App Service 進行管理呼叫。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 目前可在 App Service 預覽中使用。 請參閱[Azure App Service 的存取限制](../app-service/app-service-ip-restrictions.md)。 |
| VNet 插入支援| 是 | App Service 環境是專用於單一客戶的 App Service 私用, 並插入客戶的虛擬網路中。 請參閱[App Service 環境簡介](../app-service/environment/intro.md)。 |
| 網路隔離和防火牆支援| 是 | 針對 App Service 的公用多租使用者變化, 客戶可以設定網路 Acl (IP 限制) 來鎖定允許的輸入流量。  請參閱[Azure App Service 的存取限制](../app-service/app-service-ip-restrictions.md)。  App Service 環境會直接部署到虛擬網路中, 因此可以使用 Nsg 來保護。 |
| 強制通道支援| 是 | App Service 環境可以部署到客戶的虛擬網路, 其中已設定強制通道。 客戶必須遵循[使用強制通道設定您的 App Service 環境](../app-service/environment/forced-tunnel-support.md)中的指示。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | App Service 與支援 Application Insights 的語言 Application Insights 整合 (完整 .NET Framework、.NET Core、JAVA 和 NODE.JS)。  請參閱[監視 Azure App Service 效能](../azure-monitor/app/azure-web-apps.md)。 App Service 也會將應用程式計量傳送至 Azure 監視器。 請參閱[監視 Azure App Service 中的應用程式](../app-service/web-sites-monitor.md)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 客戶可以在自動與[Azure Active Directory (Azure AD)](../active-directory/index.yml)以及其他與 OAuth 相容身分識別提供者整合的 App Service 上建立應用程式;請參閱[Azure App Service 中的驗證和授權](../app-service/overview-authentication-authorization.md)。 對於 App Service 資產的管理存取權, 所有存取都是由 Azure AD 已驗證主體和 Azure Resource Manager RBAC 角色的組合來控制。 |
| Authorization| 是 | 對於 App Service 資產的管理存取權, 所有存取都是由 Azure AD 已驗證主體和 Azure Resource Manager RBAC 角色的組合來控制。  |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 在 App Service 物件上執行的所有管理作業都是透過[Azure Resource Manager](../azure-resource-manager/index.yml)進行。 這些作業的歷程記錄可以在入口網站中和透過 CLI 取得。請參閱[Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftweb)和[az monitor activity-log](/cli/azure/monitor/activity-log)。 |
| 資料平面記錄和審核 | 否 | App Service 的資料平面是遠端檔案共用, 其中包含客戶已部署的網站內容。  不會對遠端檔案共用進行任何審核。 |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 針對管理作業, App Service 設定的狀態可以匯出為 Azure Resource Manager 範本, 並在一段時間後進行版本設定。 針對執行時間作業, 客戶可以使用 App Service 部署位置 功能來維護應用程式的多個不同即時版本。 | 



## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | HTTPS/TLS。 |
| 加密金鑰處理 (CMK、BYOK 等)| N/A | Azure Resource Manager 不會儲存任何客戶內容, 只會儲存控制資料。 |
| 資料行層級加密 (Azure 資料服務)| 是 | |
| API 呼叫加密| 是 | |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離和防火牆支援| 否 |  |
| 強制通道支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 否 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 以[Azure Active Directory](/azure/active-directory)為基礎。|
| Authorization| 是 | |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 活動記錄會公開您資源上執行的所有寫入作業 (PUT、POST、DELETE);請參閱[查看活動記錄以在資源上審核動作](../azure-resource-manager/resource-group-audit.md)。 |
| 資料平面記錄和審核| N/A | |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure 備份](../backup/backup-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能)| 是 | 對儲存體帳戶使用儲存體服務加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 否 | 使用 HTTPS。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| 否 |  |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| VNet 插入支援| 否 |  |
| 網路隔離和防火牆支援| 是 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |
| 強制通道支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 透過診斷記錄支援 Log Analytics。 如需詳細資訊, 請參閱[使用 Log Analytics 監視 Azure 備份受保護的工作負載](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| Authorization| 是 | 會使用建立客戶和內建的 RBAC 角色。 如需詳細資訊, 請參閱[使用以角色為基礎的存取控制來管理 Azure 備份復原點](/azure/backup/backup-rbac-rs-vault)。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |
| 資料平面記錄和審核| 否 | 無法直接存取 Azure 備份資料平面。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是|  |

## <a name="cosmos-dbcosmos-dbcosmos-db-security-attributesmd"></a>[Cosmos DB](../cosmos-db/cosmos-db-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 預設會加密所有 Cosmos DB 資料庫和備份;請參閱[Azure Cosmos DB 中的資料加密](../cosmos-db/database-encryption-at-rest.md)。 不支援使用客戶管理的金鑰進行伺服器端加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、Vnet 加密中和 VNet VNet 加密)| 是 | 所有 Azure Cosmos DB 資料都會在傳輸時加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| 是 | 僅適用于資料表 API Premium。 並非所有 Api 都支援這項功能。 請[參閱 Azure Cosmos DB 簡介:資料表 API](../cosmos-db/table-introduction.md)。 |
| API 呼叫加密| 是 | Azure Cosmos DB 的所有連接都支援 HTTPS。 Azure Cosmos DB 也支援 TLS 1.2 連線, 但尚未強制執行。 如果客戶在其端關閉了較低層級的 TLS, 則可以確保連線到 Cosmos DB。  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| vNET 插入支援| 是 | 使用 VNet 服務端點, 您可以設定 Azure Cosmos DB 帳戶, 只允許從虛擬網路 (VNet) 的特定子網進行存取。 您也可以結合 VNet 存取與防火牆規則。  請參閱[從虛擬網路存取 Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md)。 |
| 網路隔離和防火牆支援| 是 | 有了防火牆支援, 您可以設定 Azure Cosmos 帳戶, 只允許來自一組已核准的 IP 位址、IP 位址和/或雲端服務的某個範圍的存取權。 請參閱[在 Azure Cosmos DB 中設定 IP 防火牆](../cosmos-db/how-to-configure-firewall.md)。|
| 支援強制通道 | 是 | 可以在虛擬機器所在的 VNET 上, 于用戶端設定。   |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 所有傳送至 Azure Cosmos DB 的要求都會被記錄下來。 支援[Azure 監視](../azure-monitor/overview.md)、azure 計量、Azure Audit 記錄。  您可以記錄對應于資料平面要求、查詢執行時間統計資料、查詢文字、MongoDB 要求的資訊。 您也可以設定警示。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 是, 在資料庫帳戶層級;在資料平面層級, Cosmos DB 會使用資源權杖和金鑰存取。 |
| Authorization| 是 | 在具有主要金鑰 (主要和次要) 和資源權杖的 Azure Cosmos 帳戶上受到支援。 您可以使用主要金鑰來取得資料的讀取/寫入或唯讀存取權。 資源權杖允許限時存取檔和容器之類的資源。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| 是 | 適用于帳戶層級作業 (例如防火牆、Vnet、金鑰存取和 IAM) 的 Azure 活動記錄。 |
| 資料平面記錄和稽核 | 是 | 容器層級作業的診斷監視記錄, 例如建立容器、布建輸送量、編制索引原則, 以及檔上的 CRUD 作業。 |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 否  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB 的其他安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 跨原始來源資源分享 (CORS) | 是 | 請參閱[設定跨原始來源資源分享 (CORS)](../cosmos-db/how-to-configure-cross-origin-resource-sharing.md)。 |


## <a name="event-hubsevent-hubsevent-hubs-security-attributesmd"></a>[事件中樞](../event-hubs/event-hubs-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) |  是 | |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| vNET 插入支援| 否 | |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | |
| Authorization|  是 | |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核| 是 |   |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | |


## <a name="expressrouteexpressrouteexpressroute-security-attributesmd"></a>[ExpressRoute](../expressroute/expressroute-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) |  N/A | ExpressRoute 不會儲存客戶資料。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 否 | |
| 加密金鑰處理 (CMK、BYOK 等)| N/A |  |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A |  |
| vNET 插入支援| N/A | |
| 網路隔離和防火牆支援| 是 | 每個客戶都包含在自己的路由網域中, 並通道傳送至自己的 VNet |
| 強制通道支援| N/A | Via 邊界閘道協定 (BGP)。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[ExpressRoute 監視、計量和警示](../expressroute/expressroute-monitoring-metrics-alerts.md)。|

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 適用于 Microsoft 的閘道服務帳戶 (GWM) (控制器);適用于 Dev 和 OP 的即時 (JIT) 存取。 |
| Authorization|  是 |適用于 Microsoft 的閘道服務帳戶 (GWM) (控制器);適用于 Dev 和 OP 的即時 (JIT) 存取。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意| 
|---|---|--|
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核| 否 |   |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過網路資源提供者 (NRP)。 |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[金鑰保存庫](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 所有物件都會加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶會控制其 Key Vault 中的所有金鑰。 當指定硬體安全模組 (HSM) 所支援的金鑰時, FIPS Level 2 HSM 會保護金鑰、憑證或密碼。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | 使用 HTTPS。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 使用虛擬網路 (VNet) 服務端點。 |
| VNet 插入支援| 否 |  |
| 網路隔離和防火牆支援| 是 | 使用 VNet 防火牆規則。 |
| 強制通道支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 使用 Log Analytics。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| Authorization| 是 | 使用 Key Vault 存取原則。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面記錄和審核| 是 | 使用 Log Analytics。 |

### <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[負載平衡器](../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | N/A | |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| N/A | |
| 加密金鑰處理 (CMK、BYOK 等)| N/A | |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | . |
| 網路隔離和防火牆支援| N/A |  |
| 強制通道支援| N/A | |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[公用基本 Load Balancer 的 Azure 監視器記錄](../load-balancer/load-balancer-monitor-log.md)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| N/A |  |
| Authorization| N/A |  |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 請參閱[公用基本 Load Balancer 的 Azure 監視器記錄](../load-balancer/load-balancer-monitor-log.md)。 |
| 資料平面記錄和審核 | N/A |  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| N/A |  | 

## <a name="service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[服務匯流排訊息](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) |  [是] 表示預設會使用伺服器端靜態加密。 | 尚不支援客戶管理的金鑰和 BYOK。 用戶端的加密是用戶端的責任 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 支援標準 HTTPS/TLS 機制。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |   |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | API 呼叫是透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS 進行。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 (僅限 Premium 層) | 只有[服務匯流排](../service-bus-messaging/service-bus-premium-messaging.md)進階層支援 VNet 服務端點。 |
| VNet 插入支援| 否 | |
| 網路隔離和防火牆支援| 是 (僅限 Premium 層) |  |
| 強制通道支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 透過[Azure 監視器和警示](../service-bus-messaging/service-bus-metrics-azure-monitor.md)來支援。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 透過[Azure Active Directory 受控服務識別](../service-bus-messaging/service-bus-managed-service-identity.md)進行管理;請參閱[服務匯流排驗證和授權](../service-bus-messaging/service-bus-authentication-and-authorization.md)。|
| Authorization| 是 | 支援透過[RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (預覽) 和 SAS 權杖進行授權;請參閱[服務匯流排驗證和授權](../service-bus-messaging/service-bus-authentication-and-authorization.md)。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 作業記錄可供使用;請參閱[服務匯流排診斷記錄](../service-bus-messaging/service-bus-diagnostic-logs.md)。  |
| 資料平面記錄和審核| 否 |  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過[AZURE RESOURCE MANAGER API](/rest/api/resources/)支援資源提供者版本設定。|


## <a name="service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[服務匯流排轉送](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) |  N/A | 轉送是 web 通訊端, 不會保存資料。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 服務需要 TLS。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 | 僅使用 Microsoft TLS 憑證。  |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | IP-HTTPS. |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| 網路隔離和防火牆支援| 否 |  |
| 強制通道支援| N/A | 轉送是 TLS 通道  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 透過 SAS。 |
| Authorization|  是 | 透過 SAS。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |
| 資料平面記錄和審核| 是 | 連接成功/失敗及已記錄的錯誤。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 客戶擁有叢集, 以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 客戶擁有叢集, 以及用來建立叢集的虛擬機器擴展集。 可以在虛擬機器擴展集上啟用 Azure 磁片加密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 |  |
| 網路隔離和防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 強制通道支援| 是 | Azure 網路提供強制通道功能。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 使用 Azure 監視支援和協力廠商支援。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| Authorization| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和審核| N/A | 客戶擁有叢集。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../sql-database/sql-database-security-attributes.md)

SQL Database 包括[單一資料庫](../sql-database/sql-database-single-index.yml)和[受控實例](../sql-database/sql-database-managed-instance.md)。 下列專案適用于這兩個供應專案, 除非另有注明。

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 如[Always Encrypted](../sql-database/sql-database-always-encrypted.md)一文所述, 稱為「加密使用中」。 伺服器端加密會使用[透明資料加密](../sql-database/transparent-data-encryption-azure-sql.md)。|
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 使用 HTTPS。 |
| 加密金鑰處理, 例如 CMK 或 BYOK| 是 | 提供服務管理和客戶管理的金鑰處理。 後者會透過[Azure Key Vault](../key-vault/index.yml)來提供。 |
| Azure data services 提供的資料行層級加密| 是 | 透過[Always Encrypted](../sql-database/sql-database-always-encrypted.md)。 |
| 加密的 API 呼叫| 是 | 使用 HTTPS/SSL。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 僅適用于[單一資料庫](../sql-database/sql-database-single-index.yml)。 |
| Azure 虛擬網路插入支援| 是 | 僅適用于[受控實例](../sql-database/sql-database-managed-instance.md)。 |
| 網路隔離和防火牆支援| 是 | 資料庫層級和伺服器層級的防火牆。 網路隔離僅適用于[受控實例](../sql-database/sql-database-managed-instance.md)。 |
| 強制通道支援| 是 | 經由[ExpressRoute](../expressroute/index.yml) VPN 的[受控實例](../sql-database/sql-database-managed-instance.md)。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援, 例如 Log Analytics 或 Application Insights| 是 | SecureSphere (來自 Imperva 的 SIEM 解決方案) 也透過[SQL 審核](../sql-database/sql-database-auditing.md) [Azure 事件中樞](../event-hubs/index.yml)整合來支援。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory (Azure AD) |
| Authorization| 是 | None |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制平面和管理平面記錄和審核| 是 | 是, 僅適用于某些事件 |
| 資料平面記錄和審核 | 是 | 透過[SQL audit](../sql-database/sql-database-auditing.md) |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援, 例如設定的版本設定| 否  | None |

### <a name="additional-security-attributes-for-sql-database"></a>SQL Database 的其他安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 預防性: 弱點評估 | 是 | 請參閱[SQL 弱點評定服務協助您識別資料庫弱點](../sql-database/sql-vulnerability-assessment.md)。 |
| 預防性: 資料探索與分類  | 是 | 請參閱[Azure SQL Database 和 SQL 資料倉儲資料探索 & 分類](../sql-database/sql-database-data-discovery-and-classification.md)。 |
| 偵測: 威脅偵測 | 是 | 請參閱[Azure SQL Database 的 Advanced 威脅防護](../sql-database/sql-database-threat-detection-overview.md)。 |


## <a name="storagestoragecommonstorage-security-attributesmd"></a>[儲存體](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 |  |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密) | 是 | 支援標準 HTTPS/TLS 機制。  使用者也可以在將資料傳送至服務之前, 將其加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 請參閱[儲存體服務加密在 Azure Key Vault 中使用客戶管理的金鑰](../storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| N/A |  |
| 網路隔離和防火牆支援| 是 | |
| 強制通道支援| N/A |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 現在已可使用 Azure 監視器計量, 記錄開始預覽 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | Azure Active Directory、共用金鑰、共用存取權杖。 |
| Authorization| 是 | 透過 RBAC、POSIX Acl 和 SAS 權杖支援授權 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核 | 是 | Azure Resource Manager 活動記錄 |
| 資料平面記錄和審核| 是 | 服務診斷記錄, 以及開始預覽 Azure 監視器記錄  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 透過 Azure Resource Manager Api 支援資源提供者版本設定 |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>虛擬機器和虛擬機器擴展集

[Linux vm](../virtual-machines/windows/virtual-machines-windows-security-attributes.md)  |  [Windows vm](../virtual-machines/windows/virtual-machines-windows-security-attributes.md)  | [虛擬機器擴展集](../virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes.md)


### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 瞭解[如何在 Azure 中將 Linux 虛擬機器加密](/azure/virtual-machines/linux/encrypt-disks), 以及如何[加密 Windows VM 上的虛擬磁片](/azure/virtual-machines/windows/encrypt-disks)。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | Azure 虛擬機器支援[ExpressRoute](/azure/expressroute)和 VNET 加密。 請參閱[Vm 中的傳輸中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶管理的金鑰是支援的 Azure 加密案例;請參閱[Azure 加密總覽](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS 和 SSL。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | |
| VNet 插入支援| 是 | . |
| 網路隔離和防火牆支援| 是 |  |
| 強制通道支援| 是 | 請參閱[使用 Azure Resource Manager 部署模型設定強制通道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[在 azure 中監視和更新 Linux 虛擬機器](/azure/virtual-machines/linux/tutorial-monitoring)和[在 azure 中監視和更新 Windows 虛擬機器](/azure/virtual-machines/windows/tutorial-monitoring)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 |  |
| Authorization| 是 |  |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 |  |
| 資料平面記錄和審核 | 否 |  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 |  | 


## <a name="vpn-gatewayvpn-gatewayvpn-gateway-security-attributesmd"></a>[VPN 閘道](../vpn-gateway/vpn-gateway-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | N/A | VPN 閘道傳輸客戶資料, 不會儲存客戶資料 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | VPN 閘道會加密 Azure VPN 閘道與客戶內部部署 VPN 裝置 (S2S) 或 VPN 用戶端 (P2S) 之間的客戶封包。 VPN 閘道也支援 VNet 對 VNet 加密。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 | 客戶指定的預先共用金鑰會在待用時加密;但尚未與 CMK 整合。 |
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| N/A | |
| VNet 插入支援| N/A | . |
| 網路隔離和防火牆支援| 是 | VPN 閘道是每個客戶的專用 VM 實例虛擬網路  |
| 強制通道支援| 是 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | 請參閱[Azure 監視器診斷記錄/警示](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) &  [Azure 監視器計量/警示](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 用於管理服務和設定 Azure VPN 閘道的[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) 。 |
| Authorization| 是 | 透過[RBAC](../role-based-access-control/overview.md)支援授權。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | Azure Resource Manager 活動記錄。 |
| 資料平面記錄和審核 | 是 | Azure 監視器 VPN 連線記錄和審核的[診斷記錄](../azure-resource-manager/resource-group-audit.md)。 |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 針對管理作業, Azure VPN 閘道設定的狀態可以匯出為 Azure Resource Manager 範本, 並在一段時間後進行版本設定。 | 

