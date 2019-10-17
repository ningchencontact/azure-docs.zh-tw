---
title: 建議的安全性作法
description: 在使用 Azure 委派的資源管理時，請務必考慮安全性與存取控制。
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 08a54313885c7d38117c242f01c2780796f38a08
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286191"
---
# <a name="recommended-security-practices"></a>建議的安全性作法

使用 Azure 委派的資源管理時，請務必考慮安全性和存取控制。 您租用戶中的使用者將能直接存取客戶訂用帳戶與資源群組，因此您應該採取步驟以維護租用戶的安全性。 您也應該確保您只會允許有效管理客戶資源所需的必要存取權。 此主題會提供可協助您這樣做的建議。

## <a name="require-azure-multi-factor-authentication"></a>要求使用 Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) (亦稱為雙步驟驗證) 可透過要求執行多個驗證步驟，來協助防止攻擊者取得帳戶存取權。 您應該針對服務提供者租用戶中的所有使用者要求使用 Multi-Factor Authentication，這包括將能夠存取客戶資源的所有使用者。

我們建議您要求客戶也在其租用戶中實作 Azure Multi-Factor Authentication。

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>使用最低權限原則將權限指派給群組

若要使管理變得更加輕鬆，我們建議您針對管理客戶資源所需的每個角色使用 Azure AD 使用者群組。 這能讓您視需要將個別使用者新增至群組，是或從該群組中移除使用者，而不是直接將權限指派給該使用者。

建立權限結構時，請務必遵循最低權限原則來讓使用者只具備完成其工作所需的權限，以協助減少發生意外錯誤的機會。

例如，您應該使用像這樣的結構：

|群組名稱  |類型  |principalId  |角色定義  |角色定義識別碼  |
|---------|---------|---------|---------|---------|
|結構設計師     |使用者群組         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|評量     |使用者群組         |\<principalId\>         |讀取者         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 專家     |使用者群組         |\<principalId\>         |VM 參與者         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|自動化     |服務主體名稱 (SPN)         |\<principalId\>         |參與者         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

在您建立這些群組之後，便可以視需要指派使用者。 請僅加入真正需要存取權的使用者。 請務必定期檢閱群組成員，並移除已不再適合或需要包含在該群組內的任何使用者。

請注意，當您[透過公用受控服務供應項目將客戶上線](../how-to/publish-managed-services-offers.md)時，您包含的任何群組 (或使用者或服務主體) 都將具有和購買該方案的每個客戶相同的權限。 若要指派不同群組來搭配每個客戶一起使用，您必須發佈每個客戶專屬的個別私人方案，或是使用 Azure Resource Manager 範本將客戶個別上線。 例如，您可以發佈存取非常有限的公用方案，然後使用自訂的 Azure Resource 範本來視需要授與額外的存取權，並直接與客戶合作以將其資源上線來取得額外的存取權。


## <a name="next-steps"></a>後續步驟

- [部署 Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)。
- 了解[跨租用戶管理體驗](cross-tenant-management-experience.md)。
