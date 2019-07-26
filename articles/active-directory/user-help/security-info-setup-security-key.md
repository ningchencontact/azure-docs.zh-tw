---
title: 將安全性資訊設定為使用安全性金鑰 (預覽)-Azure Active Directory |Microsoft Docs
description: 如何設定您的安全性資訊, 以使用快速身分識別線上 (FIDO2) 安全性金鑰來驗證您的身分識別。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386495"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>將安全性資訊設定為使用安全性金鑰 (預覽)

您可以使用安全性金鑰做為您組織內的無密碼登入方法。 安全性金鑰是與唯一 PIN 搭配使用的實體裝置, 用以登入您的公司或學校帳戶。 由於安全性金鑰需要您擁有實體裝置, 而且只有您知道的東西, 因此它會被視為比使用者名稱和密碼更強的驗證方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>本內容適用於使用者。 I如果您是系統管理員，可以在 [Azure Active Directory 文件](https://docs.microsoft.com/azure/active-directory)中找到更多關於如何設定和管理 Azure Active Directory (Azure AD) 環境的資訊。

## <a name="what-is-a-security-key"></a>什麼是安全性金鑰？

我們目前支援使用[快速身分識別線上 (FIDO)](https://fidoalliance.org/fido2/) (FIDO2) 無密碼驗證方法的數個安全性金鑰設計和提供者。 此方法可讓您登入工作或學校帳戶一次, 以存取貴組織的所有雲端資源和支援的瀏覽器。

如果您的系統管理員或組織需要您的工作或學校帳戶, 請提供安全性金鑰。 您可以使用不同類型的安全性金鑰, 例如您插入裝置的 USB 金鑰, 或您在 NFC 讀取器上的 NFC 金鑰。 您可以從製造商的檔中, 瞭解有關安全性金鑰的詳細資訊, 包括其類型。

> [!Note]
> 如果您無法使用 FIDO2 的安全性金鑰, 您可以使用其他無密碼驗證方法, 例如 Microsoft Authenticator 應用程式或 Windows Hello。 如需 Microsoft Authenticator 應用程式的詳細資訊, 請參閱[什麼是 Microsoft Authenticator 應用程式？](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)。 如需 Windows Hello 的詳細資訊, 請參閱[Windows hello 總覽](https://www.microsoft.com/windows/windows-hello)。

## <a name="before-you-begin"></a>開始之前

在您開始註冊安全性金鑰之前, 請先仔細檢查下列事項:

- 您的系統管理員已開啟這項功能, 可在您的組織中使用。

- 您所在的裝置至少執行 Windows 10 版本 1903, 並使用 Microsoft Edge 瀏覽器。

- 您已從系統管理員或您的組織收到實體安全性金鑰。 您的安全性金鑰必須同時為 FIDO2 和 Microsoft 相容。 如果您有任何關於安全性金鑰的問題, 以及它是否相容, 請洽詢貴組織的技術支援人員。

## <a name="register-your-security-key"></a>登錄您的安全性金鑰

您必須先準備您的安全性金鑰, 才能使用 Windows 和唯一的 PIN 碼, 才能使用金鑰登入您的公司或學校帳戶。

1. 移至位於 https://myprofile.microsoft.com 的 [**我的設定檔**] 頁面, 如果您尚未這麼做, 請登入。

2. 選取 [**安全性資訊**], 選取 [**新增方法**], 然後從 [**新增方法**] 下拉式清單中選取 [**安全性金鑰**]。

    ![已選取安全性金鑰的 [新增方法] 方塊](media/security-info/security-info-security-key-add-method.png)

3. 選取 [**新增**], 然後選取您擁有的安全性金鑰類型, 也就是 [ **USB 裝置**] 或 [ **NFC 裝置**]。

    ![選擇您是否有 USB 或 NFC 類型的安全性金鑰](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 如果您不確定您擁有哪種類型的安全性金鑰, 請參閱製造商的檔。 如果您不確定製造商的相關資訊, 請洽詢貴組織的技術支援人員以取得協助。

4. 讓您的安全性金鑰可實際使用, 然後在 [**安全性金鑰**] 方塊中, 選取 **[下一步]** 。

    ![[安全性金鑰] [開始註冊] 方塊](media/security-info/security-info-security-key-start-setup.png)

    此時會出現新的方塊, 協助您設定新的登入方法。

5. 在 [**設定您的新登入方法**] 方塊中, 選取 **[下一步]** , 然後:

    - 如果您的安全性金鑰是 USB 裝置, 請將您的安全性金鑰插入裝置的 USB 埠。

    - 如果您的安全性金鑰是 NFC 裝置, 請將您的安全性金鑰帶到您的讀者。

6. 在 [ **Windows 安全性**] 方塊中, 輸入唯一的安全性金鑰 PIN, 然後選取 **[確定]** 。

    您會回到 [**設定新的登入方法**] 方塊。

7. 選取 [下一步] 。

8. 返回 [**安全性資訊**] 頁面上, 輸入您稍後會為新的安全性金鑰辨識的名稱, 然後選取 **[下一步]** 。

    ![安全性資訊頁面, 命名您的安全性金鑰](media/security-info/security-info-security-key-name.png)

    您的安全性金鑰已註冊並可供您用來登入您的公司或學校帳戶。

9. 選取 [**完成**] 以關閉 [**安全性金鑰**] 方塊。

    [**安全性資訊**] 頁面會以您的安全性金鑰資訊進行更新。

    ![[安全性資訊] 頁面, 其中顯示所有已註冊的方法](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>從您的安全性資訊刪除安全性金鑰

如果您不想再使用您的安全性金鑰, 您可以從安全性資訊中刪除金鑰。 雖然這會阻止安全性金鑰與您的工作或學校帳戶一起使用, 但安全性金鑰仍會繼續儲存您的資料和認證資訊。 若要從安全性金鑰本身刪除您的資料和認證資訊, 您必須遵循本文的[重設 Microsoft 相容的安全性金鑰](#reset-your-security-key)一節中的指示。

1. 從安全性金鑰選取要移除的 [**刪除**] 連結。

2. 從 [**刪除安全性金鑰**] 方塊中選取 **[確定]** 。

    您的安全性金鑰已刪除, 您將無法再使用它來登入您的公司或學校帳戶。

>[!Important]
>如果您不小心刪除了此安全性金鑰, 則必須使用本文的[如何註冊安全性金鑰](#register-your-security-key)一節中的指示, 再次進行註冊。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>從 Windows 設定管理您的安全性金鑰設定

您可以從**Windows [設定**] 應用程式管理安全性金鑰設定, 包括重設安全性金鑰, 以及建立新的安全性金鑰 PIN。

### <a name="reset-your-security-key"></a>重設您的安全性金鑰

如果您想要刪除儲存在實體安全性金鑰上的所有帳戶資訊, 則必須將金鑰傳回回其原廠預設值。 重設您的安全性金鑰會從金鑰中刪除所有內容, 讓您可以從頭開始。

>[!IMPORTANT]
>重設您的安全性金鑰會從金鑰中刪除所有內容, 並將其重設為原廠預設值。
>
> **將會清除所有資料和認證。**

#### <a name="to-reset-your-security-key"></a>重設您的安全性金鑰

1. 開啟 [Windows 設定] 應用程式, 選取 [**帳戶**], 選取 [登**入選項**], 選取 [**安全性金鑰**], 然後選取 [**管理**]。

2. 將您的安全性金鑰插入 USB 埠, 或透過您的 NFC 讀取器來驗證您的身分識別。

3. 根據您特定的安全性金鑰製造商, 遵循螢幕上的指示。 如果您的金鑰製造商未列在螢幕上的指示中, 請參閱製造商的網站以取得詳細資訊。

4. 選取 [**關閉**] 以關閉 [**管理**] 畫面。

### <a name="create-a-new-security-key-pin"></a>建立新的安全性金鑰 PIN

您可以為您的安全性金鑰建立新的安全性金鑰 PIN。

#### <a name="to-create-a-new-security-key-pin"></a>若要建立新的安全性金鑰 PIN

1. 開啟 [Windows 設定] 應用程式, 選取 [**帳戶**], 選取 [登**入選項**], 選取 [**安全性金鑰**], 然後選取 [**管理**]。

2. 將您的安全性金鑰插入 USB 埠, 或利用 NFC 讀取器來驗證您的身分識別。5
3. 從 [**安全性金鑰 pin** ] 區域選取 [**新增**], 輸入並確認新的安全性金鑰 Pin, 然後選取 **[確定]** 。

    安全性金鑰會以新的安全性金鑰 PIN 進行更新, 以便與您的工作或學校帳戶搭配使用。 如果您決定再次變更您的 PIN, 您可以選取 [**變更**] 按鈕6。
4. 選取 [**關閉**] 以關閉 [**管理**] 畫面。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

根據您要嘗試執行的動作，您會有組織如何連絡您來驗證您身分識別的其他選項。 選項包括：

- **驗證器應用程式。** 下載並使用驗證器應用程式，以取得核准通知或隨機產生的核准代碼來進行雙步驟驗證或密碼重設。 如需如何設定和使用 Microsoft Authenticator 應用程式的逐步指示，請參閱[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)。

- **行動裝置簡訊。** 輸入您的行動裝置號碼，然後取得將用來進行雙步驟驗證或密碼重設的代碼簡訊。 如需如何透過簡訊 (SMS) 驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用簡訊 (SMS)](security-info-setup-text-msg.md)。

- **行動裝置或公司電話。** 輸入您的行動裝置號碼，然後等候來電以進行雙步驟驗證或密碼重設。 如需如何透過電話號碼驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)。

- **電子郵件地址。** 輸入您的公司或學校電子郵件地址以接收用於密碼重設的電子郵件。 此選項不適用於雙步驟驗證。 如需如何設定電子郵件的逐步指示，請參閱[將安全性資訊設定為使用電子郵件](security-info-setup-email.md)。

- **安全性問題。** 回答由您的系統管理員為組織所建立的一些安全性問題。 此選項僅適用於密碼重設，不適用雙步驟驗證。 如需如何設定安全性問題的逐步指示，請參閱[將安全性資訊設定為使用安全性問題](security-info-setup-questions.md)一文。

## <a name="next-steps"></a>後續步驟

- 如需無密碼驗證方法的詳細資訊, 請閱讀[Microsoft 的 Azure AD 開始公開預覽 FIDO2 安全性金鑰、啟用無密碼](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins)登入 blog, 或閱讀[什麼是 Microsoft Authenticator 應用程式？](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview)和[Windows Hello 總覽](https://www.microsoft.com/windows/windows-hello)文章。

- 如需[Microsoft 相容安全性金鑰](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)的詳細資訊。

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](user-help-reset-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中，取得登入問題的疑難排解秘訣和說明。
