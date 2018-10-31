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
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323741"
---
# <a name="role-based-access-control"></a>角色型存取控制

Azure Digital Twins 可讓您精確地對空間圖表中的特定資料、資源和動作進行存取控制。 這是透過名為_角色型存取控制_的細微角色和權限管理來達成的。 角色型存取控制由_角色_ (或權限的層級) 和_角色指派_ (或角色與使用者或裝置的關聯) 所組成。

使用角色型存取控制時，可將權限授與使用者、裝置、服務主體、使用者定義的函式時、屬於某個網域的所有使用者，或租用戶。 此外，存取程度也可以微調。

角色型存取控制的特色在於，其權限會繼承到空間圖形中。

## <a name="what-can-i-do-with-role-based-access-control"></a>角色型存取控制有何功能？

開發人員可使用角色型存取控制來：

* 為使用者授與管理整棟大樓各項裝置的能力，或僅就特定房間或樓層進行授權。
* 為系統管理員授與整個圖形內所有空間圖形節點的全域存取權，或僅就某部分的圖形進行授權。
* 為支援專員授與圖形的讀取權限，但存取金鑰除外。
* 為網域的每個成員授與所有圖形物件的讀取權限。

## <a name="role-based-access-control-best-practices"></a>角色型存取控制最佳做法

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>角色

### <a name="role-definitions"></a>角色定義

**角色定義**是權限的集合，有時候也稱為**角色**。 角色定義會列出允許的作業，包括*建立*、*讀取*、*更新*和*刪除*。 此外也會指定適用這些權限的物件類型。

Azure Digital Twins 中提供下列角色：

* **空間管理員**：指定的空間及其下所有節點的建立、讀取、更新和刪除權限。 全域權限。
* **使用者管理員**：使用者和使用者相關物件的建立、讀取、更新和刪除權限。 空間的讀取權限。
* **裝置管理員**：裝置和裝置相關物件的建立、讀取、更新和刪除權限。 空間的讀取權限。
* **金鑰管理員**：存取金鑰的建立、讀取、更新和刪除權限。 空間的讀取權限。
* **權杖管理員**：存取金鑰的讀取和更新權限。 空間的讀取權限。
* **使用者**：空間、感應器和使用者 (及其對應的相關物件) 的讀取權限。
* **支援專員**：存取金鑰以外各個項目的讀取權限。
* **裝置安裝人員**：裝置和感應器 (及其對應的相關物件) 的讀取和更新權限。 空間的讀取權限。
* **閘道裝置**：感應器的建立權限。 裝置和感應器 (及其對應的相關物件) 的讀取權限。

>[!NOTE]
> *前述項目的完整定義可藉由查詢系統/角色 API 來擷取。*

### <a name="object-types"></a>物件類型

`ObjectIdType` 會參考要被指定角色的身分識別類型。 `DeviceId` 和 `UserDefinedFunctionId` 類型以外的類型會對應至 Azure Active Directory (Azure AD) 物件的屬性：
  
* `UserId` 類型會將角色指派給使用者。
* `DeviceId` 類型會將角色指派給裝置。
* `DomainName` 類型會將角色指派給網域名稱。 每個具有指定網域名稱的使用者，都會有對應角色的存取權限。
* `TenantId` 類型會將角色指派給租用戶。 每個屬於指定 Azure AD 租用戶識別碼的使用者，都會有對應角色的存取權限。
* `ServicePrincipalId` 類型會將角色指派給服務主體物件識別碼。
* `UserDefinedFunctionId` 類型會將角色指派給使用者定義的函式 (UDF)。

> [!div class="nextstepaction"]
> [查詢使用者的物件識別碼](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [取得服務主體的物件識別碼](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [擷取 Azure AD 租用戶的物件識別碼](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>角色指派

建立角色指派可授與權限，而移除角色指派則可撤銷權限。 Azure Digital Twins 角色指派會建立物件 (使用者、Azure AD 租用戶等)、角色和空間的關聯性。 隨後，權限會授與給所有屬於該空間的物件，包括其下的整個空間圖形。

例如，假設某個使用者在空間圖形的根節點 (代表建築物) 的角色指派中獲得了 `DeviceInstaller` 角色。 如此，該使用者不僅可讀取和更新該節點的裝置，他對建築物中所有其他的子空間也具有相同的權限。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Digital Twins 安全性，請參閱[建立和管理角色指派](./security-create-manage-role-assignments.md)。
