---
title: 選取頁面合約-Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中選取頁面合約。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c3ee05096b0bfd071ea569105973097ce9727b07
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604533"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>使用自訂原則在 Azure Active Directory B2C 中選取頁面合約

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

無論您使用使用者流程或自訂原則，您可以在您的 Azure Active Directory (Azure AD) B2C 原則讓 JavaScript 用戶端程式碼。 若要啟用您的應用程式的 JavaScript，您必須新增項目您[自訂原則](active-directory-b2c-overview-custom.md)、 選取頁面合約，以及使用[b2clogin.com](b2clogin.md)在要求中。

頁面合約是由 Azure AD B2C 提供的元素與您提供的內容所相關聯的內容。

這篇文章討論如何藉由設定自訂原則中，選取 Azure AD B2C 中的頁面合約。

> [!NOTE]
> 如果您想要為使用者流程中啟用 JavaScript，請參閱[JavaScript 和頁面合約版本在 Azure Active Directory B2C](user-flow-javascript-overview.md)。

## <a name="replace-datauri-values"></a>取代 DataUri 值

在您的自訂原則中，您可能會有定義用於使用者旅程圖中之 HTML 範本的 [ContentDefinitions](contentdefinitions.md)。 **ContentDefinition** 包含會參考由 Azure AD B2C 所提供之頁面元素的 **DataUri**。 **LoadUri** 是針對您提供之 HTML 和 CSS 內容的相對路徑。

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

若要選取頁面合約，您必須變更您原則中之 [ContentDefinitions](contentdefinitions.md) 的 **DataUri** 值。 藉由從舊的 **DataUri** 值切換到新的值，您將會選取不可變的套件。 使用此套件的優點，在於您知道它不會變更，並在您的頁面上造成未預期的行為。

若要設定頁面合約，請使用下表來尋找 **DataUri** 值。

| 舊的 DataUri 值 | 新的 DataUri 值 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:claimsconsent:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0` |

## <a name="version-change-log"></a>版本變更記錄檔

頁面合約套件會定期更新以納入其頁面項目中的修正和改善。 下列的變更記錄檔會指定每個版本所做的變更。

### <a name="110"></a>1.1.0

- 例外狀況頁面 (globalexception)
  - 協助工具修正程式
  - 沒有從原則的連絡人時，移除預設的訊息
  - 預設 CSS 移除
- MFA 頁面 （多重要素）
  - 移除 '確認程式碼 按鈕
  - 此程式碼現在只會採用的輸入的欄位中輸入最多六 （6） 個字元
  - 頁面將會自動嘗試確認輸入 6 位數代碼，而不需要按一下任一按鈕時所輸入的程式碼
  - 如果程式碼是錯誤的輸入的欄位則會自動清除
  - 後三 （3） 嘗試使用不正確的程式碼，B2C 會將錯誤結果傳回給信賴憑證者的合作對象
  - 協助工具修正程式
  - 預設 CSS 移除
- 自我判斷提示的頁面 (selfasserted)
  - 移除的取消警示
  - 錯誤項目的 CSS 類別
  - 顯示/隱藏錯誤邏輯改善
  - 預設 CSS 移除
- 統一的 SSP (unifiedssp)
  - 已新增的保留我登入 (KMSI) 控制項

### <a name="100"></a>1.0.0

- 初始版本

## <a name="next-steps"></a>後續步驟

如需如何自訂應用程式之使用者介面的詳細資訊，請參閱[在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面](active-directory-b2c-ui-customization-custom.md)。
