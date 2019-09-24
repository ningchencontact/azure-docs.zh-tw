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
ms.openlocfilehash: c58977798fabc120f3a8647774f575bc32515dcd
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219023"
---
以下是 Azure Active Directory (Azure AD) 服務的使用條件約束和其他服務限制。

| Category | 限制 |
| --- | --- |
| 目錄 | 單一使用者能以成員或來賓的身分，隸屬於最多 500 個 Azure AD 目錄。<br/>單一使用者最多可以建立 20 個目錄。 |
| 網域 | 您可以新增 900 個以內的受控網域名稱。 如果您要設定所有網域與內部部署 Active Directory 建立同盟，則可以在每個目錄中新增 450 個以內的網域名稱。 |
|資源 |<ul><li>預設 Azure Active Directory 免費版的使用者，可以在單一目錄中建立最多 50000 Azure AD 資源。 如果您至少有一個已驗證的網域，Azure AD 中的預設目錄服務配額會延伸到 300000 Azure AD 資源。 </li><li>非系統管理員使用者可以建立的 Azure AD 資源不超過250個。 作用中資源和已刪除的資源，可供還原計入此配額。 只有刪除了少於30天前刪除的 Azure AD 資源可供還原。 已刪除已無法再供還原使用的 Azure AD 資源計數，其值為一季30天。 如果您的開發人員可能會在其定期責任的過程中重複超出此配額，您可以[建立並指派自訂角色](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)，以建立無限數量的應用程式註冊許可權。</li></ul> |
| 結構描述延伸模組 |<ul><li>字串類型延伸模組最多可以包含 256 個字元。 </li><li>二進位類型延伸模組受限於 256 個位元組。</li><li>只有100延伸模組值（在*所有*類型和*所有*應用程式中）可以寫入任何單一 Azure AD 資源。</li><li>只能使用「字串」類型或「二進位」類型單一值屬性來擴充 User、Group、TenantDetail、Device、Application 和 ServicePrincipal 實體。</li><li>結構描述延伸模組僅適用於圖形 API 版本 1.21 預覽。 應用程式必須取得寫入權限才能登錄延伸模組。</li></ul> |
| 應用程式 |最多 100 個使用者可以成為單一應用程式的擁有者。 |
| 群組 |<ul><li>最多 100 個使用者可以成為單一群組的擁有者。</li><li>任何數目的 Azure AD 資源都可以是單一群組的成員。</li><li>使用者可以是任意數目群組的成員。</li><li>可以使用 Azure AD Connect 從您的內部部署 Active Directory 同步至 Azure Active Directory 群組中的成員數目限制為 5 萬個成員。</li></ul> |
| 應用程式 Proxy | <ul><li>每個應用程式 Proxy 應用程式最多每秒500筆交易</li><li>租使用者每秒最多750筆交易</li></ul><br/>交易會定義為單一 HTTP 要求和唯一資源的回應。 當節流時，用戶端會收到429回應（要求太多）。 |
| 存取面板 |<ul><li>每位使用者在存取面板中可以看到的應用程式數目沒有限制。 這適用於已指派 Azure AD Premium 或 Enterprise Mobility Suite 授權的使用者。</li><li>每位使用者可在存取面板中看到最多 10 個應用程式圖格。 此限制適用于獲指派 Azure AD Free 授權方案授權的使用者。 應用程式圖格範例包括 Box、Salesforce 或 Dropbox。 此限制不適用於系統管理員帳戶。</li></ul> |
| 報告 | 在任何報告中，最多可以檢視或下載 1000 個資料列。 任何其他資料會遭到截斷。 |
| 管理單位 | Azure AD 的資源可以是不超過30個管理單位的成員。 |
| 系統管理員角色與權限 | <ul><li>無法將群組新增為[擁有](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)者。</li><li>無法將群組指派給[角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。</li><li>使用者讀取其他使用者的目錄資訊的能力不能限制在整個租使用者的交換器外，以停用所有非系統管理員的使用者存取所有目錄資訊（不建議）。 如需預設許可權的詳細資訊，請參閱[這裡](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)。</li><li>最多可能需要15分鐘的時間，或登出/登入，系統管理員角色成員資格新增和撤銷才會生效。</li></ul> |
