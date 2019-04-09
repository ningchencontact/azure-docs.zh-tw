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
ms.openlocfilehash: 2eb480e10ca3b674895d2d22cc44fb52f305f988
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007511"
---
# <a name="common-security-attributes-for-azure-services"></a>Azure 服務的常見安全性屬性

Azure 服務的各個層面都整合了安全性。 這篇文章會收集所選的 Azure 服務的常見安全性屬性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## [<a name="azure-backup"></a>Azure 備份](../backup/backup-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 對儲存體帳戶使用儲存體服務加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
| 加密金鑰處理 (CMK、BYOK 等)| 否 |  |
| 資料行層級加密 (Azure 資料服務)| 否 |  |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 否 |  |
| vNET 插入支援| 否 |  |
| 網路隔離/防火牆支援| 是 | 支援以強制通道進行 VM 備份。 對於在 VM 內執行的工作負載則不支援強制通道。 |
| 支援強制通道 | 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 透過診斷記錄支援 Log Analytics。 請參閱「使用 Log Analytics 監視 Azure 備份保護的工作負載」(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) 以取得詳細資訊。 |

### <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| 是 | 會使用建立客戶和內建的 RBAC 角色。 請參閱 < 使用角色型存取控制來管理 Azure 備份的復原點 （/azure/backup/備份-rbac-rs-保存庫） 如需詳細資訊。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| 是 | Azure 入口網站中所有由客戶觸發的動作都會都記錄到活動記錄。 |
| 資料平面記錄和稽核| 否 | 無法直接存取 Azure 備份資料平面。  |

### <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是|  |

## [<a name="azure-key-vault"></a>Azure 金鑰保存庫](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 所有物件都會加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 所有通訊皆透過加密的 API 呼叫來進行 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶在其 Key Vault 中控制所有金鑰。 當指定的硬體安全性模組 (HSM) 備份金鑰時，FIPS 層級 2 HSM 保護金鑰、 憑證或密碼。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | 使用 HTTPS。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 使用虛擬網路 (VNet) 服務端點。 |
| vNET 插入支援| 否 |  |
| 網路隔離/防火牆支援| 是 | 使用 Vnet 防火牆規則。 |
| 支援強制通道 | 否 |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 使用 Log Analytics。 |

### <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| 是 | 使用 Key Vault 存取原則。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面記錄和稽核| 是 | 使用 Log Analytics。 |
| 資料平面記錄和稽核| 是 | 使用 Log Analytics。 |

### <a name="access-controls"></a>存取控制

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理平面存取控制 | 是 | Azure Resource Manager 角色型存取控制 (RBAC) |
| 資料平面存取控制 (在每個服務層級上) | 是 | Key Vault 存取原則 |

## [<a name="azure-service-fabric"></a>Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 | 客戶擁有叢集，和據以建置叢集的虛擬機器 (VM) 擴展集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 |  |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶擁有叢集，和據以建置叢集的虛擬機器 (VM) 擴展集。 可以在虛擬機器擴展集上啟用 azure 磁碟加密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| vNET 插入支援| 是 |  |
| 網路隔離/防火牆支援| 是 | 使用網路安全性群組 (NSG)。 |
| 支援強制通道 | 是 | Azure 網路提供強制通道功能。 |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 使用 Azure 監視支援和第三方支援。 |

### <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| 是 | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| 是 | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| 是 | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和稽核| N/A | 客戶擁有叢集。  |

### <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 服務組態會使用 Azure 部署進行版本控制和部署。 程式碼 (應用程式和執行階段) 會使用 Azure Build 進行版本控制。
 |

## <a name="azure-storage"></a>Azure 儲存體

### <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| 是 |  |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 支援標準的 HTTPS/TLS 機制。  它會傳送至服務之前，使用者也可以加密資料。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 請參閱[在 Azure Key Vault 中使用客戶管理金鑰的儲存體服務加密](../storage/common/storage-service-encryption-customer-managed-keys.md)。|
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| 是 |  |

### <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 |  |
| vNET 插入支援| N/A |  |
| 網路隔離/防火牆支援| 是 | |
| 支援強制通道 | N/A |  |

### <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 是 | 現在，可用的 azure 監視器計量記錄的開始預覽 |

### <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| 是 | Azure Active Directory 中，共用金鑰、 共用存取權杖。 |
| 存取管理 - 授權| 是 | 支援透過 RBAC、 POSIX Acl 中和 SAS 權杖的授權 |


### <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核 | 是 | Azure Resource Manager 活動記錄檔 |
| 資料平面記錄和稽核| 是 | 服務診斷記錄和 Azure 監視器記錄開始預覽  |

### <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 是 | 支援透過 Azure Resource Manager Api 的資源提供者版本控制 |