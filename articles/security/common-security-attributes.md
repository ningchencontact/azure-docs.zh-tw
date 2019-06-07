---
title: Azure 服務的安全性屬性
description: 用來評估 Azure Service Fabric 的常見安全性屬性檢查清單
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474546"
---
# <a name="security-attributes-for-azure-services"></a>Azure 服務的安全性屬性

這篇文章會收集所選的 Azure 服務的常見安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API 管理](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 （只在服務端加密） | 機密資料，例如憑證、 金鑰和祕密的值會以加密受服務管理，每個服務執行個體索引鍵。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | [Express Route](../expressroute/index.yml)由所提供 VNet 加密[Azure 網路](../virtual-network/index.yml)。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 | 所有加密金鑰是每個服務執行個體，受管理的服務。 |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 管理平面呼叫都會經過[Azure Resource Manager](../azure-resource-manager/index.yml)透過 TLS。 需要有效的 JSON Web 權杖 (JWT)。  資料平面的呼叫可以保護使用 TLS 和其中一個支援的驗證機制 （例如用戶端憑證或 JWT）。
 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離，而且防火牆支援| 是 | 使用網路安全性群組 (NSG) 和 Azure 應用程式閘道 （或其他軟體應用裝置） 分別。 |
| 強制通道的支援| 是 | Azure 網路提供強制通道功能。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | |
| 授權| 是 | |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | [Azure 監視器活動記錄檔](../azure-monitor/platform/activity-logs-overview.md) |
| 資料平面記錄與稽核| 是 | [Azure 監視器的診斷記錄](../azure-monitor/platform/diagnostic-logs-overview.md)及 （選擇性） [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 使用[Azure API 管理 DevOps Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 靜態加密 （例如伺服器端加密，使用客戶管理的金鑰，伺服器端加密和其他加密功能） | 是 | 網站檔案內容會儲存在 Azure 儲存體，這會自動加密待用內容。 請參閱[待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。<br><br>提供的客戶機密資料皆在待用加密。 密碼會加密待用時儲存在應用程式服務組態資料庫。<br><br>在本機連接的磁碟 （選擇性） 可用來當做暫存儲存體的網站 （D:\local 和 %TMP%)。 在本機連接的磁碟不會進行待用加密。 |
| （例如 ExpressRoute 加密，在 VNet 加密和 VNet 對 VNet 加密） 的傳輸中加密| 是 | 客戶可以設定 web sites 設定成需要與輸入流量使用 HTTPS。 請參閱部落格文章[如何讓 Azure 應用程式服務僅限 HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 客戶可以選擇將應用程式祕密儲存在金鑰保存庫，並在執行階段擷取它們。 請參閱[App Service 和 Azure Functions （預覽） 會使用 Key Vault 參考](../app-service/app-service-key-vault-references.md)。|
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | 若要設定 App Service 管理呼叫發生透過[Azure Resource Manager](../azure-resource-manager/index.yml)透過 HTTPS 的呼叫。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 目前可供預覽的 App Service。 請參閱[Azure App Service 的存取限制](../app-service/app-service-ip-restrictions.md)。 |
| VNet 插入支援| 是 | App Service 環境都是以插入客戶的虛擬網路的單一客戶專用的 App service 的私用實作。 請參閱[App Service Environment 簡介](../app-service/environment/intro.md)。 |
| 網路隔離和 Firewalling 支援| 是 | App Service 的公用多租用戶變化，客戶可以設定網路 Acl （IP 限制） 鎖定允許輸入流量。  請參閱[Azure App Service 的存取限制](../app-service/app-service-ip-restrictions.md)。  App Service 環境會直接部署到虛擬網路，因此您可以使用 Nsg 保護。 |
| 強制通道的支援| 是 | App Service Environment 可以部署到客戶的強制通道設定所在的虛擬網路中。 客戶必須依照[設定您的 App Service Environment 使用強制通道](../app-service/environment/forced-tunnel-support.md)。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | App Service 整合 Application Insights 的支援 （完整的.NET Framework、.NET Core、 Java 和 Node.JS） 的 Application Insights 的語言。  請參閱[監視 Azure App Service 的效能](../azure-monitor/app/azure-web-apps.md)。 App Service 也會傳送至 「 Azure 監視器的應用程式計量。 請參閱[監視 Azure App Service 中的應用程式](../app-service/web-sites-monitor.md)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 客戶可以建置自動整合的 App Service 上的應用程式[Azure Active Directory (Azure AD)](../active-directory/index.yml)以及其他 OAuth 相容的身分識別提供者，請參閱[中驗證和授權Azure App Service](../app-service/overview-authentication-authorization.md)。 管理 App Service 的資產的存取，所有的存取會受到驗證的 Azure AD 主體和 Azure Resource Manager RBAC 角色的組合。 |
| 授權| 是 | 管理 App Service 的資產的存取，所有的存取會受到驗證的 Azure AD 主體和 Azure Resource Manager RBAC 角色的組合。  |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | App Service 物件上執行的所有管理作業都都會透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 這些作業的歷程記錄可在入口網站中和透過 CLI;請參閱[Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftweb)並[az 監視活動記錄檔](/cli/azure/monitor/activity-log)。 |
| 資料平面記錄與稽核 | 否 | 資料層應用程式服務是包含客戶部署的網站內容的遠端檔案共用。  沒有任何稽核遠端檔案共用。 |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 管理作業，應用程式服務組態的狀態可以匯出為 Azure Resource Manager 範本和建立版本一段時間。 執行階段作業，客戶可以維護多個不同的即時版本，使用 App Service 部署位置功能的應用程式。 | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 |  |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | HTTPS/TLS。 |
| 加密金鑰處理 （CMK、 BYOK）| N/A | Azure Resource Manager 會儲存任何客戶內容，只是控制資料。 |
| 資料行層級加密 (Azure Data Services)| 是 | |
| API 呼叫加密| 是 | |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 | |
| VNet 插入支援| 是 | |
| 網路隔離，而且防火牆支援| 否 |  |
| 強制通道的支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 否 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | [Azure Active Directory](/azure/active-directory)基礎。|
| 授權| 是 | |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 活動記錄所有寫入您的資源; 上執行的作業 (PUT、 POST、 DELETE) 的公開請參閱[檢視活動記錄以稽核對資源的動作](../azure-resource-manager/resource-group-audit.md)。 |
| 資料平面記錄與稽核| N/A | |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure 備份](../backup/backup-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 對儲存體帳戶使用儲存體服務加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 |  |
| 資料行層級加密 (Azure Data Services)| 否 |  |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| VNet 插入支援| 否 |  |
| 網路隔離，而且防火牆支援| 是 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |
| 強制通道的支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 透過診斷記錄支援 Log Analytics。 請參閱「使用 Log Analytics 監視 Azure 備份保護的工作負載」(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) 以取得詳細資訊。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 會使用建立客戶和內建的 RBAC 角色。 請參閱 < 使用角色型存取控制來管理 Azure 備份的復原點 （/azure/backup/備份-rbac-rs-保存庫） 如需詳細資訊。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |
| 資料平面記錄與稽核| 否 | 無法直接存取 Azure 備份資料平面。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure 金鑰保存庫](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 所有物件都會加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 客戶在其 Key Vault 中控制所有金鑰。 當指定的硬體安全性模組 (HSM) 備份金鑰時，FIPS 層級 2 HSM 保護金鑰、 憑證或密碼。 |
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 是 | 使用 HTTPS。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 使用虛擬網路 (VNet) 服務端點。 |
| VNet 插入支援| 否 |  |
| 網路隔離，而且防火牆支援| 是 | 使用 VNet 防火牆規則。 |
| 強制通道的支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 使用 Log Analytics。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 使用 Key Vault 存取原則。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面記錄與稽核| 是 | 使用 Log Analytics。 |

### <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure 服務匯流排傳訊](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>|  依預設是伺服器端加密靜止。 | 客戶管理的金鑰和 BYOK 尚不支援。 用戶端加密是用戶端的責任 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支援標準的 HTTPS/TLS 機制。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 |   |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | API 呼叫都會經過[Azure Resource Manager](../azure-resource-manager/index.yml)和 HTTPS。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 （進階層） | 支援 VNet 服務端點[服務匯流排進階層](../service-bus-messaging/service-bus-premium-messaging.md)只。 |
| VNet 插入支援| 否 | |
| 網路隔離，而且防火牆支援| 是 （進階層） |  |
| 強制通道的支援| 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 支援透過[Azure 監視器和警示](../service-bus-messaging/service-bus-metrics-azure-monitor.md)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 透過管理[Azure Active Directory 受控服務識別](../service-bus-messaging/service-bus-managed-service-identity.md); 請參閱[服務匯流排驗證和授權](../service-bus-messaging/service-bus-authentication-and-authorization.md)。|
| 授權| 是 | 支援透過授權[RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) （預覽） 和 SAS 權杖，請參閱[服務匯流排驗證和授權](../service-bus-messaging/service-bus-authentication-and-authorization.md)。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 作業記錄檔會使用;請參閱[服務匯流排診斷記錄](../service-bus-messaging/service-bus-diagnostic-logs.md)。  |
| 資料平面記錄與稽核| 否 |  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 支援資源提供者版本設定，透過[Azure Resource Manager API](/rest/api/resources/)。|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure 服務匯流排轉送](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>|  N/A | 轉送是 web 通訊端，並不會保存資料。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 服務需要 TLS。 |
| 加密金鑰處理 （CMK、 BYOK）| 否 | 使用只在 Microsoft 的 TLS 憑證。  |
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 是 | HTTPS。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| 網路隔離，而且防火牆支援| 否 |  |
| 強制通道的支援| N/A | 轉送是 TLS 通道  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 透過 SAS。 |
| 授權|  是 | 透過 SAS。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 |
| 資料平面記錄與稽核| 是 | 連接成功 / 失敗和錯誤記錄。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 透過[Azure Resource Manager](../azure-resource-manager/index.yml)。|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 客戶擁有叢集，和據以建置叢集的虛擬機器 (VM) 擴展集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 |  |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 客戶擁有叢集，和據以建置叢集的虛擬機器 (VM) 擴展集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| 是 |  |
| 網路隔離，而且防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 強制通道的支援| 是 | Azure 網路提供強制通道功能。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 使用 Azure 監視支援和第三方支援。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 授權| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |

### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄與稽核| N/A | 客戶擁有叢集。  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 服務組態會使用 Azure 部署進行版本控制和部署。 程式碼 (應用程式和執行階段) 會使用 Azure Build 進行版本控制。
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 稱為 「 加密--使用中 」，如本文所述[Always Encrypted](../sql-database/sql-database-always-encrypted.md)。 使用服務端加密[透明資料加密](../sql-database/transparent-data-encryption-azure-sql.md)(TDE)。|
| 傳輸中加密：<ul><li>ExpressRoute 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 使用 HTTPS。 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 提供服務管理和客戶管理金鑰的處理 (透過後者[Azure Key Vault](../key-vault/index.yml)。 |
| 資料行層級加密 (Azure Data Services)| 是 | 透過[Always Encrypted](../sql-database/sql-database-always-encrypted.md)。 |
| API 呼叫加密| 是 | 使用 HTTPS/SSL。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 適用於[單一資料庫](../sql-database/sql-database-single-index.yml)只。 |
| VNet 插入支援| 是 | 適用於[受管理的執行個體](../sql-database/sql-database-managed-instance.md)只。 |
| 網路隔離，而且防火牆支援| 是 | 防火牆在這兩個資料庫和伺服器層級;網路隔離[受管理的執行個體](../sql-database/sql-database-managed-instance.md)只 |
| 強制通道的支援| 是 | [受控執行個體](../sql-database/sql-database-managed-instance.md)經由[Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 第三方 SIEM 解決方案，從 Imperva (SecureSphere)，因此也支援透過[Azure 事件中樞](../event-hubs/index.yml)透過整合[SQL 稽核](../sql-database/sql-database-auditing.md)。 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | Azure Active Directory。 |
| 授權| 是 |  |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 是 | 是僅適用於某些事件。 |
| 資料平面記錄與稽核 | 是 | 透過[SQL 稽核](../sql-database/sql-database-auditing.md)。 |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 否  | | 

### <a name="additional-security-attributes-for-sql-database"></a>SQL Database 的額外的安全性屬性

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 預防： 弱點評量 | 是 | 請參閱[SQL 弱點評量服務，可協助您識別的資料庫弱點](../sql-database/sql-vulnerability-assessment.md)。 |
| 預防： 資料探索與分類  | 是 | 請參閱[Azure SQL Database 和 SQL 資料倉儲的資料探索與分類](../sql-database/sql-database-data-discovery-and-classification.md)。 |
| 偵測： 威脅偵測 | 是 | 請參閱[進階威脅防護，Azure SQL Database 的](../sql-database/sql-database-threat-detection-overview.md)。 |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure 儲存體](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 |  |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支援標準的 HTTPS/TLS 機制。  它會傳送至服務之前，使用者也可以加密資料。 |
| 加密金鑰處理 （CMK、 BYOK）| 是 | 請參閱[在 Azure Key Vault 中使用客戶管理金鑰的儲存體服務加密](../storage/common/storage-service-encryption-customer-managed-keys.md)。|
| 資料行層級加密 (Azure Data Services)| N/A |  |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| VNet 插入支援| N/A |  |
| 網路隔離，而且防火牆支援| 是 | |
| 強制通道的支援| N/A |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 現在，可用的 azure 監視器計量記錄的開始預覽 |

### <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 是 | Azure Active Directory 中，共用金鑰、 共用存取權杖。 |
| 授權| 是 | 支援透過 RBAC、 POSIX Acl 中和 SAS 權杖的授權 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核 | 是 | Azure Resource Manager 活動記錄檔 |
| 資料平面記錄與稽核| 是 | 服務診斷記錄和 Azure 監視器記錄開始預覽  |

### <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 支援透過 Azure Resource Manager Api 的資源提供者版本控制 |