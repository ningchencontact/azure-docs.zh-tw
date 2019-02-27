---
author: msmbaldwin
ms.service: service-fabric
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 179d87a0c1af587148f1b5ffa2cad8085ef0886f
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/15/2019
ms.locfileid: "56306848"
---
## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密：<ul><li>伺服器端加密</li><li>使用客戶管理的金鑰進行伺服器端加密</li><li>其他加密功能 (例如用戶端、一律加密等)</ul>| yes | 客戶擁有叢集，和據以建置叢集的虛擬機器 (VM) 擴展集。 在 VM 擴展集上可以啟用 Azure 磁碟加密。 |
| 傳輸中加密：<ul><li>Express Route 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| yes |  |
| 加密金鑰處理 (CMK、BYOK 等)| yes | 客戶擁有叢集，和據以建置叢集的虛擬機器 (VM) 擴展集。 在 VM 擴展集上可以啟用 Azure 磁碟加密。 |
| 資料行層級加密 (Azure 資料服務)| N/A |  |
| API 呼叫加密| yes | Service Fabric API 呼叫會透過 Azure Resource Manager 進行。 需要有效的 JSON Web 權杖 (JWT)。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| yes |  |
| vNET 插入支援| yes |  |
| 網路隔離/防火牆支援| yes | 使用網路安全性群組 (NSG)。 |
| 支援強制通道 | yes | Azure 網路提供強制通道功能。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log Analytics、App Insights 等)| yes | 使用 Azure 監視支援和第三方支援。 |

## <a name="iam-support"></a>IAM 支援

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 存取管理 - 驗證| yes | 驗證會透過 Azure Active Directory 進行。 |
| 存取管理 - 授權| yes | 透過 SFRP 對呼叫進行身分識別與存取管理 (IAM)。 直接呼叫叢集端點可支援兩種角色：使用者和管理員。客戶可將 API 對應至任一角色。 |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制/管理計劃記錄和稽核| yes | 所有控制平面作業都須通過稽核和核准的程序。 |
| 資料平面記錄和稽核| N/A | 客戶擁有叢集。  |

## <a name="configuration-management"></a>組態管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 (組態的版本控制等)| yes | 服務組態會使用 Azure 部署進行版本控制和部署。 程式碼 (應用程式和執行階段) 會使用 Azure Build 進行版本控制。
 |
