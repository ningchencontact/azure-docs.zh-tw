---
title: Azure App Service 的安全性屬性
description: 評估 Azure App Service 的安全性屬性檢查清單
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442261"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure App Service 的安全性屬性

本文記載 Azure App Service 內建的安全性屬性。

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>預防

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 待用加密 (例如伺服器端加密、使用客戶管理金鑰的伺服器端加密, 以及其他加密功能) | 是 | 網站檔案內容會儲存在 Azure 儲存體中, 這會自動將待用內容加密。 請參閱待用[資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。<br><br>客戶提供的秘密會在待用時加密。 秘密會在待用時加密, 並儲存在 App Service 設定資料庫中。<br><br>網站 (D:\local 和% TMP%) 可以選擇性地使用本機連接的磁片做為暫時儲存。 本機連接的磁片不會在待用時加密。 |
| 傳輸中的加密 (例如 ExpressRoute 加密、VNet 加密中和 VNet VNet 加密)| 是 | 客戶可以將網站設定為要求並使用 HTTPS 來輸入流量。 請參閱 blog 文章[如何僅讓 AZURE APP SERVICE HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密金鑰處理 (CMK、BYOK 等)| 是 | 客戶可以選擇將應用程式秘密儲存在 Key Vault 中, 並在執行時間取得它們。 請參閱[使用 App Service 和 Azure Functions 的 Key Vault 參考 (預覽)](app-service-key-vault-references.md)。|
| 資料行層級加密 (Azure 資料服務)| N/A | |
| API 呼叫加密| 是 | 透過 HTTPS [Azure Resource Manager](../azure-resource-manager/index.yml)呼叫來設定 App Service 進行管理呼叫。 |

## <a name="network-segmentation"></a>網路分割

| 安全性屬性 | 是/否 | 注意 |
|---|---|--|
| 服務端點支援| 是 | 目前可在 App Service 預覽中使用。 請參閱[Azure App Service 的存取限制](app-service-ip-restrictions.md)。 |
| VNet 插入支援| 是 | App Service 環境是專用於單一客戶的 App Service 私用, 並插入客戶的虛擬網路中。 請參閱[App Service 環境簡介](environment/intro.md)。 |
| 網路隔離和防火牆支援| 是 | 針對 App Service 的公用多租使用者變化, 客戶可以設定網路 Acl (IP 限制) 來鎖定允許的輸入流量。  請參閱[Azure App Service 的存取限制](app-service-ip-restrictions.md)。  App Service 環境會直接部署到虛擬網路中, 因此可以使用 Nsg 來保護。 |
| 強制通道支援| 是 | App Service 環境可以部署到客戶的虛擬網路, 其中已設定強制通道。 客戶必須遵循[使用強制通道設定您的 App Service 環境](environment/forced-tunnel-support.md)中的指示。 |

## <a name="detection"></a>偵測

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| Azure 監視支援 (Log analytics、App insights 等)| 是 | App Service 與支援 Application Insights 的語言 Application Insights 整合 (完整 .NET Framework、.NET Core、JAVA 和 NODE.JS)。  請參閱[監視 Azure App Service 效能](../azure-monitor/app/azure-web-apps.md)。 App Service 也會將應用程式計量傳送至 Azure 監視器。 請參閱[監視 Azure App Service 中的應用程式](web-sites-monitor.md)。 |

## <a name="identity-and-access-management"></a>身分識別和存取管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 驗證| 是 | 客戶可以在自動與[Azure Active Directory (Azure AD)](../active-directory/index.yml)以及其他與 OAuth 相容身分識別提供者整合的 App Service 上建立應用程式;請參閱[Azure App Service 中的驗證和授權](overview-authentication-authorization.md)。 對於 App Service 資產的管理存取權, 所有存取都是由 Azure AD 已驗證主體和 Azure Resource Manager RBAC 角色的組合來控制。 |
| Authorization| 是 | 對於 App Service 資產的管理存取權, 所有存取都是由 Azure AD 已驗證主體和 Azure Resource Manager RBAC 角色的組合來控制。  |


## <a name="audit-trail"></a>稽核線索

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 控制和管理平面記錄和審核| 是 | 在 App Service 物件上執行的所有管理作業都是透過[Azure Resource Manager](../azure-resource-manager/index.yml)進行。 這些作業的歷程記錄可以在入口網站中和透過 CLI 取得。請參閱[Azure Resource Manager 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftweb)和[az monitor activity-log](/cli/azure/monitor/activity-log)。 |
| 資料平面記錄和審核 | 否 | App Service 的資料平面是遠端檔案共用, 其中包含客戶已部署的網站內容。  不會對遠端檔案共用進行任何審核。 |

## <a name="configuration-management"></a>設定管理

| 安全性屬性 | 是/否 | 注意|
|---|---|--|
| 設定管理支援 (設定的版本設定等)| 是 | 針對管理作業, App Service 設定的狀態可以匯出為 Azure Resource Manager 範本, 並在一段時間後進行版本設定。 針對執行時間作業, 客戶可以使用 App Service 部署位置 功能來維護應用程式的多個不同即時版本。 | 

