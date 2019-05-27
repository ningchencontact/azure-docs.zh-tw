---
title: Azure App Service 的安全性屬性
description: 評估 Azure App Service 的安全性屬性的檢查清單
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 1d7ab8008e8fbdb5f851f158d14f62bdea803f11
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001707"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service 的安全性屬性

這篇文章說明 Azure App Service 內建的一般安全性屬性。

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 靜態加密 （例如伺服器端加密，使用客戶管理的金鑰，伺服器端加密和其他加密功能） | 有 | 網站檔案內容會儲存在 Azure 儲存體，這會自動加密待用內容。 請參閱[待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。<br><br>提供的客戶機密資料皆在待用加密。 密碼會加密待用時儲存在應用程式服務組態資料庫。<br><br>在本機連接的磁碟 （選擇性） 可用來當做暫存儲存體的網站 （D:\local 和 %TMP%)。 在本機連接的磁碟不會進行待用加密。 |
| （例如 ExpressRoute 加密，在 VNet 加密和 VNet 對 VNet 加密） 的傳輸中加密| 有 | 客戶可以設定 web sites 設定成需要與輸入流量使用 HTTPS。 請參閱部落格文章[如何讓 Azure 應用程式服務僅限 HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密金鑰處理 （CMK、 BYOK）| 有 | 客戶可以選擇將應用程式祕密儲存在金鑰保存庫，並在執行階段擷取它們。 請參閱[App Service 和 Azure Functions （預覽） 會使用 Key Vault 參考](app-service-key-vault-references.md)。|
| 資料行層級加密 (Azure Data Services)| N/A | |
| API 呼叫加密| 有 | 若要設定 App Service 管理呼叫發生透過[Azure Resource Manager](../azure-resource-manager/index.yml)透過 HTTPS 的呼叫。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 有 | 目前可供預覽的 App Service。 請參閱[Azure App Service 的存取限制](app-service-ip-restrictions.md)。 |
| VNet 插入支援| 有 | App Service 環境都是以插入客戶的虛擬網路的單一客戶專用的 App service 的私用實作。 請參閱[App Service Environment 簡介](environment/intro.md)。 |
| 網路隔離和 Firewalling 支援| 有 | App Service 的公用多租用戶變化，客戶可以設定網路 Acl （IP 限制） 鎖定允許輸入流量。  請參閱[Azure App Service 的存取限制](app-service-ip-restrictions.md)。  App Service 環境會直接部署到虛擬網路，因此您可以使用 Nsg 保護。 |
| 強制通道的支援| 有 | App Service Environment 可以部署到客戶的強制通道設定所在的虛擬網路中。 客戶必須依照[設定您的 App Service Environment 使用強制通道](environment/forced-tunnel-support.md)。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 （Log analytics、 App insights）| 有 | App Service 整合 Application Insights 的支援 （完整的.NET Framework、.NET Core、 Java 和 Node.JS） 的 Application Insights 的語言。  請參閱[監視 Azure App Service 的效能](../azure-monitor/app/azure-web-apps.md)。 App Service 也會傳送至 「 Azure 監視器的應用程式計量。 請參閱[監視 Azure App Service 中的應用程式](web-sites-monitor.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Authentication| 有 | 客戶可以建置自動整合的 App Service 上的應用程式[Azure Active Directory (Azure AD)](../active-directory/index.md)以及其他 OAuth 相容的身分識別提供者，請參閱[中驗證和授權Azure App Service](overview-authentication-authorization.md)。 管理 App Service 的資產的存取，所有的存取會受到驗證的 Azure AD 主體和 Azure Resource Manager RBAC 角色的組合。 |
| 授權| 有 | 管理 App Service 的資產的存取，所有的存取會受到驗證的 Azure AD 主體和 Azure Resource Manager RBAC 角色的組合。  |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄與稽核| 有 | App Service 物件上執行的所有管理作業都都會透過[Azure Resource Manager](../azure-resource-manager/index.yml)。 這些作業的歷程記錄可在入口網站中和透過 CLI;請參閱[Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftweb)並[az 監視活動記錄檔](/cli/azure/monitor/activity-log)。 |
| 資料平面記錄與稽核 | 無 | 資料層應用程式服務是包含客戶已部署的網站內容的遠端檔案共用。  沒有任何稽核遠端檔案共用。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 組態管理支援 （版本設定的組態等）。| 有 | 管理作業，應用程式服務組態的狀態可以匯出為 Azure Resource Manager 範本和建立版本一段時間。 執行階段作業，客戶可以維護多個不同的即時版本，使用 App Service 部署位置功能的應用程式。 | 

