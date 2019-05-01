---
title: 設定驗證工作階段管理使用 Azure Active Directory 條件式存取
description: 自訂 Azure AD 驗證的工作階段設定頻率和瀏覽器工作階段持續性中包括使用者登。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 884b3c2d3f4414427df87ee5f04138416f363b1c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577221"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>設定驗證工作階段管理使用條件式存取

在複雜的部署中，組織可能需要限制驗證工作階段。 某些情況下可能包括：

* 來自未受管理或共用的裝置存取資源
* 來自外部網路存取機密資訊
* 高影響使用者
* 關鍵業務應用程式

條件式存取控制項可讓您建立目標組織內的特定使用案例，而不會影響所有使用者的原則。

再探討如何設定原則的詳細資料，讓我們檢查預設組態。

## <a name="user-sign-in-frequency"></a>使用者登入頻率

登入頻率會定義之前要求使用者再次登入嘗試存取資源時的時間間隔。

使用者登入頻率的 Azure Active Directory (Azure AD) 預設組態是 90 天的滾動時段。 使用者要求認證通常看起來像是合理的做法，但它可以 backfire： 使用者已受訓練來不小心輸入其認證，而不需要思考可以提供這些資訊給惡意的認證提示。

聽起來像是驚人不要求使用者重新登入 90 天，但實際上的 IT 原則的任何違規將會撤銷該工作階段。 一些範例包括 （但不限於） 密碼變更、 不相容的裝置或停用的帳戶。 您可以明確地[撤銷使用者的工作階段使用 PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 Azure AD 預設組態可歸納為 「 不要求使用者提供其認證，如果他們的工作階段安全性狀態未變更 」。

登入頻率設定適用於已實作根據標準 OATH2 或 OIDC 通訊協定的應用程式。 大部分的 Microsoft 原生應用程式，用於 Windows、 Mac 和行動裝置符合設定。

## <a name="persistence-of-browsing-sessions"></a>瀏覽工作階段的持續性

持續性的瀏覽器工作階段可讓使用者保持登入狀態後關閉並重新開啟其瀏覽器視窗。

瀏覽器工作階段持續性的 Azure AD 預設值可讓使用者選擇是否要保存工作階段藉由顯示"保持登入嗎？ 」 的個人裝置上 在成功驗證之後的提示。 如果使用的指導方針一文中的 AD FS 中設定瀏覽器持續性[AD FS 的單一登入設定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)，我們將會符合該原則，並保存 Azure AD 工作階段。 您也可以設定是否在您的租用戶中的使用者會看到 「 保持登入嗎？ 」 藉由變更適當的設定中的公司商標 窗格中使用的指導方針一文中的 Azure 入口網站提示[來自訂您的 Azure AD 登入頁面](../fundamentals/customize-branding.md)。

## <a name="configuring-authentication-session-controls"></a>設定驗證工作階段控制項

條件式存取是 Azure AD Premium 功能，而且需要 premium 授權。 如果想進一步深入了解條件式存取，請參閱[什麼是 Azure Active Directory 條件式存取？](overview.md#license-requirements-for-using-conditional-access)

> [!WARNING]
> 如果您使用[可設定權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)功能目前處於公開預覽狀態，請注意，我們不支援建立兩個不同的原則相同的使用者或應用程式組合： 一個與這項功能，另一個則使用可設定權杖存留期的功能。 Microsoft 計劃在 11 月 1 日淘汰 「 可設定權杖存留期 」 功能，並取代的條件式存取驗證工作階段管理功能。  

### <a name="policy-1-sign-in-frequency-control"></a>原則 1：登入頻率控制項

1. 建立新原則
1. 選擇所有必要的條件，為客戶的環境，包括目標雲端應用程式。

   > [!NOTE]
   > 建議您設定金鑰 Microsoft Office 應用程式，例如 Exchange Online 和 SharePoint Online 以獲得最佳使用者體驗的相等驗證提示頻率。

1. 移至**存取控制** > **工作階段**按一下**登入頻率**
1. 第一個文字方塊中輸入必要的值的天與小時
1. 選取的值**小時**或是**天**從下拉式清單中
1. 儲存原則

![以頻率設定為正負號的條件式存取原則](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

在 Azure AD 註冊的 Windows 裝置登入裝置會被視為在提示字元。 比方說，如果您已設定符號中的 Office 應用程式的 24 小時的頻率，在 Azure AD 的使用者會註冊的 Windows 裝置會藉由登入裝置滿足登入頻率的原則，而且會不提示您再次開啟 Office 應用程式時。

如果您已設定不同的 web 應用程式在相同的瀏覽器工作階段中執行的不同登入頻率，最嚴格的原則將套用至這兩個應用程式，因為在相同的瀏覽器工作階段中執行的所有應用程式共用的單一工作階段權杖。

### <a name="policy-2-persistent-browser-session"></a>原則 2：持續性瀏覽器工作階段

1. 建立新原則
1. 選擇所有必要的條件。

   > [!NOTE]
   > 請注意 做為條件，此控制項需要選擇 所有雲端應用程式

1. 移至**存取控制** > **工作階段**按一下**永續性的瀏覽器工作階段**
1. 從下拉式清單中選取的值
1. 儲存原則

![設定的持續性的瀏覽器的條件式存取原則](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> 在 Azure AD 條件式存取的永續性瀏覽器工作階段設定會覆寫 「 保持登入？ 」 在 [公司商標] 窗格中的相同使用者的 Azure 入口網站，如果您已設定這兩個原則中設定。

## <a name="validation"></a>驗證

您可以使用假設狀況工具，模擬使用者登入目標應用程式，並根據您的原則的設定方式的其他條件。 驗證工作階段管理控制項顯示在工具的結果。

![如果工具產生的條件式存取](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>原則部署

若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 如需詳細資訊，請參閱文章[Azure Active Directory 中的條件式存取的最佳做法](best-practices.md)。

## <a name="next-steps"></a>後續步驟

* 如果您想要知道如何設定條件式存取原則，請參閱文章[需要使用 Azure Active Directory 條件式存取的特定應用程式的 MFA](app-based-mfa.md)。
* 如果您已準備好設定您的環境的條件式存取原則，請參閱文章[Azure Active Directory 中的條件式存取的最佳做法](best-practices.md)。
