---
title: 設定雙因素驗證方法概觀 - Azure Active Directory | Microsoft Docs
description: 有關如何設定雙因素驗證之雙因素驗證方法的概觀。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 08/12/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83bba343ac73fd0df575ae1e8a83c589a0bc15ac
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616188"
---
# <a name="set-up-your-two-factor-verification-methods-overview"></a>設定雙因素驗證方法概觀

您的組織已開啟雙因素驗證，這表示您的公司或學校帳戶登入現在需要您的使用者名稱、密碼與行動裝置或電話的組合。 您的組織已開啟此額外驗證，因為它比只使用密碼更安全，它依賴兩種形式的驗證：您知道的東西和您持有的東西。 雙因素驗證有助於防止惡意駭客偽裝成您，因為即使他們有您的密碼，他們也可能沒有您的裝置。

>[!Important]
>本內容適用於使用者。 如果您是系統管理員，可以在 [Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory)中找到更多關於如何設定和管理 Azure Active Directory (Azure AD) 環境的資訊。

## <a name="who-decides-if-you-use-this-feature"></a>由誰決定是否使用此功能？

視帳戶類型之不同，您的組織可能會決定您必須使用雙因素驗證，或是您可以自行決定。

- **公司或學校帳戶。** 如果您使用公司或學校帳戶 (例如 alain@contoso.com)，則會由您的組織決定您是否必須使用雙因素驗證，以及特定的驗證方法。 因為您的組織已決定您必須使用此功能，所以您無法個別關閉此功能。

- **個人 Microsoft 帳戶。** 您可以選擇為個人 Microsoft 帳戶 (例如 alain@outlook.com) 設定雙因素驗證。 如果您遇到雙因素驗證與個人 Microsoft 帳戶的問題，請參閱[開啟或關閉 Microsoft 帳戶的雙步驟驗證](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)。 因為是否使用此功能是由您選擇，所以您可以視需要開啟和關閉它。

## <a name="access-the-additional-security-verification-page"></a>存取 [其他安全性驗證] 頁面

您的組織開啟並設定雙因素驗證之後，您會收到提示，指示您提供詳細資訊以協助保護您的帳戶。

![需要詳細資訊的提示](media/multi-factor-authentication-verification-methods/multi-factor-authentication-initial-prompt.png)

### <a name="to-access-the-additional-security-verification-page"></a>存取 [其他安全性驗證] 頁面

1. 從 [需要更多資訊]  提示中選取 [下一步]  。

    隨即顯示 [其他安全性驗證]  頁面。

2. 在 [其他安全性驗證]  頁面上，您必須決定在登入公司或學校帳戶之後要使用哪一個雙因素驗證方法來確認您是所宣稱的身分。 您可以選取：

    | 連絡方法 | 描述 |
    | --- | --- |
    | 行動應用程式 | <ul><li>**接收驗證的通知。** 此選項會將通知推送至您智慧型手機或平板電腦上的驗證器應用程式。 檢視通知，如果合法則選取應用程式中的 [驗證]  。 您的公司或學校可能會要求您輸入 PIN 後才能進行驗證。</li><li>**使用驗證碼。** 在此模式中，驗證器應用程式會產生每 30 秒更新一次的驗證碼。 在登入畫面中輸入最新的驗證碼。<br>Microsoft Authenticator 應用程式適用於 [Android](https://go.microsoft.com/fwlink/?linkid=866594) 和 [iOS](https://go.microsoft.com/fwlink/?linkid=866594)。</li></ul> |
    | 驗證電話 | <ul><li>**撥打電話**撥打自動語音電話給您所提供的電話號碼。 接聽電話並按電話鍵盤上的井字鍵 (#) 進行驗證。</li><li>**簡訊**傳送包含驗證碼的簡訊。 遵循文字中的提示，回覆簡訊或將所提供的驗證碼輸入登入介面。</li></ul> |
    | 辦公室電話 | 撥打自動語音電話給您所提供的電話號碼。 接聽電話並按電話鍵盤上的井字鍵 (#) 進行驗證。 |

## <a name="next-steps"></a>後續步驟

存取 [其他安全性驗證]  頁面之後，您必須選取並設定雙因素驗證方法：

- [將您的行動裝置設定為您的驗證方法](multi-factor-authentication-setup-phone-number.md)

- [將辦公室電話設定為您的驗證方法](multi-factor-authentication-setup-office-phone.md)

- [將 Microsoft Authenticator 應用程式設定為您的驗證方法](multi-factor-authentication-setup-auth-app.md)

## <a name="related-resources"></a>相關資源

- [管理您的雙因素驗證方法設定](multi-factor-authentication-end-user-manage-settings.md)

- [管理應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)

- [使用雙因素驗證來登入](multi-factor-authentication-end-user-signin.md)

- [取得雙因素驗證的協助](multi-factor-authentication-end-user-troubleshoot.md) 
