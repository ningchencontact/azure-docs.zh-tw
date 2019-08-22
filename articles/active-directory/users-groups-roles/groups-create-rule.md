---
title: 建立動態群組和檢查狀態 - Azure Active Directory |Microsoft Docs
description: 如何在 Azure 入口網站中建立群組成員資格規則, 檢查狀態。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650321"
---
# <a name="create-a-dynamic-group-and-check-status"></a>建立動態群組並檢查狀態

在 Azure Active Directory (Azure AD) 中, 您可以使用規則根據使用者或裝置屬性來判斷群組成員資格。 本文說明如何在 Azure 入口網站中設定動態群組的規則。
安全性群組或 Office 365 群組支援動態成員資格。 套用群組成員資格規則時, 會評估使用者和裝置屬性是否符合成員資格規則。 當使用者或裝置的屬性變更時, 會處理組織中的所有動態群組規則, 以進行成員資格變更。 如果使用者和裝置符合群組的條件, 則會加以新增或移除。 安全性群組可以用於裝置或使用者, 但 Office 365 群組只能是使用者群組。

如需語法、支援的屬性、運算子和成員資格規則值的範例, 請參閱[Azure Active Directory 中群組的動態成員資格規則](groups-dynamic-membership.md)。

## <a name="to-create-a-group-membership-rule"></a>建立群組成員資格規則

1. 使用租使用者中全域管理員、Intune 管理員或使用者系統管理員角色的帳戶登入[Azure AD 系統管理中心](https://aad.portal.azure.com)。
2. 選取 [群組]。
3. 選取 [所有群組]，然後選取 [新增群組]。

   ![選取命令以新增群組](./media/groups-create-rule/new-group-creation.png)

4. 在 [**群組**] 頁面上, 輸入新群組的名稱和描述。 選取使用者或裝置的**成員資格類型**, 然後選取 [**新增動態查詢**]。 「規則產生器」最多支援五個運算式。 若要加入第六個或任何後續的運算式, 您必須使用文字方塊。

   ![新增動態群組的成員資格規則](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 查看您的成員資格查詢可用的自訂延伸模組屬性
   1. 選取 [**取得自訂擴充屬性**]
   2. 輸入 [應用程式識別碼], 然後選取 [重新整理**屬性**]。
6. 建立規則之後, 請選取 [**儲存**]。
7. 選取 [**新增群組**] 頁面上的 [**建立**] 來建立群組。

如果您輸入的規則無效, 說明無法處理規則的原因會顯示在入口網站的 Azure 通知中。 請仔細閱讀, 以瞭解如何修正規則。

## <a name="turn-on-or-off-welcome-email"></a>開啟或關閉歡迎電子郵件

當建立新的 Office 365 群組時, 會將新增至群組的使用者傳送歡迎電子郵件通知。 之後, 如果使用者或裝置的任何屬性變更, 則會處理組織中的所有動態群組規則, 以進行成員資格變更。 新增的使用者隨後也會收到歡迎通知。 您可以在[Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)中關閉此行為。

## <a name="check-processing-status-for-a-rule"></a>檢查規則的處理狀態

您可以在群組的 [概觀] 頁面上看到成員資格處理狀態和上次更新日期。
  
  ![動態群組狀態的顯示](./media/groups-create-rule/group-status.png)

可能會針對**成員資格處理**狀態顯示下列狀態訊息：

* **評估中**：已收到群組變更，而且正在評估更新。
* **處理中**：正在處理更新。
* **更新完成**：處理已完成，並已建立所有適用的更新。
* **處理錯誤**：因為評估成員資格規則時發生錯誤, 所以無法完成處理。
* **已暫停更新**：系統管理員已暫停動態成員資格規則更新。 MembershipRuleProcessingState 設定為「已暫停」。

可能會針對**成員資格上次更新**狀態顯示下列狀態訊息：

* &lt;**日期和時間**&gt;：上次更新成員資格的時間。
* **進行中**：目前正在更新。
* **未知**：無法抓取上次更新時間。 群組可能是新的。

如果處理特定群組的成員資格規則時發生錯誤，則會在群組的 [概觀] 頁面頂端顯示警示。 如果無法處理租用戶內所有群組 24 小時內的暫止動態成員資格更新，則會在 [所有群組] 頂端顯示警示。

![處理錯誤訊息警示](./media/groups-create-rule/processing-error.png)

這些文章提供有關 Azure Active Directory 中群組的其他資訊。

* [查看現有的群組](../fundamentals/active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)
* [管理群組的設定](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](groups-dynamic-membership.md)
