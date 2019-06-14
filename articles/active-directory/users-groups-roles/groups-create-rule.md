---
title: 建立動態群組和檢查狀態 - Azure Active Directory |Microsoft Docs
description: 如何在 Azure 入口網站中建立群組成員資格規則，請檢查狀態。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472109"
---
# <a name="create-a-dynamic-group-and-check-status"></a>建立動態群組並檢查狀態

在 Azure Active Directory (Azure AD) 中，您可以使用規則來決定使用者或裝置的屬性為基礎的群組成員資格。 這篇文章說明如何設定 Azure 入口網站中的動態群組規則。
安全性群組或 Office 365 群組支援動態成員資格。 套用的群組成員資格規則時，使用成員資格規則的比對的使用者和裝置屬性會接受評估。 當屬性變更為使用者或裝置時，組織中的所有動態群組規則處理成員資格變更。 使用者和裝置新增或移除符合的條件群組。

如需更多的語法、 支援的屬性、 運算子和值的成員資格規則的範例，請參閱[的 Azure Active Directory 中群組動態成員資格規則](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>建立群組成員資格規則

1. 登入[Azure AD 系統管理中心](https://aad.portal.azure.com)中全域管理員、 Intune 系統管理員或在租用戶中的使用者系統管理員角色的帳戶。
2. 選取 [群組]  。
3. 選取 [所有群組]  ，然後選取 [新增群組]  。

   ![選取要加入新群組的命令](./media/groups-create-rule/new-group-creation.png)

4. 在 **群組**頁面上，輸入名稱和新群組的描述。 選取 **成員資格類型**作為使用者或裝置，然後選取**新增動態查詢**。 您可以使用規則建立器來建置簡單的規則，或[自行撰寫的成員資格規則](groups-dynamic-membership.md)。

   ![新增動態群組的成員資格規則](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 若要查看可供您的成員資格查詢的自訂延伸模組屬性
   1. 選取**取得自訂的延伸模組屬性**
   2. 輸入應用程式識別碼，然後選取**重新整理屬性**。 
6. 建立規則之後，在刀鋒視窗的底部選取 [新增查詢]  。
7. 選取 [更多服務]  on the  來建立群組。

如果您輸入的規則不是有效的項目，說明為什麼無法處理此規則會顯示在入口網站的右上角。 仔細閱讀，了解如何修正此規則。

## <a name="turn-on-or-off-welcome-email"></a>開啟或關閉歡迎電子郵件

建立新的 Office 365 群組時，會新增至群組的使用者頁面時，會傳送歡迎畫面的通知。 稍後，如果使用者或裝置的任何屬性變更時，組織中的所有動態群組規則處理成員資格變更。 新增的使用者隨後也會收到歡迎通知。 您可以關閉此行為[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)。 

## <a name="check-processing-status-for-a-rule"></a>檢查規則的處理狀態

您可以在群組的 [概觀]  頁面上看到成員資格處理狀態和上次更新日期。
  
  ![顯示的動態群組狀態](./media/groups-create-rule/group-status.png)

可能會針對**成員資格處理**狀態顯示下列狀態訊息：

* **評估中**：已收到群組變更，而且正在評估更新。
* **處理中**：正在處理更新。
* **更新完成**：處理已完成，並已建立所有適用的更新。
* **處理錯誤**：無法完成處理，因為評估成員資格規則時發生錯誤。
* **已暫停更新**：系統管理員已暫停動態成員資格規則更新。 MembershipRuleProcessingState 設定為「已暫停」。

可能會針對**成員資格上次更新**狀態顯示下列狀態訊息：

* &lt;**日期和時間**&gt;：上次更新成員資格的時間。
* **進行中**：目前正在更新。
* **未知**：無法擷取上次更新時間。 此群組可能是新的。

如果處理特定群組的成員資格規則時發生錯誤，則會在群組的 [概觀]  頁面頂端顯示警示。 如果無法處理租用戶內所有群組 24 小時內的暫止動態成員資格更新，則會在 [所有群組]  頂端顯示警示。

![處理錯誤的訊息警示](./media/groups-create-rule/processing-error.png)

這些文章提供有關 Azure Active Directory 中群組的其他資訊。

* [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](groups-dynamic-membership.md)
