---
title: Azure AD 密碼保護預覽作業和報告
description: Azure AD 密碼保護預覽部署後作業和報告
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 92c8de0961f64eea8eef830ad99c7baa268099d9
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007581"
---
# <a name="preview-azure-ad-password-protection-operational-procedures"></a>預覽：Azure AD 密碼保護作業程序

|     |
| --- |
| Azure AD 密碼保護是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

完成在內部部署環境[安裝 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)後，您必須在 Azure 入口網站中設定一些項目。

## <a name="configure-the-custom-banned-password-list"></a>設定自訂禁用密碼清單

請遵循[設定自訂禁用密碼清單](howto-password-ban-bad-configure.md)一文中的指導方針，以了解為組織自訂禁用密碼清單的步驟。

## <a name="enable-password-protection"></a>啟用密碼保護

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 [Azure Active Directory]、[驗證方法] 和 [密碼保護 (預覽)]。
1. 將 [啟用 Windows Server Active Directory 上的密碼保護] 設定為 [是]。
1. 如[部署指南](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy)所述，建議您一開始就將 [模式] 設定為 [稽核]
   * 對功能感到滿意後，您可以將 [模式] 切換為 [已強制]
1. 按一下 [儲存] 

![在 Azure 入口網站中啟用 Azure AD 密碼保護元件](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>稽核模式

稽核模式的目的是為了在「What If」模式中執行軟體。 每個 DC 代理程式服務都會根據目前作用中的原則來評估傳入的密碼。 如果目前的原則設定為要在稽核模式中執行，「不良」密碼就會產生事件記錄訊息，但系統會接受該密碼。 這是稽核和強制執行模式的唯一差別；其他所有作業的執行方式則相同。

> [!NOTE]
> Microsoft 建議您一律讓初始部署和測試在稽核模式中開始進行。 然後，請監視事件記錄中的事件，以嘗試預期在啟用強制執行模式後，是否會干擾任何現有的作業程序。

## <a name="enforce-mode"></a>強制執行模式

強制執行模式的目的是為了作為最終設定。 和上述在稽核模式時一樣，每個 DC 代理程式服務都會根據目前作用中的原則來評估傳入的密碼。 但如果啟用強制執行模式，則系統會拒絕根據原則而視為不安全的密碼。

當 Azure AD 密碼保護 DC 代理程式在強制執行模式中拒絕密碼時，終端使用者所見到的影響，會和傳統內部部署密碼複雜度強制執行功能拒絕其密碼時所見到的影響相同。 例如，使用者可能會在 Windows 登入\變更密碼畫面看到下列傳統錯誤訊息：

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

這則訊息只是數個可能結果的其中一個範例。 特定錯誤訊息會隨著嘗試設定不安全密碼的實際軟體或案例而有所不同。

受影響的終端使用者可能需要與其 IT 人員合作，以了解新的需求，並更加能夠選擇安全的密碼。

## <a name="enable-mode"></a>啟用模式

這項設定通常應保持為預設的啟用 (是) 狀態。 將此設定設為停用 (否)，會導致所有已部署的 Azure AD 密碼保護 DC 代理程式進入靜止模式，依現況接受其中的所有密碼，而在任何情況下，都不會執行任何驗證活動 (例如，甚至不會發出稽核事件)。

## <a name="usage-reporting"></a>使用量回報

`Get-AzureADPasswordProtectionSummaryReport` Cmdlet 可用來產生活動的摘要檢視。 此 Cmdlet 的範例輸出如下：

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

此 Cmdlet 的報告範圍可使用 –Forest、-Domain 或 –DomainController 參數其中之一來加以影響。 未指定參數意指 –Forest。

> [!NOTE]
> 此 Cmdlet 的運作方式是針對每個網域控制站開啟 PowerShell 工作階段。 每個網域控制站上都必須啟用 PowerShell 遠端供作階段支援，且用戶端必須要有足夠權限，才能成功。 如需 PowerShell 遠端工作階段需求的詳細資訊，請在 Powershell 視窗中，執行 'Get-help about_Remote_Troubleshooting'。

> [!NOTE]
> 從遠端查詢每個 DC 代理程式服務的系統管理事件記錄即可讓此 Cmdlet 運作。 如果事件記錄包含大量事件，此 Cmdlet 可能需要很長的時間才能完成。 此外，透過網路大量查詢大型資料集可能會影響網域控制站的效能。 因此，在生產環境中請小心使用這個 Cmdlet。

## <a name="dc-agent-discovery"></a>DC 代理程式探索

`Get-AzureADPasswordProtectionDCAgent` Cmdlet 可用來顯示與執行於網域或樹系中的各種 DC 代理程式有關的基本資訊。 您可以從執行中的 DC 代理程式服務所註冊的 serviceConnectionPoint 物件中擷取這項資訊。 此 Cmdlet 的範例輸出如下：

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

每個 DC 代理程式服務大約每小時會更新一次各種屬性。 資料仍會受限於 Active Directory 複寫延遲。

此 Cmdlet 的查詢範圍可使用 –Forest 或 –Domain 參數來變更。

## <a name="next-steps"></a>後續步驟

[針對 Azure AD 密碼保護進行疑難排解和監視](howto-password-ban-bad-on-premises-troubleshoot.md)
