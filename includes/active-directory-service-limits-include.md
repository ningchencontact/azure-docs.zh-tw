---
title: 包含檔案
description: 包含檔案
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 067280cdad85f59106bce5ff214e2fa9eddf3b71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133461"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| Category | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單一使用者最多可以建立 20 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個目錄中新增 450 個以內的網域名稱。 |
| 物件 |<ul><li>依照預設，在免費版的 Azure Active Directory 中，最多可以在單一目錄中建立 50,000 個物件。 如果您有至少一個已驗證的網域，則 Azure AD 中的預設目錄服務配額會擴充至 300,000 個物件。 </li><li>非系統管理員的使用者最多可以建立 250 個物件。 此配額可還原的計數包括作用中的物件和已刪除的物件。 只可還原 30 天內刪除的已刪除物件。 此配額無法還原的已刪除物件計數值為 30 天的四分之一。 您可以[將系統管理員角色指派給非系統管理員使用者](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)，他們會因為需要進行的日常工作而重覆超過此配額。</li></ul> |
| 結構描述延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>任何單一物件均可寫入 100 個延伸模組值，包括所有  類型和所有  應用程式皆可。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| [應用程式] |最多 100 個使用者可以成為單一應用程式的擁有者。 |
| 群組 |<ul><li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的物件都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。</li></ul> |
| 應用程式 Proxy | <ul><li>最多 500 筆的交易，每個應用程式 Proxy 應用程式每秒</li><li>最多 750 每秒交易數個租用戶</li></ul><br/>交易被定義為單一 http 要求和回應唯一的資源。 節流時，用戶端會收到 429 的回應 （太多要求）。 |
| 存取面板 |<ul><li>每位使用者在存取面板中可以看到的應用程式數目沒有限制。 這適用於已指派 Azure AD Premium 或 Enterprise Mobility Suite 授權的使用者。</li><li>每位使用者可在存取面板中看到最多 10 個應用程式圖格。 這項限制適用於已指派「免費」或 Azure Active Directory 的 Azure AD Basic 版本授權的使用者。 應用程式圖格範例包括 Box、Salesforce 或 Dropbox。 此限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | 物件可以是有不超過 30 個管理單位的成員。 |
| 系統管理員角色與權限 | <ul><li>群組無法加入成為[擁有者](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)。</li><li>無法將群組指派給[角色](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>使用者的能力讀取其他使用者的目錄資訊無法限制之外的整個租用戶的切換，以停用所有非系統管理員使用者的存取 （不建議使用） 的所有目錄資訊。 預設權限的更多有關[此處](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>可能需要 15 分鐘，或簽署外/登入之前系統管理員角色成員資格新增項目和撤銷才會生效。</li></ul> |
