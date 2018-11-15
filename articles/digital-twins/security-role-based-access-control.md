---
title: 了解 Azure Digital Twins 的角色型存取控制 | Microsoft Docs
description: 了解 Digital Twins 中採用角色型存取控制的驗證。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014783"
---
# <a name="role-based-access-control"></a>角色型存取控制

Azure Digital Twins 可讓您精確地對空間圖表中的特定資料、資源和動作進行存取控制。 這會透過名為角色型存取控制 (RBAC) 的細微角色和權限管理來達成。 RBAC 是由「角色」和「角色指派」所組成的。 角色會識別權限的層級。 角色指派會將角色與使用者或裝置產生關聯。

使用 RBAC，就可以將權限授與：

- 使用者。
- 裝置。
- 服務主體。
- 使用者定義函式。 
- 隸屬於網域的所有使用者。 
- 租用戶。
 
您也可以微調存取權的等級。

RBAC 的特色在於其權限會向下繼承到空間圖形。

## <a name="what-can-i-do-with-rbac"></a>RBAC 有何用途？

開發人員可能會使用 RBAC 來進行下列動作：

* 為使用者授與管理整棟建築物各項裝置的能力，或僅就特定房間或樓層進行授權。
* 為系統管理員授與整個圖形內所有空間圖形節點的全域存取權，或僅就某部分的圖形進行授權。
* 為支援專員授與圖形的讀取權限，但存取金鑰除外。
* 為網域的每個成員授與所有圖形物件的讀取權限。

## <a name="rbac-best-practices"></a>RBAC 最佳做法

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>角色

### <a name="role-definitions"></a>角色定義

角色定義是權限的集合，有時也稱為角色。 角色定義會列出允許的作業，包括建立、讀取、更新和刪除。 此外也會指定適用這些權限的物件類型。

Azure Digital Twins 中提供下列角色：

* **空間管理員**：適用於指定空間及其下所有節點的建立、讀取、更新和刪除權限。 全域權限。
* **使用者管理員**：適用於使用者和使用者相關物件的建立、讀取、更新和刪除權限。 空間的讀取權限。
* **裝置管理員**：適用於裝置和裝置相關物件的建立、讀取、更新和刪除權限。 空間的讀取權限。
* **金鑰管理員**：適用於存取金鑰的建立、讀取、更新和刪除權限。 空間的讀取權限。
* **權杖管理員**：適用於存取金鑰的讀取和更新權限。 空間的讀取權限。
* **使用者**：適用於空間、感應器和使用者 (包括其對應的相關物件) 的讀取權限。
* **支援專員**：存取金鑰以外各個項目的讀取權限。
* **裝置安裝人員**：適用於裝置和感應器 (包括其對應的相關物件) 的讀取和更新權限。 空間的讀取權限。
* **閘道裝置**：感應器的建立權限。 適用於裝置和感應器 (包括其對應的相關物件) 的讀取權限。

>[!NOTE]
> 若要擷取上述角色的完整定義，請查詢系統/角色 API。

### <a name="object-types"></a>物件類型

`ObjectIdType` 會參考要被指定角色的身分識別類型。 `DeviceId` 和 `UserDefinedFunctionId` 類型以外的類型會對應至 Azure Active Directory (Azure AD) 物件的屬性：
  
* `UserId` 類型會將角色指派給使用者。
* `DeviceId` 類型會將角色指派給裝置。
* `DomainName` 類型會將角色指派給網域名稱。 每個具有指定網域名稱的使用者，都會有對應角色的存取權限。
* `TenantId` 類型會將角色指派給租用戶。 每個屬於指定 Azure AD 租用戶識別碼的使用者，都會有對應角色的存取權限。
* `ServicePrincipalId` 類型會將角色指派給服務主體物件識別碼。
* `UserDefinedFunctionId` 類型會將角色指派給使用者定義函式 (UDF)。

> [!div class="nextstepaction"]
> [查詢使用者的物件識別碼](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [取得服務主體的物件識別碼](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [擷取 Azure AD 租用戶的物件識別碼](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>角色指派

若要將權限授與收件者，請建立角色指派。 若要撤銷權限，請移除角色指派。 Azure Digital Twins 的角色指派會建立物件 (例如，使用者或 Azure AD 租用戶) 與角色和空間的關聯性。 權限會授與屬於該空間的所有物件。 空間包含其下方的整個空間圖形。

例如，假設某個使用者在空間圖形根節點 (代表建築物) 的角色指派中獲得了 `DeviceInstaller` 角色。 該使用者接著就能針對該節點及建築物中的所有其他子空間，讀取和更新裝置。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。
