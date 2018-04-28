---
title: Azure AD Connect：無縫單一登入 - 常見問題集 | Microsoft Docs
description: Azure Active Directory 無縫單一登入常見問題集的答案。
services: active-directory
keywords: 何謂 Azure AD Connect、安裝 Active Directory、Azure AD、SSO、單一登入的必要元件
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: ba402847d14f7de6c70b545b74d7ba8c1aaddcb0
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory 無縫單一登入：常見問題集

本文將會解決 Azure Active Directory 無縫單一登入 (無縫 SSO) 的常見問題。 請隨時回來查看新內容。

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>無縫 SSO 與哪些登入方法搭配運作？

無縫 SSO 可以與[密碼雜湊同步處理](active-directory-aadconnectsync-implement-password-hash-synchronization.md)或[傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)登入方法合併使用。 然而，此功能無法搭配 Active Directory Federation Services (ADFS) 使用。

## <a name="is-seamless-sso-a-free-feature"></a>無縫 SSO 是免費功能嗎？

無縫 SSO 是免費功能，您不需要任何付費的 Azure AD 版本即可使用。

## <a name="is-seamless-sso-available-in-the-microsoft-azure-germany-cloudhttpwwwmicrosoftdecloud-deutschland-and-the-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>[Microsoft Azure 德國雲端](http://www.microsoft.de/cloud-deutschland)和 [Microsoft Azure Government 雲端](https://azure.microsoft.com/features/gov/)是否有提供「無縫 SSO」功能？

編號 只有全球版的 Azure AD 執行個體有提供「無縫 SSO」功能。

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>哪些應用程式利用無縫 SSO 的 `domain_hint` 或 `login_hint` 參數功能？

以下是一份不完整的應用程式清單，這些應用程式會將這些參數傳送給 Azure AD，因此可使用「無縫 SSO」(亦即，使用者不需要輸入自己的使用者名稱) 為使用者提供無訊息的登入體驗：

| 應用程式名稱 | 要使用的應用程式 URL |
| -- | -- |
| 存取面板 | myapps.microsoft.com/contoso.com |
| 網路版 Outlook | outlook.office365.com/contoso.com |

此外，如果應用程式將登入要求傳送至 Azure AD 的租用戶端點 (也就是 https://login.microsoftonline.com/contoso.com/<..> 或 https://login.microsoftonline.com/<tenant_ID>/<..>) 而不是 Azure AD 的一般端點 (也就是 https://login.microsoftonline.com/common/<...>)，使用者也可獲得無訊息登入體驗。 以下是一份不完整的應用程式清單，列出會提出這類登入要求的應用程式。

| 應用程式名稱 | 要使用的應用程式 URL |
| -- | -- |
| SharePoint Online | contoso.sharepoint.com |
| Azure 入口網站 | portal.azure.com/contoso.com |

在上表中，請以您的網域名稱取代 "contoso.com"，以連至您租用戶的正確應用程式 URL。

如果想要將無訊息登入使用在其他應用程式上，請以意見反應區段告知。

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>無縫 SSO 支援 `Alternate ID` 作為使用者名稱，而不是 `userPrincipalName`？

是。 如[這裡](active-directory-aadconnect-get-started-custom.md)所述設定於 Azure AD Connect 時，無縫 SSO 支援 `Alternate ID` 作為使用者名稱。 並非所有 Office 365 應用程式都支援 `Alternate ID`。 請參閱支援陳述式的特定應用程式文件。

## <a name="what-is-the-difference-between-the-single-sign-on-experience-provided-by-azure-ad-joinactive-directory-azureadjoin-overviewmd-and-seamless-sso"></a>[Azure AD 聯結](../active-directory-azureadjoin-overview.md)與「無縫 SSO」所提供的單一登入體驗有何差異？

[Azure AD 聯結](../active-directory-azureadjoin-overview.md)可為裝置已向 Azure AD 註冊的使用者提供 SSO。 這些裝置不一定要加入網域。 提供 SSO 時，使用的是「主要重新整理權杖」(或稱 *PRT*)，而不是 Kerberos。 在 Windows 10 裝置上可獲得最佳使用者體驗。 SSO 在 Edge 瀏覽器上會自動執行。 在 Chrome 上則藉由使用瀏覽器擴充功能也能運作。

您可以在租用戶上同時使用「Azure AD 聯結」和「無縫 SSO」。 這兩個功能是互補的。 如果同時開啟這兩個功能，則來自「Azure AD 聯結」的 SSO 優先順序會高於「無縫 SSO」。

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>我想要使用 Azure AD 註冊非 Windows 10 裝置，而不需要使用 AD FS。 我可以改為使用無縫 SSO 嗎？

是，此案例需要 2.1 版或更新版本的[加入工作場所用戶端](https://www.microsoft.com/download/details.aspx?id=53554)。

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>如何才能變換 `AZUREADSSOACC` 電腦帳戶的 Kerberos 解密金鑰？

請務必經常變換在內部部署 AD 樹系中建立之 `AZUREADSSOACC` 電腦帳戶 (這表示 Azure AD) 的 Kerberos 解密金鑰。

>[!IMPORTANT]
>強烈建議您至少每隔 30 天變換一次 Kerberos 解密金鑰。

請在執行 Azure AD Connect 的內部部署伺服器上依照下列步驟操作：

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>步驟 1. 取得已啟用無縫 SSO 的 AD 樹系清單

1. 首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 接著下載並安裝 [適用於 Windows PowerShell 的 64 位元 Azure Active Directory 模組](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)。
3. 瀏覽到 `%programfiles%\Microsoft Azure Active Directory Connect` 資料夾。
4. 使用此命令匯入順暢 SSO PowerShell 模組：`Import-Module .\AzureADSSO.psd1`。
5. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 此命令應提供一個快顯視窗，以便輸入租用戶的全域管理員認證。
6. 呼叫 `Get-AzureADSSOStatus`。 此命令會提供已啟用這項功能的 AD 樹系清單 (查看 [網域] 清單)。

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>步驟 2. 在已設定 Kerberos 解密金鑰的每個 AD 樹系上更新該金鑰

1. 呼叫 `$creds = Get-Credential`。 出現提示時，輸入預定 Azure AD 樹系的網域系統管理員認證。
2. 呼叫 `Update-AzureADSSOForest -OnPremCredentials $creds`。 此命令會更新此特定 AD 樹系中 `AZUREADSSOACC` 電腦帳戶的 Kerberos 解密金鑰，並且在 Azure AD 中更新它。
3. 針對您已設定此功能的每個 AD 樹系，重複上述步驟。

>[!IMPORTANT]
>確定您「並未」執行 `Update-AzureADSSOForest` 命令一次以上。 否則，此功能會停止運作，直到使用者的 Kerberos 票證過期並由您的內部部署 Active Directory 重新發出為止。

## <a name="how-can-i-disable-seamless-sso"></a>如何停用無縫 SSO？

您可以使用 Azure AD Connect 停用無縫 SSO。

執行 Azure AD Connect，選擇 [變更使用者登入頁面]，然後按一下 [下一步]。 然後取消選取 [啟用單一登入] 選項。 繼續執行精靈。 完成精靈之後，無縫 SSO 就會在您的租用戶上停用。

但是，您會在畫面上看到一個包含以下內容的訊息：

「單一登入現已停用，但還要執行其他手動步驟才可完成清理。 深入了解」

若要完成此程序，請在執行 Azure AD Connect 的內部部署伺服器上依照下列手動步驟操作：

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>步驟 1. 取得已啟用無縫 SSO 的 AD 樹系清單

1. 首先，下載並安裝 [Microsoft Online Services 登入小幫手](http://go.microsoft.com/fwlink/?LinkID=286152)。
2. 接著下載並安裝 [適用於 Windows PowerShell 的 64 位元 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297)。
3. 瀏覽到 `%programfiles%\Microsoft Azure Active Directory Connect` 資料夾。
4. 使用此命令匯入順暢 SSO PowerShell 模組：`Import-Module .\AzureADSSO.psd1`。
5. 以系統管理員身分執行 PowerShell。 在 PowerShell 中，呼叫 `New-AzureADSSOAuthenticationContext`。 此命令應提供一個快顯視窗，以便輸入租用戶的全域管理員認證。
6. 呼叫 `Get-AzureADSSOStatus`。 此命令會提供已啟用這項功能的 AD 樹系清單 (查看 [網域] 清單)。

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>步驟 2. 從您看到的每個列出 AD 樹系，手動刪除 `AZUREADSSOACCT` 電腦帳戶。

## <a name="next-steps"></a>後續步驟

- [**快速入門**](active-directory-aadconnect-sso-quick-start.md) - 開始使用 Azure AD 無縫 SSO。
- [**技術性深入探討**](active-directory-aadconnect-sso-how-it-works.md) - 了解這項功能的運作方式。
- [**疑難排解**](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解決此功能的常見問題。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。
